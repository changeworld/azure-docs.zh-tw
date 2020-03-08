---
title: 部署內部部署 Azure AD 密碼保護
description: 瞭解如何在內部部署 Active Directory Domain Services 環境中規劃和部署 Azure AD 的密碼保護
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a977eac19128886dd3c379e200f7cb78066a06af
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671761"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>規劃和部署內部部署 Azure Active Directory 密碼保護

使用者通常會建立使用像是學校、體育小組或知名人等常見當地用語的密碼。 這些密碼很容易猜測，而且不會受到字典型攻擊的危害。 為了在您的組織中強制使用強式密碼，Azure Active Directory （Azure AD）密碼保護提供了全域和自訂的禁用密碼清單。 如果這些禁用密碼清單中有相符的結果，密碼變更要求就會失敗。

若要保護您的內部部署 Active Directory Domain Services （AD DS）環境，您可以安裝和設定 Azure AD 密碼保護，以與您的內部內部部署 DC 搭配使用。 本文說明如何在您的內部部署環境中安裝和註冊 Azure AD 密碼保護 proxy 服務和 Azure AD 密碼保護 DC 代理程式。

如需有關 Azure AD 密碼保護在內部部署環境中如何運作的詳細資訊，請參閱[如何強制執行 Windows Server 的 Azure AD 密碼保護 Active Directory](concept-password-ban-bad-on-premises.md)。

## <a name="deployment-strategy"></a>部署策略

下圖顯示 Azure AD 密碼保護的基本元件如何在內部部署 Active Directory 環境中共同作業：

