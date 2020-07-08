---
title: 從傳統虛擬網路遷移 Azure AD Domain Services |Microsoft Docs
description: 瞭解如何將現有的 Azure AD Domain Services 受控網域從傳統虛擬網路模型遷移至 Resource Manager 型虛擬網路。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 35f92afea9f9e8da3cf1eeefa81cac0cb712843a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84734617"
---
# <a name="migrate-azure-active-directory-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>將 Azure Active Directory Domain Services 從傳統虛擬網路模型遷移至 Resource Manager

Azure Active Directory Domain Services （Azure AD DS）針對目前使用傳統虛擬網路模型的客戶，支援一次移動至 Resource Manager 虛擬網路模型。 使用 Resource Manager 部署模型 Azure AD DS 受控網域提供額外的功能，例如更細緻的密碼原則、audit 記錄和帳戶鎖定保護。

本文概述遷移的考慮，然後是成功遷移現有受控網域所需的步驟。 如需一些優點，請參閱[在 AZURE AD DS 中從傳統部署模型遷移至 Resource Manager 部署模型的優點][migration-benefits]。

> [!NOTE]
> 在2017中，Azure AD Domain Services 變成可供 Azure Resource Manager 網路中的主機使用。 之後，我們就可以使用 Azure Resource Manager 的現代化功能來建立更安全的服務。 由於 Azure Resource Manager 部署完全取代傳統部署，Azure AD DS 傳統虛擬網路部署將于2023年3月1日淘汰。
>
> 如需詳細資訊，請參閱[官方淘汰通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)。

## <a name="overview-of-the-migration-process"></a>移轉程序概觀

遷移程式會使用在傳統虛擬網路中執行的現有受控網域，並將其移至現有的 Resource Manager 虛擬網路。 遷移是使用 PowerShell 來執行，而且有兩個主要的執行階段：*準備*和*遷移*。

![Azure AD DS 的遷移程式總覽](media/migrate-from-classic-vnet/migration-overview.png)

在*準備*階段中，Azure AD DS 會建立網域的備份，以取得同步處理至受控網域的最新使用者、群組和密碼快照集。 接著會停用同步處理，並刪除裝載受控網域的雲端服務。 在準備階段期間，受控網域無法驗證使用者。

![遷移 Azure AD DS 的準備階段](media/migrate-from-classic-vnet/migration-preparation.png)

在 [*遷移*] 階段中，會複製來自傳統受控網域的網域控制站基礎虛擬磁片，以使用 Resource Manager 部署模型建立 vm。 接著會重新建立受控網域，其中包括 LDAPS 和 DNS 設定。 Azure AD 的同步處理會重新開機，並還原 LDAP 憑證。 不需要將任何機器重新加入受控網域，它們會繼續加入受控網域，並在不變更的情況下執行。

![Azure AD DS 的遷移](media/migrate-from-classic-vnet/migration-process.png)

## <a name="example-scenarios-for-migration"></a>遷移的範例案例

遷移受控網域的一些常見案例包括下列範例。

> [!NOTE]
> 請不要轉換傳統虛擬網路，直到您確認已成功遷移為止。 轉換虛擬網路會移除復原或還原受控網域的選項（如果在遷移和驗證階段期間發生任何問題）。

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>將 Azure AD DS 遷移至現有的 Resource Manager 虛擬網路（建議）

常見的案例是您已經將其他現有的傳統資源移至 Resource Manager 部署模型與虛擬網路。 對等互連會從 Resource Manager 虛擬網路，用來繼續在 Azure AD DS 中執行的傳統虛擬網路。 這種方法可讓 Resource Manager 應用程式和服務在傳統虛擬網路中使用受控網域的驗證和管理功能。 遷移之後，所有資源都會使用 Resource Manager 部署模型和虛擬網路來執行。

