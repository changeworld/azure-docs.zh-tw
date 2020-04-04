---
title: 部署本地 Azure AD 密碼保護
description: 瞭解如何在本地活動目錄域服務環境中規劃與部署 Azure AD 密碼保護
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ac9b76dd8d3c950b14f6d7b331f15647427ac89
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652727"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>規劃並部署 Azure 活動目錄密碼保護

使用者通常創建使用常見本地單詞(如學校、運動隊或名人)的密碼。 這些密碼很容易猜測,並且對基於字典的攻擊很弱。 要在組織中強制實施強密碼,Azure 活動目錄 (Azure AD) 密碼保護提供了全域和自定義禁止的密碼清單。 如果這些禁用密碼清單中有匹配項,則密碼更改請求將失敗。

為了保護本地活動目錄域服務 (AD DS) 環境,可以安裝和配置 Azure AD 密碼保護以使用上置目錄 DC。 本文介紹如何在本地環境中安裝和註冊 Azure AD 密碼保護代理服務和 Azure AD 密碼保護 DC 代理。

關於 Azure AD 密碼保護在本地環境中的工作方式的詳細資訊,請參閱[如何為 Windows 伺服器活動目錄強制實施 Azure AD 密碼保護](concept-password-ban-bad-on-premises.md)。

## <a name="deployment-strategy"></a>部署策略

下圖顯示了 Azure AD 密碼保護的基本元件如何在本地活動目錄環境中協同工作:

