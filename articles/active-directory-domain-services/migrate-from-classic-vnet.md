---
title: 從經典虛擬網路遷移 Azure AD 域服務 |微軟文件
description: 瞭解如何將現有的 Azure AD 網域服務託管域實例從經典虛擬網路模型遷移到基於資源管理器的虛擬網路。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 6acf9301367ae2c6947f6935c43f420d3d7cac65
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655027"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>將 Azure AD 網域服務從傳統虛擬網路模型移轉到資源管理員

Azure 活動目錄域服務 (AD DS) 支援目前使用經典虛擬網路模型的客戶一次性移動到資源管理器虛擬網路模型。 使用資源管理員部署模型的 Azure AD DS 託管域提供了其他功能,如細粒度密碼策略、審核日誌和帳戶鎖定保護。

本文概述了遷移的好處和注意事項,以及成功遷移現有 Azure AD DS 實例所需的步驟。

> [!NOTE]
> 2017 年,Azure AD 網域服務可用於在 Azure 資源管理器網路中託管。 自那時以來,我們一直能夠使用 Azure 資源管理器的現代功能構建更安全的服務。 由於 Azure 資源管理器部署完全取代了經典部署,因此 Azure AD DS 經典虛擬網路部署將於 2023 年 3 月 1 日停用。
>
> 有關詳細資訊,請參閱[官方棄用通知](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="overview-of-the-migration-process"></a>移轉程序概觀

遷移過程採用在經典虛擬網路中運行的現有 Azure AD DS 實例,並將其移動到現有的資源管理器虛擬網路。 移動使用 PowerShell 執行,並且有兩個主要執行階段 -*準備*與*移轉*。

![Azure AD DS 的移轉程序概述](media/migrate-from-classic-vnet/migration-overview.png)

在*準備*階段,Azure AD DS 會備份域,以獲得與託管域同步的使用者、組和密碼的最新快照。 然後禁用同步,並刪除承載 Azure AD DS 託管域的雲端服務。 在準備階段,Azure AD DS 託管域無法對使用者進行身份驗證。

![移轉 Azure AD DS 的準備階段](media/migrate-from-classic-vnet/migration-preparation.png)

在*遷移*階段,將複製經典 Azure AD DS 託管域中域控制器的基礎虛擬磁碟,以便使用資源管理器部署模型創建 VM。 然後重新創建 Azure AD DS 託管域,其中包括 LDAPS 和 DNS 配置。 重新啟動與 Azure AD 的同步,並還原 LDAP 證書。 無需將任何電腦重新加入 Azure AD DS 託管域 - 它們繼續加入託管域,並在不發生更改的情況下運行。

![Azure AD DS 的移轉](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>遷移好處

使用此遷移過程移動 Azure AD DS 託管域時,無需將電腦重新加入託管域或刪除 Azure AD DS 實例並從頭開始創建。 在遷移過程結束時,VM將繼續加入 Azure AD DS 託管域。

移轉後,Azure AD DS 提供了許多功能,這些功能僅適用於使用資源管理器虛擬網路的域,例如:

* 細粒度密碼策略支援。
* AD 帳戶鎖定保護。
* 通過電子郵件發送 Azure AD DS 託管域上的警報通知。
* 使用 Azure 監視器審核日誌。
* Azure 檔案整合
* 高清洞察集成

使用資源管理員虛擬網路的 Azure AD DS 託管域可説明您瞭解最新的新功能。 將來將棄用使用經典虛擬網路對 Azure AD DS 的支援。

## <a name="example-scenarios-for-migration"></a>遷移範例方案

遷移 Azure AD DS 託管域的一些常見方案包括以下範例。

> [!NOTE]
> 在確認遷移成功之前,不要轉換經典虛擬網路。 轉換虛擬網路將刪除在遷移和驗證階段出現任何問題時回滾或還原 Azure AD DS 託管域的選項。

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>將 Azure AD DS 移轉到現有資源管理員虛擬網路(建議)

常見方案是,您已經將其他現有經典資源移動到資源管理器部署模型和虛擬網路。 然後,從資源管理器虛擬網路到繼續運行 Azure AD DS 的經典虛擬網路使用對等互連。 此方法允許資源管理員應用程式和服務在經典虛擬網路中使用 Azure AD DS 託管域的身份驗證和管理功能。 遷移後,所有資源都使用資源管理器部署模型和虛擬網路運行。

![將 Azure AD DS 移至現有資源管理員虛擬網路](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

此範例移轉方案涉及的進階步驟包括以下部分:

1. 刪除經典虛擬網路上配置的現有 VPN 閘道或虛擬網路對等互連。
1. 使用本文中概述的步驟遷移 Azure AD DS 託管域。
1. 測試並確認遷移成功,然後刪除經典虛擬網路。

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>遷移多個資源,包括 Azure AD DS

在此範例中,您將 Azure AD DS 和其他關聯資源從經典部署模型遷移到資源管理器部署模型。 如果某些資源繼續與 Azure AD DS 託管域一起在經典虛擬網路中運行,則它們都可以從遷移到資源管理器部署模型中獲益。

![將多個資源移轉到資源管理員部署模型](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

此範例移轉方案涉及的進階步驟包括以下部分:

1. 刪除經典虛擬網路上配置的現有 VPN 閘道或虛擬網路對等互連。
1. 使用本文中概述的步驟遷移 Azure AD DS 託管域。
1. 在經典虛擬網路和資源管理器網路之間設置虛擬網路對等互連。
1. 測試並確認遷移成功。
1. [移動其他經典資源,如 VM。][migrate-iaas]

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>遷移 Azure AD DS,但保留傳統網路上的其他資源

使用此範例方案,您可以在一個會話中具有最少的停機時間。 您只將 Azure AD DS 移至資源管理器虛擬網路,並將現有資源保留在經典部署模型和虛擬網路上。 在隨後的維護期間,您可以根據需要從經典部署模型和虛擬網路遷移其他資源。

![只將 Azure AD DS 移至資源管理員部署模型](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

此範例移轉方案涉及的進階步驟包括以下部分:

1. 刪除經典虛擬網路上配置的現有 VPN 閘道或虛擬網路對等互連。
1. 使用本文中概述的步驟遷移 Azure AD DS 託管域。
1. 在經典虛擬網路和新的資源管理器虛擬網路之間設置虛擬網路對等。
1. 稍後,根據需要從傳統網路[移轉其他資源][migrate-iaas]。

## <a name="before-you-begin"></a>開始之前

在準備然後遷移 Azure AD DS 託管域時,有關身份驗證和管理服務可用性的一些注意事項。 Azure AD DS 託管域在遷移期間一段時間內不可用。 依賴 Azure AD DS 的應用程式和服務在遷移期間遇到停機時間。

> [!IMPORTANT]
> 在開始遷移過程之前,請閱讀所有這些遷移文章和指導。 遷移過程會影響 Azure AD DS 域控制器在一段時間內的可用性。 在遷移過程中,用戶、服務和應用程式無法針對託管域進行身份驗證。

### <a name="ip-addresses"></a>IP 位址

遷移後,Azure AD DS 託管域的域控制器 IP 位址會更改。 此更改包括安全 LDAP 終結點的公共 IP 位址。 新的 IP 位址位於資源管理器虛擬網路中新子網的地址範圍內。

在回滾的情況下,IP 位址可能會在回滾後更改。

Azure AD DS 通常使用位址範圍內的前兩個可用 IP 位址,但不保證這樣做。 當前無法指定移轉後要使用的 IP 位址。

### <a name="downtime"></a>停機

遷移過程涉及域控制器在一段時間內處於脫機狀態。 當 Azure AD DS 遷移到資源管理員部署模型和虛擬網路時,無法存取網域控制器。 平均而言,停機時間約為 1 到 3 小時。 此時間段從域控制器離線到第一個域控制器重新連線的那一刻。 此平均值不包括第二個域控制器複製所需的時間,也不包括將其他資源遷移到資源管理器部署模型所需的時間。

### <a name="account-lockout"></a>帳戶鎖定

在經典虛擬網路上運行的 Azure AD DS 託管域沒有 AD 帳戶鎖定策略。 如果 VM 被暴露在網路上,攻擊者可能會使用密碼噴射方法將病毒強制強制進入帳戶。 沒有帳戶鎖定策略來阻止這些嘗試。 對於使用資源管理員部署模型和虛擬網路的 Azure AD DS 託管域,AD 帳戶鎖定策略可防止這些密碼噴射攻擊。

默認情況下,在2分鐘內嘗試5次錯誤密碼,將帳戶鎖定30分鐘。

鎖定的帳戶不能用於登錄,這可能會干擾管理 Azure AD DS 託管域或由帳戶管理的應用程式的能力。 遷移 Azure AD DS 託管域後,由於多次嘗試登錄失敗,帳戶可能會體驗到永久鎖定的感覺。 移移後的兩種常見方案包括:

* 使用過期密碼的服務帳戶。
    * 服務帳戶反覆嘗試使用過期密碼登錄,密碼將鎖定帳戶。 要解決此問題,請找到憑據過期的應用程式或 VM 並更新密碼。
* 惡意實體使用暴力嘗試登錄到帳戶。
    * 當 VM 暴露到 Internet 時,攻擊者通常會嘗試使用通用使用者名和密碼組合進行簽名。 這些重複的失敗登錄嘗試可以鎖定帳戶。 不建議使用具有通用名稱(例如*管理員*或*管理員*)的管理員帳戶,以盡量減少將管理帳戶鎖定。
    * 盡量減少暴露到互聯網的 VM 數量。 可以使用[Azure 堡壘][azure-bastion]使用 Azure 門戶安全地連接到 VM。

如果您懷疑某些帳戶可能在遷移后鎖定,則最後遷移步驟將概述如何啟用審核或更改細粒度的密碼策略設置。

### <a name="roll-back-and-restore"></a>回復與復原

如果遷移不成功,則有回滾或還原 Azure AD DS 託管域的過程。 回滾是一種自助服務選項,用於在遷移嘗試之前立即將託管域的狀態返回到。 Azure 支援工程師還可以作為最後手段從備份還原託管域。 有關詳細資訊,請參閱[如何從失敗的遷移回滾或還原](#roll-back-and-restore-from-migration)。

### <a name="restrictions-on-available-virtual-networks"></a>可用虛擬網路的限制

Azure AD DS 託管域可以遷移到虛擬網路時存在一些限制。 目標資源管理員虛擬網路必須滿足以下要求:

* 資源管理器虛擬網路必須與 Azure AD DS 當前部署的經典虛擬網路位於同一 Azure 訂閱中。
* 資源管理器虛擬網路必須與 Azure AD DS 當前部署的經典虛擬網路位於同一區域。
* 資源管理器虛擬網路的子網應至少具有 3-5 個可用 IP 位址。
* 資源管理器虛擬網路的子網應該是 Azure AD DS 的專用子網,並且不應承載任何其他工作負荷。

有關虛擬網路要求的詳細資訊,請參閱[虛擬網路設計注意事項和設定選項][network-considerations]。

## <a name="migration-steps"></a>移轉步驟

遷移到資源管理員部署模型和虛擬網路分為 5 個主要步驟:

| 步驟    | 通過執行  | 預估時間  | 停機  | 回滾/還原? |
|---------|--------------------|-----------------|-----------|-------------------|
| [第一步 - 更新並尋找新的虛擬網路](#update-and-verify-virtual-network-settings) | Azure 入口網站 | 15 分鐘 | 無需停機 | N/A |
| [步驟 2 - 準備 Azure AD DS 託管域進行遷移](#prepare-the-managed-domain-for-migration) | PowerShell | 平均15~30分鐘 | Azure AD DS 的停機時間在此命令完成後開始。 | 回滾並還原可用。 |
| [步驟 3 - 將 Azure AD DS 託管域移至現有虛擬網路](#migrate-the-managed-domain) | PowerShell | 平均 1 ~ 3 小時 | 完成此命令後,一個域控制器可用,停機時間結束。 | 發生故障時,回滾(自助服務)和還原都可用。 |
| [步驟 4 - 測試並等待副本域控制器](#test-and-verify-connectivity-after-the-migration)| 電源外殼和 Azure 門戶 | 1 小時或更長時間,具體取決於測試次數 | 兩個域控制器都可用,應正常工作。 | N/A。 成功遷移第一個 VM 後,沒有回滾或還原的選項。 |
| [步驟 5 - 選擇設定步驟](#optional-post-migration-configuration-steps) | Azure 門戶和 VM | N/A | 無需停機 | N/A |

> [!IMPORTANT]
> 為了避免額外的停機時間,在開始遷移過程之前,請閱讀所有這些遷移文章和指導。 遷移過程會影響 Azure AD DS 域控制器在一段時間內的可用性。 在遷移過程中,用戶、服務和應用程式無法針對託管域進行身份驗證。

## <a name="update-and-verify-virtual-network-settings"></a>更新並驗證虛擬網路設定

在開始遷移過程之前,完成以下初始檢查和更新。 這些步驟可以在遷移之前的任何時間發生,並且不會影響 Azure AD DS 託管域的操作。

1. 將本地 Azure PowerShell 環境更新為最新版本。 要完成遷移步驟,至少需要版本*2.3.2*。

    有關如何檢查和更新 PowerShell 版本的資訊,請參閱 Azure [PowerShell 概述][azure-powershell]。

1. 創建或選擇現有的資源管理器虛擬網路。

    確保網路設置不會阻止 Azure AD DS 所需的必要埠。 必須在經典虛擬網路和資源管理器虛擬網路上打開埠。 這些設置包括路由表(儘管不建議使用路由表)和網路安全組。

    要檢視所需的連接埠,請參閱[網路安全群組和所需連接埠][network-ports]。 為了最小化網路通信問題,建議在遷移成功完成後等待並將網路安全組或路由表應用於資源管理器虛擬網路。

    記下此目標資源組、目標虛擬網路和目標虛擬網路子網。 這些資源名稱在遷移過程中使用。

1. 檢查 Azure 門戶中的 Azure AD DS 託管域運行狀況。 如果您有託管域的任何警報,則在開始遷移過程之前解決這些問題。
1. 或者,如果您計劃將其他資源移動到資源管理器部署模型和虛擬網路,請確認可以遷移這些資源。 有關詳細資訊,請參閱[平台支援的 IaaS 資源從傳統遷移到資源管理員][migrate-iaas]。

    > [!NOTE]
    > 不要將經典虛擬網路轉換為資源管理器虛擬網路。 如果這樣做,則沒有回滾或還原 Azure AD DS 託管域的選項。

## <a name="prepare-the-managed-domain-for-migration"></a>準備託管域進行遷移

Azure PowerShell 用於準備 Azure AD DS 託管域進行遷移。 這些步驟包括執行備份、暫停同步和刪除承載 Azure AD DS 的雲端服務。 完成此步驟後,Azure AD DS 將離線一段時間。 如果準備步驟失敗,您可以[回捲到以前的狀態](#roll-back)。

要準備 Azure AD DS 託管域進行遷移,請完成以下步驟:

1. 從`Migrate-Aaads`[PowerShell 庫][powershell-script]安裝腳本。 此 PowerShell 移腳本是由 Azure AD 工程團隊進行數位簽名的。

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. 使用[獲取憑據][get-credential]cmdlet 創建變數以保存遷移腳本的認證。

    指定的*使用者帳戶需要在*Azure AD 租戶中啟用 Azure AD DS,然後在 Azure 訂閱中*啟用參與者*許可權以創建所需的 Azure AD DS 資源。

    出現提示時,輸入相應的使用者帳戶和密碼:

    ```powershell
    $creds = Get-Credential
    ```

1. 現在使用`Migrate-Aadds`*-Prepare*參數運行 cmdlet。 為自己的 Azure AD DS 託管域提供 *-託管域Fqdn,* 例如*aaddscontoso.com*:

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>移轉託管域

通過準備和備份 Azure AD DS 託管域,可以遷移域。 此步驟使用資源管理器部署模型重新創建 Azure AD 網域服務網域控制器 VM。 此步驟可能需要 1 到 3 小時才能完成。

使用`Migrate-Aadds`*-Commit*參數運行 cmdlet。 在上一節中準備自己的 Azure AD DS 託管域提供 *-託管域Fqdn,* 例如*aaddscontoso.com*:

指定包含要將 Azure AD DS 移至目標資源群組,例如*我的資源群組*。 提供目標虛擬網路,如*myVnet*和子網路,如*網域服務*。

執行此指令後,您無法回捲:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

在腳本驗證託管域已準備好進行遷移后,輸入*Y*以啟動遷移過程。

> [!IMPORTANT]
> 在遷移過程中,不要將經典虛擬網路轉換為資源管理器虛擬網路。 如果轉換虛擬網路,則無法回滾或還原 Azure AD DS 託管域,因為原始虛擬網路將不復存在。

在遷移過程中,每兩分鐘一次,進度指示器報告當前狀態,如以下範例輸出所示:

![Azure AD DS 移轉進度指示器](media/migrate-from-classic-vnet/powershell-migration-status.png)

即使您關閉了 PowerShell 腳本,遷移過程也會繼續運行。 在 Azure 門戶中,託管域的狀態報告為*移轉*。

移轉成功完成後,您可以在 Azure 門戶或透過 Azure PowerShell 查看第一個網域控制器的 IP 位址。 還顯示了第二個可用域控制器的時間估計值。

在此階段,您可以選擇從經典部署模型和虛擬網路移動其他現有資源。 或者,您可以將資源保留在經典部署模型上,並在 Azure AD DS 遷移完成後將虛擬網路彼此對等。

## <a name="test-and-verify-connectivity-after-the-migration"></a>移轉後測試及驗證連線

第二個域控制器可能需要一些時間才能成功部署,並且可用於 Azure AD DS 託管域。

使用資源管理員部署模型,Azure AD DS 託管域的網路資源將顯示在 Azure 門戶或 Azure PowerShell 中。 要瞭解有關這些網路資源正在做什麼和做什麼的更多資訊,請參閱[Azure AD DS 使用的網路資源][network-resources]。

當至少有一個域控制器可用時,完成以下配置步驟,以便與 VM 進行網路連線:

* **更新 DNS 伺服器設定**要讓資源管理器虛擬網路上的其他資源解析並使用 Azure AD DS 託管域,請使用新域控制器的 IP 位址更新 DNS 設置。 Azure 門戶可以為您自動配置這些設置。 要瞭解有關如何設定資源管理員虛擬網路的詳細資訊,請參閱更新 Azure[虛擬網路的 DNS 設定][update-dns]。
* **重新啟動加入網域的 VM** - 當 Azure AD DS 網域控制器的 DNS 伺服器 IP 位址更改時,重新啟動任何加入網域的 VM,以便他們使用新的 DNS 伺服器設定。 如果應用程式或 VM 已手動配置 DNS 設定,請使用 Azure 門戶中顯示的網域控制器的新 DNS 伺服器 IP 位址手動更新它們。

現在測試虛擬網路連接和名稱解析。 連線到資源管理員虛擬網路或對等互連的 VM 上,請試著以下網路通訊測試:

1. 檢查是否可以 ping 其中一個網域控制器的 IP 位址,例如`ping 10.1.0.4`
    * 域控制器的 IP 位址顯示在 Azure 門戶中的 Azure AD DS 託管域**的屬性頁上。**
1. 驗證託管網域的名稱解析,例如`nslookup aaddscontoso.com`
    * 指定您自己的 Azure AD DS 託管域的 DNS 名稱,以驗證 DNS 設定是否正確並解析。

第二個域控制器應在遷移 cmdlet 完成後 1-2 小時可用。 要檢查第二個域控制器是否可用,請查看 Azure 門戶中的 Azure AD DS 託管域**的屬性**頁。 如果顯示兩個 IP 位址,則第二個域控制器已準備就緒。

## <a name="optional-post-migration-configuration-steps"></a>選擇的移轉後設定步驟

遷移過程成功完成後,某些可選配置步驟包括啟用審核日誌或電子郵件通知,或更新細粒度密碼策略。

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>使用 Azure 監視器訂閱稽核紀錄

Azure AD DS 公開審核日誌,以説明對域控制器上的事件進行故障排除和查看。 有關詳細資訊,請參閱[啟用和使用稽核紀錄][security-audits]。

您可以使用範本監視日誌中公開的重要資訊。 例如,審核日誌工作簿範本可以監視 Azure AD DS 託管域上可能的帳戶鎖定。

### <a name="configure-azure-ad-domain-services-email-notifications"></a>設定 Azure AD 網域服務電子郵件通知

要在 Azure AD DS 託管域上檢測到問題時收到通知,請更新 Azure 門戶中的電子郵件通知設置。 有關詳細資訊,請參閱[設定通知設定][notifications]。

### <a name="update-fine-grained-password-policy"></a>更新細粒度密碼原則

如果需要,可以更新細粒度密碼策略,以小於默認配置的限制。 您可以使用審核日誌來確定限制較少的設置是否有意義,然後根據需要配置策略。 使用以下進階步驟檢視和更新移移後會自動鎖定的帳戶的策略設定:

1. [設定密碼策略][password-policy],以減少對 Azure AD DS 託管域的限制,並觀察審核日誌中的事件。
1. 如果任何服務帳戶使用審核日誌中標識的過期密碼,請使用正確的密碼更新這些帳戶。
1. 如果 VM 被公開到 Internet,請檢視通用帳戶名稱,如*管理員*,*使用者*或具有高登入嘗試的*來賓*。 在可能的情況下,更新這些 VM 以使用不太通用的帳戶。
1. 使用 VM 上的網路追蹤查找攻擊源,並阻止這些 IP 位址嘗試登錄。
1. 當存在最少的鎖定問題時,根據需要更新細粒度密碼策略以進行嚴格操作。

### <a name="creating-a-network-security-group"></a>建立網路安全性群組

Azure AD DS 需要一個網路安全組來保護託管域所需的埠並阻止所有其他傳入流量。 此網路安全組充當一個額外的保護層,用於鎖定對託管域的訪問,並且不會自動創建。 要建立網路安全群組並打開所需的連接埠,請查看以下步驟:

1. 在 Azure 門戶中,選擇 Azure AD DS 資源。 在概覽頁上,如果沒有與 Azure AD 域服務關聯,則顯示一個按鈕以創建網路安全組。
1. 如果使用安全 LDAP,請向網路安全組添加規則以允許*TCP*埠*636*的傳入流量。 有關詳細資訊,請參閱[設定安全 LDAP][secure-ldap]。

## <a name="roll-back-and-restore-from-migration"></a>回滾並從遷移中還原

在遷移過程中的某個點,您可以選擇回滾或還原 Azure AD DS 託管域。

### <a name="roll-back"></a>回滾

如果在運行 PowerShell cmdlet 準備在步驟 2 中或步驟 3 中遷移本身時出現錯誤,則 Azure AD DS 託管域可以回滾到原始配置。 此回滾需要原始經典虛擬網路。 請注意,回滾後 IP 位址仍可能更改。

使用`Migrate-Aadds`*-Abort*參數運行 cmdlet。 為上一節中準備的 Azure AD DS 託管域 (如*aaddscontoso.com*) 和經典虛擬網路名稱(如*myClassicVnet*) 提供 *-託管網域( D) Sdn:*

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>還原

作為最後的手段,可以從上次可用的備份中還原 Azure AD 域服務。 遷移的步驟 1 中執行備份,以確保最新的備份可用。 此備份存儲 30 天。

要從備份還原 Azure AD DS 託管域,[請使用 Azure 門戶開啟支援案例票證][azure-support]。 提供目錄 ID、功能變數名稱和還原原因。 支持和恢復過程可能需要多天時間才能完成。

## <a name="troubleshooting"></a>疑難排解

如果在遷移到資源管理員部署模型後出現問題,請查看以下一些常見故障排除區域:

* [解決域加入問題][troubleshoot-domain-join]
* [解決帳戶鎖定問題][troubleshoot-account-lockout]
* [解決帳戶登入問題][troubleshoot-sign-in]
* [排除安全的 LDAP 連線問題][tshoot-ldaps]

## <a name="next-steps"></a>後續步驟

將 Azure AD DS 管理員移到資源管理員部署模型後,[建立與網域加入 Windows VM,][join-windows]然後[安裝管理工具][tutorial-create-management-vm]。

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

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