![將 Azure AD DS 遷移至現有的 Resource Manager 虛擬網路](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

此範例遷移案例所牽涉到的高階步驟包括下列部分：

1. 移除在傳統虛擬網路上設定的現有 VPN 閘道或虛擬網路對等互連。
1. 使用這篇文章中所述的步驟來遷移受控網域。
1. 測試並確認遷移成功，然後刪除傳統虛擬網路。

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>遷移多個資源，包括 Azure AD DS

在此範例案例中，您會將 Azure AD DS 和其他相關資源從傳統部署模型遷移至 Resource Manager 部署模型。 如果某些資源繼續在傳統虛擬網路中與受控網域一起執行，則可以從遷移到 Resource Manager 部署模型中獲益。

![將多個資源遷移至 Resource Manager 部署模型](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

此範例遷移案例所牽涉到的高階步驟包括下列部分：

1. 移除在傳統虛擬網路上設定的現有 VPN 閘道或虛擬網路對等互連。
1. 使用這篇文章中所述的步驟來遷移受控網域。
1. 設定傳統虛擬網路與 Resource Manager 網路之間的虛擬網路對等互連。
1. 測試並確認成功的遷移。
1. [移動其他傳統資源（例如 vm][migrate-iaas]）。

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>遷移 Azure AD DS，但保留傳統虛擬網路上的其他資源

在此範例案例中，您在一個會話中的停機時間最少。 您只會將 Azure AD DS 遷移至 Resource Manager 的虛擬網路，並將現有的資源保留在傳統部署模型和虛擬網路上。 在下列維護期間，您可以視需要從傳統部署模型和虛擬網路遷移其他資源。

![只將 Azure AD DS 遷移至 Resource Manager 部署模型](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

此範例遷移案例所牽涉到的高階步驟包括下列部分：

1. 移除在傳統虛擬網路上設定的現有 VPN 閘道或虛擬網路對等互連。
1. 使用這篇文章中所述的步驟來遷移受控網域。
1. 設定傳統虛擬網路與新 Resource Manager 虛擬網路之間的虛擬網路對等互連。
1. 稍後，視需要從傳統虛擬網路[遷移其他資源][migrate-iaas]。

## <a name="before-you-begin"></a>開始之前

當您準備然後遷移受控網域時，會有一些關於驗證和管理服務可用性的考慮。 受控網域在遷移期間無法使用一段時間。 依賴 Azure AD DS 在遷移期間停機的應用程式和服務。

> [!IMPORTANT]
> 開始進行遷移程式之前，請先閱讀這篇所有的遷移文章和指導方針。 遷移程式會影響 Azure AD DS 網域控制站在一段時間內的可用性。 使用者、服務和應用程式在遷移過程中無法針對受控網域進行驗證。

### <a name="ip-addresses"></a>IP 位址

受控網域的網域控制站 IP 位址會在遷移後變更。 這種變更包括安全 LDAP 端點的公用 IP 位址。 新的 IP 位址會在 Resource Manager 虛擬網路中新子網的位址範圍內。

在復原的情況下，IP 位址可能會在回復後變更。

Azure AD DS 通常會使用位址範圍中前兩個可用的 IP 位址，但這並不保證。 您目前無法指定要在遷移後使用的 IP 位址。

### <a name="downtime"></a>停機

遷移程式牽涉到網域控制站已離線一段時間。 Azure AD DS 遷移至 Resource Manager 部署模型和虛擬網路時，無法存取網域控制站。 平均來說，停機時間大約為1到3小時。 這段期間是從網域控制站離線到第一個網域控制站恢復上線的時間。 此平均值不包含第二個網域控制站複寫所需的時間，或將其他資源遷移至 Resource Manager 部署模型所花費的時間。

### <a name="account-lockout"></a>帳戶鎖定

在傳統虛擬網路上執行的受控網域不會有 AD 帳戶鎖定原則。 如果 Vm 已公開到網際網路，攻擊者就可以使用密碼噴灑方法來強制執行帳戶。 沒有帳戶鎖定原則可停止這些嘗試。 對於使用 Resource Manager 部署模型和虛擬網路的受控網域，AD 帳戶鎖定原則可防範這些密碼噴灑攻擊。

根據預設，在2分鐘內，5次不正確的密碼嘗試會鎖定帳戶30分鐘。

已鎖定的帳戶無法用來登入，這可能會干擾管理受管理的網域或帳戶所管理之應用程式的能力。 在遷移受控網域之後，帳戶可能會因為重複嘗試登入失敗，而遇到類似永久鎖定的情況。 遷移後的兩個常見案例包括下列各項：

* 使用過期密碼的服務帳戶。
    * 服務帳戶會重複嘗試使用過期的密碼登入，這會鎖定帳戶。 若要修正此問題，請找出認證過期的應用程式或 VM，並更新密碼。
* 惡意實體使用暴力密碼破解嘗試登入帳戶。
    * 當 Vm 公開至網際網路時，攻擊者通常會在嘗試簽署時嘗試一般使用者名稱和密碼組合。 這些重複的失敗登入嘗試可能會鎖定帳戶。 不建議使用具有一般名稱（例如*admin*或*administrator*）的系統管理員帳戶，以將管理帳戶的鎖定降至最低。
    * 將公開給網際網路的 Vm 數目降至最低。 您可以使用[Azure][azure-bastion]防禦安全地連線到使用 Azure 入口網站的 vm。

如果您懷疑某些帳戶可能會在遷移之後遭到鎖定，最後的遷移步驟會概述如何啟用審核或變更更細緻的密碼原則設定。

### <a name="roll-back-and-restore"></a>復原和還原

如果遷移不成功，則會有復原或還原受控網域的程式。 Rollback 是一個自助式選項，可在嘗試遷移之前立即將受控網域的狀態傳回給。 Azure 支援工程師也可以將受控網域從備份還原為最後手段。 如需詳細資訊，請參閱[如何從失敗的遷移復原或還原](#roll-back-and-restore-from-migration)。

### <a name="restrictions-on-available-virtual-networks"></a>可用虛擬網路的限制

受控網域可以遷移到的虛擬網路上有一些限制。 目的地 Resource Manager 虛擬網路必須符合下列需求：

* Resource Manager 虛擬網路必須位於與目前部署 Azure AD DS 的傳統虛擬網路相同的 Azure 訂用帳戶中。
* Resource Manager 虛擬網路必須位於與目前部署 Azure AD DS 的傳統虛擬網路相同的區域中。
* Resource Manager 虛擬網路的子網應至少有3-5 個可用的 IP 位址。
* Resource Manager 虛擬網路的子網應為 Azure AD DS 專用的子網，且不應裝載任何其他工作負載。

如需虛擬網路需求的詳細資訊，請參閱[虛擬網路設計考慮和設定選項][network-considerations]。

## <a name="migration-steps"></a>移轉步驟

遷移至 Resource Manager 部署模型和虛擬網路會分成5個主要步驟：

| 步驟    | 執行  | 預估時間  | 停機  | 要復原/還原嗎？ |
|---------|--------------------|-----------------|-----------|-------------------|
| [步驟 1-更新並尋找新的虛擬網路](#update-and-verify-virtual-network-settings) | Azure 入口網站 | 15 分鐘 | 不需要停機 | N/A |
| [步驟 2-準備受控網域以進行遷移](#prepare-the-managed-domain-for-migration) | PowerShell | 平均 15-30 分鐘 | 完成此命令之後，Azure AD DS 的停機時間就會啟動。 | 復原和還原可用。 |
| [步驟 3-將受控網域移至現有的虛擬網路](#migrate-the-managed-domain) | PowerShell | 平均 1-3 小時 | 當此命令完成後，就會有一個網域控制站可供使用，停機時間結束。 | 失敗時，可以使用復原（自助式）和還原。 |
| [步驟 4-測試並等候複本網域控制站](#test-and-verify-connectivity-after-the-migration)| PowerShell 和 Azure 入口網站 | 1小時以上，視測試數目而定 | 這兩個網域控制站都可以使用，而且應該正常運作。 | N/A。 成功遷移第一個 VM 之後，就不會有復原或還原的選項。 |
| [步驟 5-選擇性設定步驟](#optional-post-migration-configuration-steps) | Azure 入口網站和 Vm | N/A | 不需要停機 | N/A |

> [!IMPORTANT]
> 若要避免額外的停機時間，請在開始進行遷移程式之前，閱讀所有這項遷移文章和指導方針。 遷移程式會在一段時間內影響 Azure AD DS 網域控制站的可用性。 使用者、服務和應用程式在遷移過程中無法針對受控網域進行驗證。

## <a name="update-and-verify-virtual-network-settings"></a>更新並確認虛擬網路設定

開始進行遷移程式之前，請先完成下列初始檢查和更新。 這些步驟可能會在遷移前的任何時間發生，而不會影響受控網域的操作。

1. 將您的本機 Azure PowerShell 環境更新為最新版本。 若要完成遷移步驟，您至少需要版本*2.3.2*。

    如需如何檢查和更新 PowerShell 版本的相關資訊，請參閱[Azure PowerShell 總覽][azure-powershell]。

1. 建立或選擇現有的 Resource Manager 虛擬網路。

    請確定網路設定不會封鎖 Azure AD DS 所需的必要端口。 傳統虛擬網路和 Resource Manager 虛擬網路都必須開啟埠。 這些設定包括路由表（但不建議使用路由表）和網路安全性群組。

    若要查看所需的埠，請參閱[網路安全性群組和所需的埠][network-ports]。 若要將網路通訊問題降至最低，建議您在成功完成遷移之後，等候並將網路安全性群組或路由表套用至 Resource Manager 的虛擬網路。

    記下此目標資源群組、目標虛擬網路和目標虛擬網路子網。 這些資源名稱會在遷移過程中使用。

1. 檢查 Azure 入口網站中的受控網域健全狀況。 如果您有受控網域的任何警示，請在開始進行遷移程式之前加以解決。
1. 或者，如果您打算將其他資源移到 Resource Manager 部署模型和虛擬網路，請確認可以遷移這些資源。 如需詳細資訊，請參閱[平臺支援將 IaaS 資源從傳統遷移至 Resource Manager][migrate-iaas]。

    > [!NOTE]
    > 請勿將傳統虛擬網路轉換為 Resource Manager 虛擬網路。 如果您這樣做，則沒有復原或還原受控網域的選項。

## <a name="prepare-the-managed-domain-for-migration"></a>準備受控網域以進行遷移

Azure PowerShell 可用來準備受控網域以進行遷移。 這些步驟包括取得備份、暫停同步處理，以及刪除裝載 Azure AD DS 的雲端服務。 當此步驟完成時，Azure AD DS 會離線一段時間。 如果準備步驟失敗，您可以[回復為先前的狀態](#roll-back)。

若要準備受控網域以進行遷移，請完成下列步驟：

1. `Migrate-Aaads`從[PowerShell 資源庫][powershell-script]安裝腳本。 此 PowerShell 遷移腳本是由 Azure AD 工程小組以數位方式簽署。

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. 建立一個變數，以使用[取得認證][get-credential]Cmdlet 來保存遷移腳本的認證。

    您指定的使用者帳戶需要 Azure AD 租使用者中的*全域系統管理員*許可權，才能在您的 Azure 訂用帳戶中啟用 Azure AD DS 和*參與者*許可權，以建立所需的 Azure AD DS 資源。

    出現提示時，輸入適當的使用者帳戶和密碼：

    ```powershell
    $creds = Get-Credential
    ```

1. 現在，請 `Migrate-Aadds` 使用 *-Prepare*參數執行 Cmdlet。 為您自己的受控網域提供 *-ManagedDomainFqdn* ，例如*aaddscontoso.com*：

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>遷移受控網域

準備好並備份受控網域之後，就可以遷移網域。 此步驟會使用 Resource Manager 部署模型來重新建立 Azure AD Domain Services 網域控制站 Vm。 此步驟可能需要1到3小時的時間才能完成。

`Migrate-Aadds`使用 *-Commit*參數執行 Cmdlet。 為您自己在上一節中準備好的受控網域提供 *-ManagedDomainFqdn* ，例如*aaddscontoso.com*：

指定包含您想要 Azure AD DS 遷移至之虛擬網路的目標資源群組，例如*myResourceGroup*。 提供目標虛擬網路（例如*myVnet*）和子網，例如*DomainServices*。

執行此命令之後，您就無法復原：

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

在腳本驗證受控網域準備好進行遷移之後，請輸入*Y*開始進行遷移程式。

> [!IMPORTANT]
> 在遷移過程中，請勿將傳統虛擬網路轉換為 Resource Manager 虛擬網路。 如果您轉換虛擬網路，則無法復原或還原受控網域，因為原始虛擬網路將不再存在。

在遷移程式期間每兩分鐘，進度指示器都會報告目前的狀態，如下列範例輸出所示：

![Azure AD DS 遷移的進度指示器](media/migrate-from-classic-vnet/powershell-migration-status.png)

即使您關閉 PowerShell 腳本，遷移程式仍會繼續執行。 在 Azure 入口網站中，受控網域的狀態會報告為 [正在*遷移*]。

當遷移成功完成時，您可以在 Azure 入口網站中或透過 Azure PowerShell，來查看第一個網域控制站的 IP 位址。 此外，也會顯示第二個可用網域控制站上的預估時間。

在這個階段，您可以選擇性地從傳統部署模型和虛擬網路中移動其他現有的資源。 或者，您可以在 Azure AD DS 遷移完成之後，將資源保留在傳統部署模型上，並將虛擬網路對等互連。

## <a name="test-and-verify-connectivity-after-the-migration"></a>在遷移後測試並驗證連線能力

第二個網域控制站可能需要一些時間才能成功部署，並可在受控網域中使用。

使用 Resource Manager 部署模型，受控網域的網路資源會顯示在 Azure 入口網站或 Azure PowerShell 中。 若要深入瞭解這些網路資源的功能和用途，請參閱[AZURE AD DS 使用的網路資源][network-resources]。

當至少有一個網域控制站可供使用時，請完成下列設定步驟以使用 Vm 的網路連線能力：

* **更新 DNS 伺服器設定**若要讓 Resource Manager 虛擬網路上的其他資源解析並使用受控網域，請使用新網域控制站的 IP 位址來更新 DNS 設定。 Azure 入口網站可以自動為您設定這些設定。 若要深入瞭解如何設定 Resource Manager 虛擬網路，請參閱[更新 Azure 虛擬網路的 DNS 設定][update-dns]。
* **重新開機已加入網域的 vm** -當 Azure AD DS 網域控制站的 DNS 伺服器 IP 位址變更時，重新開機任何已加入網域的 vm，然後再使用新的 DNS 伺服器設定。 如果應用程式或 Vm 已手動設定 DNS 設定，請使用 Azure 入口網站中顯示之網域控制站的新 DNS 伺服器 IP 位址，以手動方式更新它們。

現在，請測試虛擬網路連線和名稱解析。 在連線到 Resource Manager 虛擬網路的 VM 上，或對等互連至它時，嘗試下列網路通訊測試：

1. 檢查是否可以 ping 其中一個網域控制站的 IP 位址，例如`ping 10.1.0.4`
    * 網域控制站的 IP 位址會顯示在 [Azure 入口網站中受控**網域的 [內容] 頁面上**。
1. 確認受控網域的名稱解析，例如`nslookup aaddscontoso.com`
    * 指定您自己受控網域的 DNS 名稱，以確認 DNS 設定是否正確並加以解析。

第二個網域控制站在遷移 Cmdlet 完成後應可供使用1-2 小時。 若要檢查第二個網域控制站是否可用，請查看 Azure 入口網站中受控網域的 [內容 **] 頁面。** 如果顯示兩個 IP 位址，則第二個網域控制站已就緒。

## <a name="optional-post-migration-configuration-steps"></a>選擇性的遷移後設定步驟

成功完成遷移程式後，某些選擇性的設定步驟包括啟用審核記錄或電子郵件通知，或更新更細緻的密碼原則。

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>使用 Azure 監視器訂閱 audit 記錄

Azure AD DS 會公開 audit 記錄檔，以協助疑難排解及查看網域控制站上的事件。 如需詳細資訊，請參閱[啟用和使用 audit logs][security-audits]。

您可以使用範本來監視記錄檔中所公開的重要資訊。 例如，「審核記錄」活頁簿範本可以監視受控網域上可能的帳戶鎖定。

### <a name="configure-azure-ad-domain-services-email-notifications"></a>設定 Azure AD Domain Services 的電子郵件通知

若要在受控網域上偵測到問題時收到通知，請更新 Azure 入口網站中的電子郵件通知設定。 如需詳細資訊，請參閱[設定通知設定][notifications]。

### <a name="update-fine-grained-password-policy"></a>更新更細緻的密碼原則

如有需要，您可以將更細緻的密碼原則更新為低於預設設定的限制。 您可以使用 audit 記錄來判斷較不嚴格的設定是否合理，然後視需要設定原則。 使用下列高階步驟來審查和更新在遷移後重複鎖定之帳戶的原則設定：

1. 針對受控網域的限制設定較少的[密碼原則][password-policy]，並觀察 audit 記錄檔中的事件。
1. 如果有任何服務帳戶使用在 audit 記錄中識別出的過期密碼，請使用正確的密碼更新這些帳戶。
1. 如果 VM 已公開至網際網路，請檢查一般帳戶名稱（例如*系統管理員*、*使用者*或*來賓*），其具有高登入嘗試。 可能的話，請更新這些 Vm，以使用較不常用的命名帳戶。
1. 使用 VM 上的網路追蹤來找出攻擊的來源，並封鎖這些 IP 位址以嘗試登入。
1. 當發生最少的鎖定問題時，請視需要將更細緻的密碼原則更新為嚴格。

### <a name="creating-a-network-security-group"></a>建立網路安全性群組

Azure AD DS 需要一個網路安全性群組來保護受控網域所需的埠，並封鎖所有其他的連入流量。 此網路安全性群組可作為額外的保護層，以鎖定受控網域的存取權，且不會自動建立。 若要建立網路安全性群組並開啟所需的埠，請參閱下列步驟：

1. 在 [Azure 入口網站中，選取您的 Azure AD DS 資源。 在 [總覽] 頁面上，如果沒有與 Azure AD Domain Services 相關聯的 [網路安全性群組]，則會顯示按鈕來建立。
1. 如果您使用安全 LDAP，請將規則新增至網路安全性群組，以允許*TCP*埠*636*的連入流量。 如需詳細資訊，請參閱[設定安全 LDAP][secure-ldap]。

## <a name="roll-back-and-restore-from-migration"></a>復原並從遷移還原

在遷移程式中的特定時間點，您可以選擇復原或還原受控網域。

### <a name="roll-back"></a>復原

當您執行 PowerShell Cmdlet 以準備在步驟2中進行遷移或在步驟3中自行遷移時，如果發生錯誤，受控網域可以回復為原始設定。 此復原需要原始的傳統虛擬網路。 請注意，回復之後，IP 位址可能仍會變更。

`Migrate-Aadds`使用 *-Abort*參數執行 Cmdlet。 為您自己在上一節中準備好的受控網域（例如*aaddscontoso.com*）和傳統虛擬網路名稱（例如*myClassicVnet*）提供 *-ManagedDomainFqdn* ：

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>還原

最後一種辦法是從最後一個可用的備份還原 Azure AD Domain Services。 在遷移的步驟1中進行備份，以確定可以使用最新的備份。 此備份會儲存30天。

若要從備份還原受控網域，請[使用 Azure 入口網站開啟支援案例票證][azure-support]。 提供您的目錄識別碼、功能變數名稱和還原的原因。 支援和還原程式可能需要數天的時間才能完成。

## <a name="troubleshooting"></a>疑難排解

如果您在遷移至 Resource Manager 部署模型之後遇到問題，請參閱下列一些常見的疑難排解區域：

* [針對網域聯結問題進行疑難排解][troubleshoot-domain-join]
* [針對帳戶鎖定問題進行疑難排解][troubleshoot-account-lockout]
* [對帳戶登入問題進行疑難排解][troubleshoot-sign-in]
* [針對安全 LDAP 連線問題進行疑難排解][tshoot-ldaps]

## <a name="next-steps"></a>後續步驟

當您的受控網域遷移至 Resource Manager 部署模型時，[建立 WINDOWS VM 並加入網域][join-windows]，然後[安裝管理工具][tutorial-create-management-vm]。

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential
[migration-benefits]: concepts-migration-benefits.md

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