![Azure AD 密碼保護元件如何一起運作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

在部署軟體之前,最好先查看軟體的工作原理。 有關詳細資訊,請參閱[Azure AD 密碼保護的概念概述](concept-password-ban-bad-on-premises.md)。

我們建議您在*審核*模式下開始部署。 審核模式是預設的初始設置,可以繼續設置密碼。 將被阻止的密碼將記錄在事件日誌中。 在審核模式下部署代理伺服器和DC代理後,監視密碼策略在強制實施策略時對用戶的影響。

在審核階段,許多組織發現以下情況適用:

* 它們必須改善現有的作業程序，才能使用更安全的密碼。
* 使用者通常使用不安全的密碼。
* 他們需要告知使用者即將發生的安全強制更改、可能對他們產生的影響以及如何選擇更安全的密碼。

還可以進行更強大的密碼驗證來影響現有的 Active Directory 域控制器部署自動化。 我們建議在審計期間評估期間至少進行一次 DC 升級和一次 DC 降級,以幫助發現此類問題。 如需詳細資訊，請參閱下列文章：

* [Ntdsutil.exe 無法設定儲存目錄服務修復模式密碼](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [域控制器複本升級失敗,因為目錄服務修復模式密碼較弱](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [由於本地管理員密碼薄弱,域控制器降級失敗](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

在該功能在審核模式下運行一段合理期間後,您可以將配置從*審核*切換到 *「強制」,* 以需要更安全的密碼。 在此期間進行額外的監視是一個好主意。

### <a name="multiple-forest-considerations"></a>多種林注意事項

沒有任何要在多個樹系部署 Azure AD 密碼保護的額外需求。

每個林都獨立配置,如下節所述,用於[部署上部 Azure AD 密碼保護](#download-required-software)。 每個 Azure AD 密碼保護代理只能支援其聯接到的林中的域控制器。

任何林中的 Azure AD 密碼保護軟體都不知道部署在其他林中的密碼保護軟體,而不考慮 Active Directory 信任配置。

### <a name="read-only-domain-controller-considerations"></a>唯讀網域控制器注意事項

密碼更改或設定事件不會處理和持久化在唯讀域控制器 (RODCs) 上。 相反,它們被轉發到可寫域控制器。 您不必在 RODCs 上安裝 Azure AD 密碼保護 DC 代理軟體。

此外,不支援在唯讀域控制器上執行 Azure AD 密碼保護代理服務。

### <a name="high-availability-considerations"></a>高可用性注意事項

密碼保護的主要問題是當林中的 DC 嘗試從 Azure 下載新策略或其他數據時,Azure AD 密碼保護代理伺服器的可用性。 每個 Azure AD 密碼保護 DC 代理在決定呼叫哪個代理伺服器時都使用簡單的循環樣式演演演算法。 代理跳過未回應的代理伺服器。

對於大多數完全連接的 Active Directory 部署,這些部署具有目錄和 sysvol 資料夾狀態的正常運行複製,兩個 Azure AD 密碼保護代理伺服器足以確保可用性。 此配置可及時下載新策略和其他數據。 如果需要,可以部署其他 Azure AD 密碼保護代理伺服器。

Azure AD 密碼保護 DC 代理軟體的設計可緩解與高可用性相關的常見問題。 Azure AD 密碼保護 DC 代理維護最近下載的密碼策略的本地緩存。 即使所有已註冊的代理伺服器都不可用,Azure AD 密碼保護DC代理仍繼續強制執行其緩存的密碼策略。

大型部署中密碼策略的合理更新頻率通常是幾天,而不是小時或更少。 因此,代理伺服器的短暫中斷不會顯著影響 Azure AD 密碼保護。

## <a name="deployment-requirements"></a>部署需求

有關授權的資訊,請參閱[Azure AD 密碼保護許可要求](concept-password-ban-bad.md#license-requirements)。

以下核心要求適用:

* 安裝了 Azure AD 密碼保護元件的所有電腦(包括域控制器)都必須安裝通用 C 運行時。
    * 您可以通過確保從 Windows 更新獲得所有更新來獲取運行時。 或者,您可以在特定於作業系統的更新包中獲取它。 有關詳細資訊,請參閱[Windows 中通用 C 執行時的更新](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)。
* 您需要在林根域中具有活動目錄域管理員許可權的帳戶才能將 Windows 伺服器活動目錄林與 Azure AD 註冊。
* 必須在運行 Windows Server 2012 的網域中的所有域控制器上啟用密鑰分發服務。 默認情況下,此服務是通過手動觸發器啟動啟用的。
* 每個域中至少有一個域控制器和至少一台承載 Azure AD 密碼保護代理服務的伺服器之間必須存在網路連接。 此連接必須允許域控制器存取 RPC 終結點映射器埠 135 和代理服務上的 RPC 伺服器埠。
    * 預設情況下,RPC 伺服器連接埠是動態 RPC 連接埠,但可以設定為[使用靜態連接埠](#static)。
* 將安裝 Azure AD 密碼保護代理服務的所有電腦都必須具有對以下終結點的網路存取權限:

    |**端點**|**目的**|
    | --- | --- |
    |`https://login.microsoftonline.com`|驗證要求|
    |`https://enterpriseregistration.windows.net`|Azure AD 密碼保護功能|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD 密碼保護 DC 代理

以下要求適用於 Azure AD 密碼保護 DC 代理:

* 將安裝 Azure AD 密碼保護 DC 代理軟體的所有電腦都必須運行 Windows Server 2012 或更高版本。
    * 活動目錄功能變數或林不需要位於 Windows Server 2012 功能功能等級 (DFL) 或林功能級別 (FFL)。 如[設計原則](concept-password-ban-bad-on-premises.md#design-principles)所述,DC 代理或代理軟體的運行不需要最低 DFL 或 FFL。
* 執行 Azure AD 密碼保護 DC 代理的所有電腦都必須安裝 .NET 4.5。
* 運行 Azure AD 密碼保護 DC 代理服務的任何活動目錄功能都必須使用分散式檔案系統複製 (DFSR) 進行系統複製。
   * 如果您的網域尚未使用 DFSR,則必須在安裝 Azure AD 密碼保護之前進行遷移。 有關詳細資訊,請參閱[SYSVOL 複製移指南:FRS 到 DFS 複製](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Azure AD 密碼保護 DC 代理軟體目前將安裝在仍在使用 FRS(DFSR 前身技術)進行系統複製的域控制器上,但該軟體在此環境中無法正常工作。
    >
    > 其他負面的副作用包括單個檔無法複製,sysvol 還原過程似乎成功,但默默地無法複製所有檔。
    >
    > 遷移域以儘快使用 DFSR,這既是 DFSR 固有的優勢,也是為了取消阻止 Azure AD 密碼保護的部署。 在仍在使用 FRS 的域中運行時,軟體的未來版本將自動禁用。

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD 密碼保護代理服務

以下要求適用於 Azure AD 密碼保護代理服務:

* 將安裝 Azure AD 密碼保護代理服務的所有電腦都必須運行 Windows Server 2012 R2 或更高版本。

    > [!NOTE]
    > Azure AD 密碼保護代理服務部署是部署 Azure AD 密碼保護的必填項,即使域控制器可能具有出站直接 Internet 連接。

* 將安裝 Azure AD 密碼保護代理服務的所有電腦都必須安裝 .NET 4.7。
    * .NET 4.7 應該已安裝在完全更新的 Windows 伺服器上。 如有必要,請下載並執行在 Windows[的 .NET 架構 4.7 離線安裝程式中找到的安裝程式](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)。
* 必須配置承載 Azure AD 密碼保護代理服務的所有電腦,以便授予域控制器登錄到代理服務的能力。 此功能通過「從網路存取此電腦」許可權分配進行控制。
* 必須將承載 Azure AD 密碼保護代理服務的所有電腦配置為允許出站 TLS 1.2 HTTP 流量。
* 全域*管理員*帳戶,用於向 Azure AD 註冊 Azure AD 密碼保護代理服務和林。
* 必須為[應用程式代理環境設置過程](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)中指定的埠和網址集啟用網路存取。

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>微軟 Azure AD 連線代理更新程式先決條件

Microsoft Azure AD 連接代理更新程式服務與 Azure AD 密碼保護代理服務並排安裝。 需要額外的設定才能使 Microsoft Azure AD 連線代理更新程式服務能夠正常工作:

* 如果環境使用 HTTP 代理伺服器,請按照「[使用現有本地代理伺服器」](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)中指定的準則操作。
* 微軟 Azure AD 連接代理更新程式服務還需要[TLS 要求](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)中指定的 TLS 1.2 步驟。

> [!WARNING]
> Azure AD 密碼保護代理和 Azure AD 應用程式代理安裝不同版本的 Microsoft Azure AD 連接代理更新程式服務,這就是為什麼說明引用應用程式代理內容的原因。 並排安裝時,這些不同版本不相容,因此不建議在同一台電腦上安裝 Azure AD 密碼保護代理和應用程式代理。

## <a name="download-required-software"></a>下載所需的軟體

本地 Azure AD 密碼保護部署需要兩個安裝程式:

* Azure AD 密碼保護 DC 代理伺服器 (*AzureAD 密碼保護 DC 代理.msi*)
* Azure AD 密碼保護代理伺服器 (*AzureAD 密碼保護代理.exe*)

從[微軟下載中心](https://www.microsoft.com/download/details.aspx?id=57071)下載兩個安裝程式。

## <a name="install-and-configure-the-proxy-service"></a>安裝並設定代理服務

Azure AD 密碼保護代理服務通常位於本地 ADDS 環境中的成員伺服器上。 安裝後,Azure AD 密碼保護代理服務將與 Azure AD 通訊,以維護 Azure AD 租戶全域和客戶禁止的密碼清單的副本。

在下一節中,您將 Azure AD 密碼保護 DC 代理安裝在本地 ADDS 環境中的網域控制器上。 這些 DC 代理與代理服務通訊,獲取最新的禁止密碼清單,用於在處理網域中的密碼更改事件時使用。

選擇一個或多個伺服器來承載 Azure AD 密碼保護代理服務。 以下注意事項適用於伺服器:

* 每個此類服務只能為單個林提供密碼策略。 主機必須加入該林中的域。 根域和子域都支援。 需要在林的每個域中至少有一個 DC 和密碼保護計算機之間的網路連接。
* 您可以在域控制器上運行 Azure AD 密碼保護代理服務以進行測試,但該域控制器則需要網路連接。 此連接可能是一個安全問題。 我們建議僅對此配置進行測試。
* 我們建議至少兩台 Azure AD 密碼保護代理伺服器進行冗餘,如上一節關於[高可用性注意事項](#high-availability-considerations)的章節中所述。
* 不支援在唯讀域控制器上執行 Azure AD 密碼保護代理服務。

要安裝 Azure AD 密碼保護代理服務,請完成以下步驟:

1. 要安裝 Azure AD 密碼保護`AzureADPasswordProtectionProxySetup.exe`代理服務, 請執行軟體安裝程式。

    軟體安裝不需要重新啟動,並且可以使用標準 MSI 過程進行自動化,如以下範例所示:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > 在安裝套件之前,Windows`AzureADPasswordProtectionProxySetup.exe`防火牆服務必須運行,以避免安裝錯誤。
    >
    > 如果 Windows 防火牆配置為未運行,解決方法是在安裝期間臨時啟用和運行防火牆服務。 安裝後,代理軟體對 Windows 防火牆沒有特定的依賴關係。
    >
    > 如果您使用的是第三方防火牆,則仍必須將其配置為滿足部署要求。 其中包括允許對埠 135 和代理 RPC 伺服器埠進行入站訪問。 有關詳細資訊,請參閱上一節關於[部署要求](#deployment-requirements)。

1. Azure AD 密碼保護代理軟體包括新的`AzureADPasswordProtection`PowerShell 模組。 以下步驟運行此 PowerShell 模組的各種 cmdlet。

    要使用此模組,請使用管理員打開 PowerShell 視窗,然後匯入新模組,如下所示:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. 要檢查 Azure AD 密碼保護代理服務是否正在執行,請使用以下 PowerShell 命令:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    結果應顯示 *「 正在執行***」 的狀態**。

1. 代理服務在電腦上運行,但沒有憑據與 Azure AD 通信。 使用`Register-AzureADPasswordProtectionProxy`cmdlet 將 Azure AD 密碼保護代理伺服器註冊到 Azure AD。

    此 cmdlet 需要 Azure 租戶的全域管理員憑據。 您還需要林根域中的本地活動目錄域管理員許可權。 還必須使用具有本地管理員權限的帳戶執行此 cmdlet:

    此命令成功一次後,Azure AD 密碼保護代理服務,它的其他調用成功,但沒有必要。

    `Register-AzureADPasswordProtectionProxy` cmdlet 支援以下三種身份驗證模式。 前兩種模式支援 Azure 多重身份驗證,但第三種模式不支援。

    > [!TIP]
    > 首次為特定 Azure 租戶運行此 cmdlet 時,在完成之前可能會出現明顯的延遲。 除非報告失敗,否則不要擔心此延遲。

     * 互動式驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式在伺服器核心操作系統上不起作用。 而是使用以下身份驗證模式之一。 此外,如果啟用了 Internet 資源管理器增強的安全配置,此模式可能會失敗。 解決方法是禁用該配置、註冊代理,然後重新啟用它。

     * 裝置代碼驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        當出現提示時,按照連結打開 Web 瀏覽器並輸入身份驗證代碼。

     * 無訊息 (密碼型) 驗證模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果帳戶需要 Azure 多重身份驗證,則此模式將失敗。 在這種情況下,請使用前兩種身份驗證模式之一,或者使用不需要 MFA 的不同帳戶。
        >
        > 如果 Azure 設備註冊(Azure AD 密碼保護在封面下使用)已配置為全域需要 MFA,則可能還會看到所需的 MFA。 要滿足此要求,可以使用支援 MFA 與前兩種身份驗證模式之一之一的不同帳戶,也可以暫時放寬 Azure 設備註冊 MFA 要求。
        >
        > 要進行此更改,請在 Azure 門戶中搜索並選擇**Azure 活動目錄**,然後選擇 **「設備>設備設置**」。 設定**需要多因素身份驗證才能將裝置聯接**到*No*。 註冊完成後,請確保將此設置重新設定為 *"是*"。
        >
        > 我們建議僅出於測試目的繞過 MFA 要求。

    您當前不必指定 *-ForestCredential*參數,該參數是為將來的功能保留的。

    在服務的生存期內,只需註冊一次 Azure AD 密碼保護代理服務。 之後,Azure AD 密碼保護代理服務將自動執行任何其他必要的維護。

1. 現在,使用`Register-AzureADPasswordProtectionForest`PowerShell cmdlet 將本地活動目錄林註冊為與 Azure 通信所需的認證。

    > [!NOTE]
    > 如果環境中安裝了多個 Azure AD 密碼保護代理伺服器,則使用哪個代理伺服器註冊林並不重要。

    cmdlet 需要 Azure 租戶的全域管理員憑據。 您必須使用具有本地管理員權限的帳戶執行此 cmdlet。 它還需要本地活動目錄企業管理員許可權。 此步驟會針對每一樹系執行一次。

    `Register-AzureADPasswordProtectionForest` cmdlet 支援以下三種身份驗證模式。 前兩種模式支援 Azure 多重身份驗證,但第三種模式不支援。

    > [!TIP]
    > 首次為特定 Azure 租戶運行此 cmdlet 時,在完成之前可能會出現明顯的延遲。 除非報告失敗,否則不要擔心此延遲。

     * 互動式驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式在伺服器核心操作系統上不起作用。 而是使用以下兩種身份驗證模式之一。 此外,如果啟用了 Internet 資源管理器增強的安全配置,此模式可能會失敗。 解決方法是禁用該配置、註冊林,然後重新啟用它。  

     * 裝置代碼驗證模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        當出現提示時,按照連結打開 Web 瀏覽器並輸入身份驗證代碼。

     * 無訊息 (密碼型) 驗證模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果帳戶需要 Azure 多重身份驗證,則此模式將失敗。 在這種情況下,請使用前兩種身份驗證模式之一,或者使用不需要 MFA 的不同帳戶。
        >
        > 如果 Azure 設備註冊(Azure AD 密碼保護在封面下使用)已配置為全域需要 MFA,則可能還會看到所需的 MFA。 要滿足此要求,可以使用支援 MFA 與前兩種身份驗證模式之一之一的不同帳戶,也可以暫時放寬 Azure 設備註冊 MFA 要求。
        >
        > 要進行此更改,請在 Azure 門戶中搜索並選擇**Azure 活動目錄**,然後選擇 **「設備>設備設置**」。 設定**需要多因素身份驗證才能將裝置聯接**到*No*。 註冊完成後,請確保將此設置重新設定為 *"是*"。
        >
        > 我們建議僅出於測試目的繞過 MFA 要求。

       僅當當前登錄使用者也是根域的活動目錄域管理員時,這些示例才成功。 如果不是這樣,您可以通過 *-ForestCredential*參數提供替代域認證。

    在林的生存期內,活動目錄林的註冊僅需要一次。 之後,林中的 Azure AD 密碼保護 DC 代理將自動執行任何其他必要的維護。 成功`Register-AzureADPasswordProtectionForest`運行林后,cmdlet 的其他調用將成功,但沒有必要。
    
    為了`Register-AzureADPasswordProtectionForest`成功,在 Azure AD 密碼保護代理伺服器的域中必須至少有一個運行 Windows Server 2012 或更高版本的 DC 可用。 在此步驟之前,不需要在任何域控制器上安裝 Azure AD 密碼保護 DC 代理軟體。

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>將代理服務設定為透過 HTTP 代理進行通訊

如果環境需要使用特定的 HTTP 代理與 Azure 通信,請使用以下步驟配置 Azure AD 密碼保護服務。

在`%ProgramFiles%\Azure AD Password Protection Proxy\Service`資料夾中創建*AzureAD 密碼保護代理.exe.config*檔案。 包括以下內容:

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

如果您的 HTTP 代理需要認證,請新增*使用預設認證標籤*:

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

在這兩種情況下,請`http://yourhttpproxy.com:8080`替換為特定 HTTP 代理伺服器的位址和埠。

如果您的 HTTP 代理設定為使用授權策略,則必須授予對承載代理服務的電腦的 Active Directory 電腦帳戶的訪問許可權,以便進行密碼保護。

我們建議您在創建或更新*AzureAD 密碼保護代理檔*後停止並重新啟動 Azure AD 密碼保護代理服務。

代理服務不支援使用特定憑據連接到 HTTP 代理。

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>設定代理服務以偵聽特定連接埠

Azure AD 密碼保護 DC 代理軟體使用透過 TCP 的 RPC 與代理服務通訊。 默認情況下,Azure AD 密碼保護代理服務偵聽任何可用的動態 RPC 終結點。 如有必要,由於環境中的網路拓撲或防火牆要求,您可以將服務配置為偵聽特定的 TCP 埠。

<a id="static" /></a>要將服務設定為在靜態連接埠下執行,`Set-AzureADPasswordProtectionProxyConfiguration`請使用 cmdlet,如下所示:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> 您必須停止並重新啟動 Azure AD 密碼保護代理服務,才能使這些更改生效。

要將服務配置為在動態埠下運行,請使用相同的過程,但將*StaticPort*設置為零:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> 您必須停止並重新啟動 Azure AD 密碼保護代理服務,才能使這些更改生效。

Azure AD 密碼保護代理服務需要在埠配置發生任何更改後手動重新啟動。 進行這些配置更改後,不必在域控制器上重新啟動 Azure AD 密碼保護 DC 代理服務。

要查詢服務的目前設定,`Get-AzureADPasswordProtectionProxyConfiguration`請使用 cmdlet,如以下範例所示

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

以下範例輸出顯示 Azure AD 密碼保護代理服務正在使用動態埠:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>安裝 DC 代理伺服器

要安裝 Azure AD 密碼`AzureADPasswordProtectionDCAgentSetup.msi`保護 DC 代理 服務,請運行套件。

您可以使用標準 MSI 過程自動安裝軟體,如以下範例所示:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

如果您希望`/norestart`安裝程式自動重新啟動計算機,則可以省略該標誌。

軟體安裝或卸載需要重新啟動。 此要求是因為密碼篩選器 DLL 僅通過重新啟動載入或卸載。

在網域控制器上安裝 DC 代理軟體並重新啟動該電腦後,將完成打開的 Azure AD 密碼保護。 任何其他設定都無須進行，也無法進行。 針對預置 DC 的密碼更改事件使用 Azure AD 中設定的禁止密碼清單。

要從 Azure 門戶啟用預覽 Azure AD 密碼保護或設定自訂禁止的密碼,請參閱[啟用本地 Azure AD 密碼保護](howto-password-ban-bad-on-premises-operations.md)。

> [!TIP]
> 您可以在尚未成為網域控制器的電腦上安裝 Azure AD 密碼保護 DC 代理。 在這種情況下,服務啟動並運行,但在計算機升級為域控制器之前保持非活動狀態。

## <a name="upgrading-the-proxy-service"></a>升級代理服務

Azure AD 密碼保護代理服務支援自動升級。 自動升級使用 Microsoft Azure AD 連接代理更新程式服務,該服務與代理服務並排安裝。 默認情況下,自動升級處於打開狀態,並且可能使用`Set-AzureADPasswordProtectionProxyConfiguration`cmdlet 啟用或禁用。

可以使用`Get-AzureADPasswordProtectionProxyConfiguration`cmdlet 查詢當前設定。 我們建議始終啟用自動升級設置。

`Get-AzureADPasswordProtectionProxy` cmdlet 可用於查詢林中目前安裝的所有 Azure AD 密碼保護代理伺服器的軟體版本。

### <a name="manual-upgrade-process"></a>手動升級過程

通過運行最新版本`AzureADPasswordProtectionProxySetup.exe`的軟體安裝程式來完成手動升級。 該軟體的最新版本可在[微軟下載中心](https://www.microsoft.com/download/details.aspx?id=57071)。

卸載 Azure AD 密碼保護代理服務的目前版本不需要 - 安裝程式執行就地升級。 升級代理服務時,不需要重新啟動。 軟體升級可以使用標準 MSI 過程`AzureADPasswordProtectionProxySetup.exe /quiet`(如 ) 自動進行。

## <a name="upgrading-the-dc-agent"></a>升級 DC 代理

當 Azure AD 密碼保護 DC 代理軟體的較新版本可用時,`AzureADPasswordProtectionDCAgentSetup.msi`通過運行最新版本的套件來完成升級。 該軟體的最新版本可在[微軟下載中心](https://www.microsoft.com/download/details.aspx?id=57071)。

卸載 DC 代理軟體的目前版本不需要 - 安裝程式執行就地升級。 升級 DC 代理軟體時,始終需要重新啟動 - 此要求是由核心 Windows 行為引起的。

軟體升級可以使用標準 MSI 過程`msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`(如 ) 自動進行。

如果您希望安裝程式自動重新啟動電腦`/norestart`,則可以省略該標誌。

`Get-AzureADPasswordProtectionDCAgent` cmdlet 可用於查詢林中目前安裝的所有 Azure AD 密碼保護 DC 代理的軟體版本。

## <a name="next-steps"></a>後續步驟

現在,您已在本地伺服器上安裝了 Azure AD 密碼保護所需的服務,請在 Azure[門戶中啟用預置 Azure AD 密碼保護](howto-password-ban-bad-on-premises-operations.md)以完成部署。
