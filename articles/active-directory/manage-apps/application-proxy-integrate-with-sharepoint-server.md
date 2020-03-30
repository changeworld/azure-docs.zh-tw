---
title: 啟用對 SharePoint 的遠端存取 - Azure AD 應用程式代理
description: 涵蓋如何整合內部部署 SharePoint 伺服器與 Azure AD 應用程式 Proxy 的基本概念。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983470994c103cb25d0d2aff96ae8544080e6288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481291"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取

此分步指南介紹了如何將本地 SharePoint 伺服器場與 Azure 活動目錄 （Azure AD） 應用程式代理集成。

## <a name="prerequisites"></a>Prerequisites

要執行配置，您需要以下資源：
- A SharePoint 2013 伺服器場或更新。
- 具有包含應用程式代理的計畫的 Azure AD 租戶。 詳細瞭解[Azure AD 計畫和定價](https://azure.microsoft.com/pricing/details/active-directory/)。
- Azure AD 租戶中的[自訂已驗證域](../fundamentals/add-custom-domain.md)。
- 本地活動目錄與 Azure AD 連接同步，使用者可以通過該目錄[登錄到 Azure](../hybrid/plan-connect-user-signin.md)。
- 在公司域中的電腦上安裝和運行的應用程式代理連接器。

使用應用程式代理配置共用點需要兩個 URL：
- 外部 URL，對最終使用者可見，並在 Azure AD 中確定。 此 URL 可以使用自訂域。 瞭解有關在[Azure AD 應用程式代理 中使用自訂域的更多詳細資訊](application-proxy-configure-custom-domain.md)。
- 內部 URL，僅在公司域中知道，從不直接使用。

> [!IMPORTANT]
> 要確保連結映射正確，請按照以下建議進行內部 URL：
> - 使用 HTTPS：
> - 不要使用自訂埠。
> - 在公司網域名稱系統 （DNS） 中，創建主機 （A） 以指向 SharePoint WFE（或負載等化器），而不是別名 （CName）。

本文使用以下值：
- 內部 URL：`https://sharepoint`
- 外部 URL：`https://spsites-demo1984.msappproxy.net/`
- SharePoint Web 應用程式的應用程式池帳戶：`Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>第 1 步：在 Azure AD 中配置使用應用程式代理的應用程式

在此步驟中，您將在 Azure 活動目錄租戶中創建使用應用程式代理的應用程式。 設置外部 URL 並指定內部 URL，這兩個 URL 稍後在 SharePoint 中使用。

1. 創建應用，如以下設置所述。 如需逐步指示，請參閱[使用 Azure AD 應用程式 Proxy 發佈應用程式](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。
   * **內部 URL**：將在 SharePoint 中稍後設置的 SharePoint`https://sharepoint`內部 URL，如 。
   * **預身份驗證**： Azure 活動目錄
   * **翻譯標題中的 URL**：否
   * **翻譯應用程式正文中的 URL**：否

   ![將 SharePoint 發佈為應用程式](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. 發佈應用後，按照以下步驟配置單個登錄設置：

   1. 在入口網站的應用程式頁面上，選取 [單一登入]****。
   1. 對於**單一登入模式**，請選擇**集成 Windows 身份驗證**。
   1. 將**內部應用程式 SPN**設置為之前設置的值。 在此示例中，值為`HTTP/sharepoint`。
   1. 在 **"委派登錄標識**"下，為活動目錄林配置選擇最合適的選項。 例如，如果林中具有單個活動目錄域，請選擇**本地 SAM 帳戶名稱**（如下圖所示）。 但是，如果使用者與 SharePoint 和應用程式代理連接器伺服器不在同一域中，請選擇**本地使用者主體名稱**（未在螢幕截圖中顯示）。

   ![設定 SSO 的整合式 Windows 驗證](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. 若要完成您的應用程式設定，請移至 [使用者和群組]**** 區段，並指派使用者存取此應用程式。 

## <a name="step-2-configure-the-sharepoint-web-application"></a>第 2 步：配置 SharePoint Web 應用程式

SharePoint Web 應用程式必須配置 Kerberos 和適當的備用存取對應才能與 Azure AD 應用程式代理正確工作。 有兩種可能的選項：

- 創建新的 Web 應用程式，並且僅使用預設區域。 這是首選選項，因為它提供了 SharePoint 的最佳體驗（例如，SharePoint 生成的電子郵件警報中的連結始終指向預設區域）。
- 擴展現有 Web 應用程式以在非預設區域中配置 Kerberos。

> [!IMPORTANT]
> 無論使用哪個區域，SharePoint Web 應用程式的應用程式池帳戶都必須是 Kerberos 正常工作的域帳戶。

### <a name="provision-the-sharepoint-web-application"></a>預配 SharePoint Web 應用程式

- 如果創建新的 Web 應用程式，並且僅使用預設區域（首選選項）：

    1. 啟動**SharePoint 管理命令列**並運行以下腳本：

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. 開啟 [SharePoint 管理中心] **** 網站。
    1. 在 [系統設定]**** 下，選取 [設定備用存取對應]****。 將打開 **"備用存取對應收集"** 框。
    1. 使用新的 Web 應用程式篩選顯示，並確認您看到類似內容：

       ![Web 應用程式的備用存取對應](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- 如果將現有 Web 應用程式擴展到新區域（如果無法使用預設區域）：

    1. 啟動 SharePoint 管理命令列並運行以下腳本：

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. 開啟 [SharePoint 管理中心] **** 網站。
    1. 在 [系統設定]**** 下，選取 [設定備用存取對應]****。 將打開 **"備用存取對應收集"** 框。
    1. 使用擴展的 Web 應用程式篩選顯示，並確認您看到類似內容：

        ![擴展應用程式的備用存取對應](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>確保 SharePoint Web 應用程式在域帳戶下運行

要標識運行 SharePoint Web 應用程式應用程式池的帳戶並確保它是域帳戶，請按照以下步驟操作：

1. 開啟 [SharePoint 管理中心] **** 網站。
1. 移至 [安全性]****，然後選取 [設定服務帳戶]****。
1. 選擇**Web 應用程式池 - 您的 Web 應用程式名稱**。

   ![設定服務帳戶的選項](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. 確認**選擇此元件的帳戶**將返回域帳戶，並記住它，因為在下一步中需要它。

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>確保為外網區域的 IIS 網站配置了 HTTPS 證書

由於內部 URL 使用 HTTPS`https://SharePoint/`協定 （），因此必須在 Internet 資訊服務 （IIS） 網站上設置證書。

1. 打開 Windows 電源外殼主控台。
1. 運行以下腳本以生成自簽章憑證並將其添加到電腦的 MY 存儲：

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > 自我簽署憑證僅適合供測試使用。 在生產環境中，我們強烈建議您改用憑證授權單位頒發的證書。

1. 打開互聯網資訊服務管理員主控台。
1. 在樹狀檢視中展開伺服器，展開**網站**，選擇**SharePoint - AAD 代理**網站，然後選擇**綁定**。
1. 選擇**HTTPs 綁定**，然後選擇 **"編輯**"。
1. 在 TLS/SSL 憑證欄位中，選擇**SharePoint**證書，然後選擇 **"確定**"。

現在，您可以通過 Azure AD 應用程式代理在外部訪問 SharePoint 網站。

## <a name="step-3-configure-kerberos-constrained-delegation"></a>第 3 步：配置 Kerberos 限制委派

使用者最初將在 Azure AD 中進行身份驗證，然後通過使用 Kerberos 通過 Azure AD 代理連接器進行共用點驗證。 要允許連接器代表 Azure AD 使用者獲取 Kerberos 權杖，必須配置具有協定轉換的 Kerberos 限制委派 （KCD）。 要瞭解有關 KCD 的詳細資訊，請參閱[Kerberos 限制委派概述](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))。

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>為 SharePoint 服務帳戶設置 SPN

在本文中，內部 URL 為`https://sharepoint`，因此服務主體名稱 （SPN） 為`HTTP/sharepoint`。 您必須將這些值替換為與環境對應的值。
要為 SharePoint 應用程式池帳戶`HTTP/sharepoint``Contoso\spapppool`註冊 SPN，請作為域的管理員從命令提示符運行以下命令：

`setspn -S HTTP/sharepoint Contoso\spapppool`

該`Setspn`命令在添加 SPN 之前搜索它。 如果 SPN 已存在，則會看到**重複 SPN 值**錯誤。 在這種情況下，請考慮刪除現有的 SPN，如果它未在正確的應用程式池帳戶下設置。 您可以通過使用 -L 選項運行命令來`Setspn`驗證 SPN 是否已成功添加。 若要深入了解此命令，請參閱 [Setspn](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11))。

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>確保連接器受信任，以便委派到添加到 SharePoint 應用程式池帳戶的 SPN

設定 KCD，讓 Azure AD 應用程式 Proxy 服務可以將使用者識別委派給 SharePoint 應用程式集區帳戶。 啟用應用程式 Proxy 連接器來設定 KCD，以便為已在 Azure AD 中驗證的使用者擷取 Kerberos 票證。 然後，該伺服器將上下文傳遞給目標應用程式（本例中為 SharePoint）。

要配置 KCD，請按照以下步驟操作每個連接器電腦：

1. 以域管理員身份登錄到網域控制站，然後打開活動目錄使用者和電腦。
1. 查找運行 Azure AD 代理連接器的電腦。 在此示例中，它是 SharePoint 伺服器本身。
1. 按兩下電腦，然後選擇"**委派**"選項卡。
1. 請確保委派選項設置為 **"信任此電腦"，以便僅委派到指定的服務**。 然後，選取 [使用任何驗證通訊協定]****。
1. 選擇 **"添加**"按鈕，選擇 **"使用者"或"電腦**"，然後查找 SharePoint 應用程式池帳戶。 例如：`Contoso\spapppool`。
1. 在 SPN 的清單中，選取您稍早針對服務帳戶建立的 SPN。
1. 選擇 **"確定**"，然後再次選擇 **"確定"** 以保存更改。
  
   ![委派設定](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

現在，您可以使用外部 URL 登錄到 SharePoint 並使用 Azure 進行身份驗證。

## <a name="troubleshoot-sign-in-errors"></a>排除登錄錯誤

如果登錄到網站不起作用，您可以在連接器日誌中獲取有關此問題的詳細資訊：從運行連接器的電腦中打開事件檢視器，轉到**應用程式和服務日誌** > **Microsoft** > **AadApplicationProxy** > **連接器**，並檢查**管理**日誌。

## <a name="next-steps"></a>後續步驟

* [使用 Azure AD 應用程式 Proxy 中的自訂網域](application-proxy-configure-custom-domain.md)
* [瞭解 Azure AD 應用程式代理連接器](application-proxy-connectors.md)