![Azure AD 密碼保護元件如何一起運作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

在您部署軟體之前，最好先複習它的運作方式。 如需詳細資訊，請參閱[概念概述 Azure AD 密碼保護](concept-password-ban-bad-on-premises.md)。

我們建議您以*audit*模式啟動部署。 Audit 模式是預設的初始設定，可以繼續設定密碼。 系統會將封鎖的密碼記錄在事件記錄檔中。 在 audit 模式中部署 proxy 伺服器和 DC 代理程式之後，請監視強制執行原則時，密碼原則對使用者的影響。

在 audit 階段，許多組織會發現下列情況適用：

* 它們必須改善現有的作業程序，才能使用更安全的密碼。
* 使用者通常會使用不安全的密碼。
* 他們必須通知使用者即將進行的安全性強制變更、可能對他們造成的影響，以及如何選擇更安全的密碼。

也可以進行更強的密碼驗證，以影響您現有的 Active Directory 網域控制站部署自動化。 我們建議在審查期間評估期間，至少有一個 DC 升級和一個 DC 降級，以協助找出這類問題。 如需詳細資訊，請參閱下列文章：

* [Ntdsutil.exe 無法設定弱式目錄服務修復模式密碼](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [網域控制站複本升級因弱式目錄服務修復模式密碼而失敗](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [由於本機系統管理員密碼弱，導致網域控制站降級失敗](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

在以 audit 模式執行此功能一段合理的時間之後，您可以將設定從*audit*切換為*強制*性，以要求更安全的密碼。 這段期間的額外監視是個不錯的主意。

### <a name="multiple-forest-considerations"></a>多樹系考慮

沒有任何要在多個樹系部署 Azure AD 密碼保護的額外需求。

每個樹系都是獨立設定的，如下一節所述，[部署內部內部部署 Azure AD 密碼保護](#download-required-software)。 每個 Azure AD 的密碼保護 proxy 都只能支援其已加入樹系中的網域控制站。

不論 Active Directory 信任設定為何，任何樹系中的 Azure AD 密碼保護軟體都不會察覺部署在其他樹系中的密碼保護軟體。

### <a name="read-only-domain-controller-considerations"></a>唯讀網域控制站考慮

密碼變更或設定事件不會在唯讀網域控制站（Rodc）上處理及保存。 相反地，它們會轉送到可寫入的網域控制站。 您不需要在 Rodc 上安裝 Azure AD 密碼保護 DC 代理程式軟體。

此外，不支援在唯讀網域控制站上執行 Azure AD 密碼保護 proxy 服務。

### <a name="high-availability-considerations"></a>高可用性考慮

當樹系中的 Dc 嘗試從 Azure 下載新原則或其他資料時，密碼保護的主要考慮是 Azure AD 密碼保護 proxy 伺服器的可用性。 每個 Azure AD 密碼保護 DC 代理程式會在決定要呼叫哪一個 proxy 伺服器時，使用簡單的迴圈配置資源樣式演算法。 代理程式會略過沒有回應的 proxy 伺服器。

對於大部分已完全連線的 Active Directory 部署，其中包含目錄和 sysvol 資料夾狀態的狀況良好複寫，兩部 Azure AD 密碼保護 proxy 伺服器就足以確保可用性。 這項設定會導致及時下載新的原則和其他資料。 如有需要，您可以部署額外的 Azure AD 密碼保護 proxy 伺服器。

Azure AD 密碼保護 DC 代理程式軟體的設計可減少與高可用性相關聯的一般問題。 Azure AD 密碼保護 DC 代理程式會維護最近下載的密碼原則的本機快取。 即使所有已註冊的 proxy 伺服器都無法使用，Azure AD 密碼保護 DC 代理程式仍會繼續強制執行其快取的密碼原則。

在大型部署中，密碼原則的合理更新頻率通常是天，而不是小時或更少。 因此，暫時中斷 proxy 伺服器並不會嚴重影響 Azure AD 密碼保護。

## <a name="deployment-requirements"></a>部署需求

如需授權的詳細資訊，請參閱[Azure AD 密碼保護授權需求](concept-password-ban-bad.md#license-requirements)。

適用下列核心需求：

* 已安裝 Azure AD 密碼保護元件的所有機器（包括網域控制站）都必須安裝通用 C 執行時間。
    * 您可以藉由確定您有 Windows Update 的所有更新，來取得執行時間。 或者，您也可以在 OS 特定的更新套件中取得。 如需詳細資訊，請參閱[Windows 中的通用 C 執行時間更新](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)。
* 您需要具有樹系根域中 Active Directory 網域系統管理員許可權的帳戶，才能向 Azure AD 註冊 Windows Server Active Directory 樹系。
* 金鑰發佈服務必須在執行 Windows Server 2012 的網域中的所有網域控制站上啟用。 根據預設，會透過手動觸發程式啟動來啟用此服務。
* 每個網域中至少要有一個網域控制站，而且至少有一部裝載 proxy 服務的伺服器，才能 Azure AD 密碼保護的網路連接。 此連線能力必須允許網域控制站存取 proxy 服務上的 RPC 端點對應程式埠135和 RPC 伺服器埠。
    * 根據預設，RPC 伺服器埠是動態 RPC 埠，但可以設定為[使用靜態埠](#static)。
* 將安裝 Azure AD 密碼保護 Proxy 服務的所有電腦都必須具備下列端點的網路存取權：

    |**端點**|**目的**|
    | --- | --- |
    |`https://login.microsoftonline.com`|驗證要求|
    |`https://enterpriseregistration.windows.net`|Azure AD 密碼保護功能|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD 密碼保護 DC 代理程式

下列需求適用于 Azure AD 密碼保護 DC 代理程式：

* 將安裝 Azure AD 密碼保護 DC 代理程式軟體的所有電腦都必須執行 Windows Server 2012 或更新版本。
    * Active Directory 網域或樹系不需要位於 Windows Server 2012 網域功能等級（DFL）或樹系功能等級（FFL）。 如[設計原則](concept-password-ban-bad-on-premises.md#design-principles)中所述，執行 DC 代理程式或 proxy 軟體時，不需要最少的 DFL 或 FFL。
* 執行 Azure AD 密碼保護 DC 代理程式的所有電腦都必須安裝 .NET 4.5。
* 執行 Azure AD 密碼保護 DC 代理程式服務的任何 Active Directory 網域，都必須使用分散式檔案系統複寫（DFSR）來進行 sysvol 複寫。
   * 如果您的網域尚未使用 DFSR，您必須在安裝 Azure AD 密碼保護之前進行遷移。 如需詳細資訊，請參閱[SYSVOL 複寫遷移指南： FRS to DFS 複寫](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Azure AD 密碼保護 DC 代理程式軟體目前會安裝在網域中的網域控制站上，而這些網域仍在使用 FRS （前身為 DFSR）進行 sysvol 複寫，但軟體將無法在此環境中正常運作。
    >
    > 額外的負面副作用包括無法複寫的個別檔案，且 sysvol 還原程式似乎成功，但無法以無訊息方式複寫所有檔案。
    >
    > 請儘快將您的網域遷移至使用 DFSR，這兩者都是針對 DFSR 的固有優勢，並解除封鎖 Azure AD 密碼保護的部署。 軟體的未來版本將會在仍使用 FRS 的網域中執行時自動停用。

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD 密碼保護 proxy 服務

下列需求適用于 Azure AD 密碼保護 proxy 服務：

* 將安裝 Azure AD 密碼保護 proxy 服務的所有電腦都必須執行 Windows Server 2012 R2 或更新版本。

    > [!NOTE]
    > Azure AD 密碼保護 proxy 服務部署是部署 Azure AD 密碼保護的必要需求，即使網域控制站可能具有輸出直接網際網路連線能力。

* 將安裝 Azure AD 密碼保護 proxy 服務的所有電腦都必須安裝 .NET 4.7。
    * .NET 4.7 應該已經安裝在完全更新的 Windows 伺服器上。 如有需要，請下載並執行[Windows 的 .NET Framework 4.7 離線安裝程式](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)中找到的安裝程式。
* 裝載 Azure AD 密碼保護 proxy 服務的所有電腦都必須設定為授與網域控制站登入 proxy 服務的能力。 此功能是透過「從網路存取這台電腦」許可權指派來控制。
* 裝載 Azure AD 密碼保護 proxy 服務的所有電腦都必須設定為允許輸出 TLS 1.2 HTTP 流量。
* 用來向 Azure AD 註冊 Azure AD 密碼保護 proxy 服務與樹系的*全域系統管理員*帳戶。
* 您必須針對[應用程式 Proxy 環境安裝](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)程式中指定的一組埠和 url 啟用網路存取。

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Microsoft Azure AD 連接代理程式更新程式的必要條件

Microsoft Azure AD Connect Agent 更新程式服務會與 Azure AD 密碼保護 Proxy 服務並存安裝。 需要進行其他設定，才能讓 Microsoft Azure AD Connect 代理程式更新程式服務能夠運作：

* 如果您的環境使用 HTTP proxy 伺服器，請遵循[使用現有的內部部署 proxy 伺服器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)中所指定的指導方針。
* Microsoft Azure AD Connect Agent 更新程式服務也需要[tls 需求](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)中指定的 tls 1.2 步驟。

> [!WARNING]
> Azure AD 密碼保護 proxy 和 Azure AD 應用程式 Proxy 會安裝不同版本的 Microsoft Azure AD 連接代理程式更新程式服務，這就是為什麼指示會參考應用程式 Proxy 內容的原因。 這些不同版本在並存安裝時不相容，因此不建議在同一部電腦上安裝 Azure AD 的密碼保護 Proxy 和應用程式 Proxy。

## <a name="download-required-software"></a>下載所需的軟體

內部部署 Azure AD 密碼保護部署有兩個必要的安裝程式：

* Azure AD 密碼保護 DC 代理程式（*AzureADPasswordProtectionDCAgentSetup .msi*）
* Azure AD 密碼保護 proxy （*azureadpasswordprotectionproxysetup.msi .exe*）

從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)下載這兩個安裝程式。

## <a name="install-and-configure-the-proxy-service"></a>安裝和設定 proxy 服務

Azure AD 的密碼保護 proxy 服務通常位於內部部署 AD DS 環境中的成員伺服器上。 安裝之後，Azure AD 的密碼保護 proxy 服務就會與 Azure AD 通訊，為您的 Azure AD 租使用者維護全域和客戶禁用密碼清單的複本。

在下一節中，您會在內部部署 AD DS 環境中的網域控制站上安裝 Azure AD 密碼保護 DC 代理程式。 這些 DC 代理程式會與 proxy 服務通訊，以取得最新的禁用密碼清單，以便在處理網域內的密碼變更事件時使用。

選擇一或多部要裝載 Azure AD 密碼保護 proxy 服務的伺服器。 下列考慮適用于伺服器：

* 每個這類服務只能為單一樹系提供密碼原則。 主機電腦必須加入該樹系中的網域。 同時支援根域和子域。 在樹系的每個網域中至少有一個 DC 與密碼保護電腦之間，您需要有網路連線能力。
* 您可以在網域控制站上執行 Azure AD 的密碼保護 proxy 服務來進行測試，但是該網域控制站需要網際網路連線能力。 此連線可能是安全性考慮。 我們建議僅供測試之用。
* 我們建議至少有兩個 Azure AD 的密碼保護 proxy 伺服器來進行冗余，如前一節關於[高可用性考慮](#high-availability-considerations)中所述。
* 不支援在唯讀網域控制站上執行 Azure AD 密碼保護 proxy 服務。

若要安裝 Azure AD 密碼保護 proxy 服務，請完成下列步驟：

1. 若要安裝 Azure AD 密碼保護 proxy 服務，請執行 `AzureADPasswordProtectionProxySetup.exe` 軟體安裝程式。

    軟體安裝不需要重新開機，而且可能會使用標準 MSI 程式來自動化，如下列範例所示：
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > 在您安裝 `AzureADPasswordProtectionProxySetup.exe` 套件之前，必須先執行 Windows 防火牆服務，以避免發生安裝錯誤。
    >
    > 如果 Windows 防火牆設定為不執行，因應措施是在安裝期間暫時啟用並執行防火牆服務。 在安裝後，proxy 軟體對 Windows 防火牆沒有特定的相依性。
    >
    > 如果您使用的是協力廠商防火牆，仍然必須將其設定為符合部署需求。 其中包括允許對埠135和 proxy RPC 伺服器埠的輸入存取。 如需詳細資訊，請參閱上一節的[部署需求](#deployment-requirements)。

1. Azure AD 的密碼保護 proxy 套裝軟體含新的 PowerShell 模組，`AzureADPasswordProtection`。 下列步驟會從這個 PowerShell 模組執行各種 Cmdlet。

    若要使用此模組，請以系統管理員身分開啟 PowerShell 視窗，並匯入新的模組，如下所示：
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. 若要檢查 Azure AD 的密碼保護 proxy 服務是否正在執行，請使用下列 PowerShell 命令：

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    結果應該會顯示 [*正在*執行] 的**狀態**。

1. Proxy 服務正在電腦上執行，但沒有認證可與 Azure AD 通訊。 使用 `Register-AzureADPasswordProtectionProxy` Cmdlet 向 Azure AD 註冊 Azure AD 密碼保護 proxy 伺服器。

    此 Cmdlet 需要您 Azure 租使用者的全域管理員認證。 您也需要內部部署 Active Directory 樹系根域中的網域系統管理員許可權。 此 Cmdlet 也必須使用具有本機系統管理員許可權的帳戶來執行：

    在此命令成功針對 Azure AD 密碼保護 proxy 服務執行一次之後，額外的調用會成功，但不需要。

    `Register-AzureADPasswordProtectionProxy` Cmdlet 支援下列三種驗證模式。 前兩個模式支援 Azure 多重要素驗證，但第三個模式則否。

    > [!TIP]
    > 第一次針對特定的 Azure 租使用者執行此 Cmdlet 時，可能會有明顯的延遲。 除非回報失敗，否則請不要擔心這種延遲。

     * 互動式驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 在 Server Core 作業系統上，此模式無法運作。 相反地，請使用下列其中一種驗證模式。 此外，如果啟用 Internet Explorer 增強式安全性設定，此模式可能會失敗。 解決方法是停用該設定、註冊 proxy，然後重新啟用它。

     * 裝置代碼驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        出現提示時，請遵循連結來開啟網頁瀏覽器，並輸入驗證碼。

     * 無訊息 (密碼型) 驗證模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果您的帳戶需要 Azure 多重要素驗證，此模式就會失敗。 在此情況下，請使用先前兩種驗證模式的其中一個，或改為使用不需要 MFA 的其他帳戶。
        >
        > 如果 Azure 裝置註冊（由 Azure AD 密碼保護所使用）已設定為全域要求 MFA，您可能也會看到必要的 MFA。 若要解決此需求，您可以使用與先前兩種驗證模式之一支援 MFA 的不同帳戶，或者您也可以暫時放寬 Azure 裝置註冊 MFA 需求。
        >
        > 若要進行這項變更，請搜尋並選取 Azure 入口網站中的**Azure Active Directory** ，然後選取 [**裝置] > [裝置設定**]。 將 [**需要多重要素驗證才能將裝置加入**至] 設定為 [*否*]。 註冊完成後，請務必將此設定重新設定回 *[是]* 。
        >
        > 我們建議您將 MFA 需求略過，僅供測試之用。

    您目前不需要指定 *-ForestCredential*參數，它會保留供未來的功能使用。

    只有在服務的存留期內，才需要註冊 Azure AD 密碼保護 proxy 服務。 之後，Azure AD 密碼保護 proxy 服務就會自動執行任何其他必要的維護。

1. 現在，使用 `Register-AzureADPasswordProtectionForest` PowerShell Cmdlet，在內部部署 Active Directory 樹系中註冊必要的認證，以與 Azure 進行通訊。

    > [!NOTE]
    > 如果您的環境中已安裝多個 Azure AD 的密碼保護 proxy 伺服器，則您用來註冊樹系的 proxy 伺服器並不重要。

    此 Cmdlet 需要您 Azure 租使用者的全域管理員認證。 您也必須使用具有本機系統管理員許可權的帳戶來執行此 Cmdlet。 它也需要內部部署 Active Directory 企業系統管理員許可權。 此步驟會針對每一樹系執行一次。

    `Register-AzureADPasswordProtectionForest` Cmdlet 支援下列三種驗證模式。 前兩個模式支援 Azure 多重要素驗證，但第三個模式則否。

    > [!TIP]
    > 第一次針對特定的 Azure 租使用者執行此 Cmdlet 時，可能會有明顯的延遲。 除非回報失敗，否則請不要擔心這種延遲。

     * 互動式驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 在 Server Core 作業系統上，此模式無法運作。 請改用下列其中一種驗證模式。 此外，如果啟用 Internet Explorer 增強式安全性設定，此模式可能會失敗。 解決方法是停用該設定、註冊樹系，然後重新啟用它。  

     * 裝置代碼驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        出現提示時，請遵循連結來開啟網頁瀏覽器，並輸入驗證碼。

     * 無訊息 (密碼型) 驗證模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果您的帳戶需要 Azure 多重要素驗證，此模式就會失敗。 在此情況下，請使用先前兩種驗證模式的其中一個，或改為使用不需要 MFA 的其他帳戶。
        >
        > 如果 Azure 裝置註冊（由 Azure AD 密碼保護所使用）已設定為全域要求 MFA，您可能也會看到必要的 MFA。 若要解決此需求，您可以使用與先前兩種驗證模式之一支援 MFA 的不同帳戶，或者您也可以暫時放寬 Azure 裝置註冊 MFA 需求。
        >
        > 若要進行這項變更，請搜尋並選取 Azure 入口網站中的**Azure Active Directory** ，然後選取 [**裝置] > [裝置設定**]。 將 [**需要多重要素驗證才能將裝置加入**至] 設定為 [*否*]。 註冊完成後，請務必將此設定重新設定回 *[是]* 。
        >
        > 我們建議您將 MFA 需求略過，僅供測試之用。

       只有當目前登入的使用者也是根域的 Active Directory 網域系統管理員時，這些範例才會成功。 如果不是這種情況，您可以透過 *-ForestCredential*參數提供替代網域認證。

    只有在樹系的存留期內，才需要註冊 Active Directory 樹系。 之後，樹系中的 Azure AD 密碼保護 DC 代理程式就會自動執行任何其他必要的維護。 成功執行樹系的 `Register-AzureADPasswordProtectionForest` 之後，Cmdlet 的其他調用會成功，但不需要。
    
    若要讓 `Register-AzureADPasswordProtectionForest` 成功，Azure AD 密碼保護 proxy 伺服器的網域中至少必須有一個執行 Windows Server 2012 或更新版本的 DC。 在此步驟之前，不需要在任何網域控制站上安裝 Azure AD 密碼保護 DC 代理程式軟體。

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>設定 proxy 服務以透過 HTTP proxy 進行通訊

如果您的環境需要使用特定的 HTTP proxy 來與 Azure 通訊，請使用下列步驟來設定 Azure AD 密碼保護服務。

在 `%ProgramFiles%\Azure AD Password Protection Proxy\Service` 資料夾中建立*register-azureadpasswordprotectionproxy* 。 包含下列內容：

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

如果您的 HTTP proxy 需要驗證，請新增*useDefaultCredentials*標記：

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

在這兩種情況下，請將 `http://yourhttpproxy.com:8080` 取代為特定 HTTP proxy 伺服器的位址和埠。

如果您的 HTTP proxy 已設定為使用授權原則，您必須授與裝載 proxy 服務之電腦的 Active Directory 電腦帳戶的存取權，以進行密碼保護。

建議您在建立或更新*register-azureadpasswordprotectionproxy*之後，停止並重新啟動 Azure AD 的密碼保護 proxy 服務。

Proxy 服務不支援使用特定認證來連接 HTTP proxy。

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>設定 proxy 服務接聽特定埠

Azure AD 密碼保護 DC 代理程式軟體會使用 RPC over TCP 來與 proxy 服務進行通訊。 根據預設，Azure AD 的密碼保護 proxy 服務會在任何可用的動態 RPC 端點上進行接聽。 您可以根據環境中的網路拓朴或防火牆需求，將服務設定為在特定 TCP 通訊埠上接聽。

<a id="static" /></a>若要將服務設定為在靜態埠下執行，請使用 `Set-AzureADPasswordProtectionProxyConfiguration` Cmdlet，如下所示：

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> 您必須停止並重新啟動 Azure AD 的密碼保護 proxy 服務，這些變更才會生效。

若要將服務設定為在動態埠下執行，請使用相同的程式，但將*StaticPort*設回零：

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> 您必須停止並重新啟動 Azure AD 的密碼保護 proxy 服務，這些變更才會生效。

Azure AD 的密碼保護 proxy 服務在埠設定變更之後，需要手動重新開機。 在進行這些設定變更之後，您不需要重新開機網域控制站上的 Azure AD 密碼保護 DC 代理程式服務。

若要查詢服務的目前設定，請使用 `Get-AzureADPasswordProtectionProxyConfiguration` Cmdlet，如下列範例所示

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

下列範例輸出顯示 Azure AD 密碼保護 proxy 服務使用動態埠：

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>安裝 DC 代理程式服務

若要安裝 Azure AD 密碼保護 DC 代理程式服務，請執行 `AzureADPasswordProtectionDCAgentSetup.msi` 套件。

您可以使用標準 MSI 程式將軟體安裝自動化，如下列範例所示：

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

如果您想要讓安裝程式自動重新開機電腦，可以省略 `/norestart` 旗標。

軟體安裝或卸載需要重新開機。 這項需求是因為密碼篩選 Dll 只會在重新開機時載入或卸載。

在網域控制站上安裝 DC 代理程式軟體並重新啟動該電腦後，安裝內部部署 Azure AD 密碼保護已完成。 任何其他設定都無須進行，也無法進行。 針對內部內部部署 Dc 的密碼變更事件會使用來自 Azure AD 的已設定禁用密碼清單。

若要從 Azure 入口網站啟用內部部署 Azure AD 密碼保護，或設定自訂的禁用密碼，請參閱[啟用內部部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-operations.md)。

> [!TIP]
> 您可以在還不是網域控制站的電腦上安裝 Azure AD 密碼保護 DC 代理程式。 在此情況下，服務會啟動並執行，但會維持非作用中狀態，直到電腦升級為網域控制站為止。

## <a name="upgrading-the-proxy-service"></a>升級 proxy 服務

Azure AD 密碼保護 proxy 服務支援自動升級。 自動升級會使用 Microsoft Azure AD Connect Agent 更新程式服務，這會與 proxy 服務並存安裝。 預設會開啟自動升級，而且可能會使用 `Set-AzureADPasswordProtectionProxyConfiguration` Cmdlet 來啟用或停用。

您可以使用 `Get-AzureADPasswordProtectionProxyConfiguration` Cmdlet 來查詢目前的設定。 我們建議您一律啟用自動升級設定。

`Get-AzureADPasswordProtectionProxy` Cmdlet 可用來查詢樹系中所有目前已安裝 Azure AD 密碼保護 proxy 伺服器的軟體版本。

### <a name="manual-upgrade-process"></a>手動升級程式

手動升級是藉由執行最新版的 `AzureADPasswordProtectionProxySetup.exe` 軟體安裝程式來完成。 您可以從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)取得軟體的最新版本。

不需要卸載目前版本的 Azure AD 密碼保護 proxy 服務-安裝程式會執行就地升級。 升級 proxy 服務時，不需要重新開機。 軟體升級可能會使用標準 MSI 程式自動化，例如 `AzureADPasswordProtectionProxySetup.exe /quiet`。

## <a name="upgrading-the-dc-agent"></a>升級 DC 代理程式

當有較新版本的 Azure AD 密碼保護 DC 代理程式軟體可供使用時，會執行最新版的 `AzureADPasswordProtectionDCAgentSetup.msi` 軟體套件來進行升級。 您可以從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=57071)取得軟體的最新版本。

不需要卸載目前版本的 DC 代理程式軟體-安裝程式會執行就地升級。 升級 DC 代理程式軟體時，一律需要重新開機-這項需求是由核心 Windows 行為所造成。

軟體升級可能會使用標準 MSI 程式自動化，例如 `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`。

如果您想要讓安裝程式自動重新開機電腦，可以省略 `/norestart` 旗標。

`Get-AzureADPasswordProtectionDCAgent` Cmdlet 可用來查詢樹系中所有目前已安裝 Azure AD 密碼保護 DC 代理程式的軟體版本。

## <a name="next-steps"></a>後續步驟

既然您已在內部部署伺服器上安裝了 Azure AD 密碼保護所需的服務，請在[Azure 入口網站中啟用內部部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-operations.md)，以完成您的部署。
