---
title: 啟用 SharePoint 的遠端存取-Azure AD 應用程式 Proxy
description: 涵蓋如何整合內部部署 SharePoint 伺服器與 Azure AD 應用程式 Proxy 的基本概念。
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c318c539b1c09761ed81e7602808e415fdaf8b80
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658174"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 啟用 SharePoint 的遠端存取

本逐步指南說明如何將內部部署 SharePoint 伺服器陣列與 Azure Active Directory (Azure AD) 應用程式 Proxy 整合。

## <a name="prerequisites"></a>先決條件

若要執行設定，您需要下列資源：
- SharePoint 2013 伺服器陣列或更新版本。
- 具有方案的 Azure AD 租使用者，其中包含應用程式 Proxy。 深入瞭解 [Azure AD 方案和定價](https://azure.microsoft.com/pricing/details/active-directory/)。
- Azure AD 租使用者中 [已驗證的自訂網域](../fundamentals/add-custom-domain.md) 。
- 內部部署 Active Directory 與 Azure AD Connect 同步處理，讓使用者可以登 [入 Azure](../hybrid/plan-connect-user-signin.md)。
- 在公司網域內的電腦上安裝並執行應用程式 Proxy 連接器。

使用應用程式 Proxy 設定 SharePoint 需要兩個 Url：
- 外部 URL，使用者可以看見並在 Azure AD 中判斷。 此 URL 可以使用自訂網域。 深入瞭解如何 [在 Azure AD 應用程式 Proxy 中使用自訂網域](application-proxy-configure-custom-domain.md)。
- 內部 URL，只有在公司網域內才知道，而且永遠不會直接使用。

> [!IMPORTANT]
> 若要確定連結已正確對應，請針對內部 URL 遵循下列建議：
> - 使用 HTTPS：
> - 請勿使用自訂埠。
> - 在公司網域名稱系統 (DNS) 中，建立主機 () 以指向 SharePoint WFE (或負載平衡器) ，而不是別名 (CName) 。

本文使用下列值：
- 內部 URL： `https://sharepoint`
- 外部 URL： `https://spsites-demo1984.msappproxy.net/`
- SharePoint web 應用程式的應用程式集區帳戶： `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>步驟1：在使用應用程式 Proxy 的 Azure AD 中設定應用程式

在此步驟中，您會在使用應用程式 Proxy 的 Azure Active Directory 租使用者中建立應用程式。 您可以設定外部 URL 並指定內部 URL，這兩個 URL 稍後會在 SharePoint 中使用。

1. 依照下列設定所述，建立應用程式。 如需逐步指示，請參閱[使用 Azure AD 應用程式 Proxy 發佈應用程式](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)。
   * **內部 url**：將在 sharepoint 中稍後設定的 SHAREPOINT 內部 url，例如 `https://sharepoint` 。
   * **預先驗證**： Azure Active Directory
   * **轉譯標頭中的 url**：否
   * **轉譯應用程式主體中的 url**：否

   ![將 SharePoint 發佈為應用程式](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. 發佈您的應用程式之後，請遵循下列步驟來設定單一登入設定：

   1. 在入口網站的應用程式頁面上，選取 [單一登入]。
   1. 針對 [單一登入模式]，選取 [整合式 Windows 驗證]。
   1. 將 [ **內部應用程式 SPN** ] 設定為您稍早設定的值。 在此範例中，值為 `HTTP/sharepoint` 。
   1. 在 [ **委派的登** 入身分識別] 下，為您的 Active Directory 樹系設定選取最適合的選項。 例如，如果您的樹系中有單一 Active Directory 網域，請選取 [內部 **部署 SAM 帳戶名稱** ] (，如下列螢幕擷取畫面所示) 。 但是，如果您的使用者與 SharePoint 和應用程式 Proxy 連接器伺服器不在相同的網域中，請選取 [內部 **部署使用者主體名稱** ] (不會顯示在螢幕擷取畫面) 中。

   ![設定 SSO 的整合式 Windows 驗證](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. 若要完成您的應用程式設定，請移至 [使用者和群組]區段，並指派使用者存取此應用程式。 

## <a name="step-2-configure-the-sharepoint-web-application"></a>步驟2：設定 SharePoint web 應用程式

您必須使用 Kerberos 和適當的備用存取對應來設定 SharePoint web 應用程式，才能在 Azure AD 應用程式 Proxy 中正確運作。 有兩個可能的選項：

- 建立新的 web 應用程式，並僅使用預設區域。 這是慣用的選項，因為它會提供最佳的 SharePoint 體驗 (例如，SharePoint 所產生之電子郵件警示中的連結一律指向預設區域) 。
- 擴充現有的 web 應用程式，以在非預設區域中設定 Kerberos。

> [!IMPORTANT]
> 無論使用哪一個區域，SharePoint web 應用程式的應用程式集區帳戶都必須是網域帳戶，Kerberos 才能正確運作。

### <a name="provision-the-sharepoint-web-application"></a>布建 SharePoint web 應用程式

- 如果您建立新的 web 應用程式，且只使用預設區域 (慣用選項) ：

    1. 啟動 **SharePoint 管理命令** 介面，並執行下列腳本：

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

    2. 開啟 [SharePoint 管理中心] 網站。
    1. 在 [系統設定] 下，選取 [設定備用存取對應]。 [ **備用存取對應集合** ] 方塊隨即開啟。
    1. 使用新的 web 應用程式篩選顯示內容，並確認您看到如下所示的內容：

       ![Web 應用程式的備用存取對應](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- 如果您將現有的 web 應用程式延伸到新的區域 (，以防無法使用預設的區域) ：

    1. 啟動 SharePoint 管理命令介面，並執行下列腳本：

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

    2. 開啟 [SharePoint 管理中心] 網站。
    1. 在 [系統設定] 下，選取 [設定備用存取對應]。 [ **備用存取對應集合** ] 方塊隨即開啟。
    1. 使用已擴充的 web 應用程式篩選顯示內容，並確認您看到如下所示的內容：

        ![擴充應用程式的備用存取對應](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>確定 SharePoint web 應用程式正在網域帳戶下執行

若要識別在 SharePoint web 應用程式中執行應用程式集區的帳戶，並確定它是網域帳戶，請遵循下列步驟：

1. 開啟 [SharePoint 管理中心] 網站。
1. 移至 [安全性]，然後選取 [設定服務帳戶]。
1. 選取 **Web 應用程式集區-YourWebApplicationName**。

   ![設定服務帳戶的選項](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. 確認 [ **選取此元件的帳戶** ] 會傳回網域帳戶，並記住它，因為在下一個步驟中需要此帳戶。

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>請確定已針對外部網路區域的 IIS 網站設定 HTTPS 憑證

因為內部 URL 使用 HTTPS 通訊協定 (`https://SharePoint/`) ，所以必須在 Internet Information Services (IIS) 網站上設定憑證。

1. 開啟 Windows PowerShell 主控台。
1. 執行下列腳本來產生自我簽署的憑證，並將它新增到電腦的「我的存放區」：

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > 自我簽署憑證僅適合供測試使用。 在生產環境中，強烈建議您改為使用由憑證授權單位單位發行的憑證。

1. 開啟 Internet Information Services Manager 主控台。
1. 在樹狀檢視中展開伺服器，展開 [**網站**] **，選取 [** **SharePoint-AAD Proxy** ] 網站，然後選取 [系結]。
1. 選取 [ **HTTPs** 系結]，然後選取 [ **編輯**]。
1. 在 [TLS/SSL 憑證] 欄位中，選擇 [ **SharePoint** 憑證]，然後選取 **[確定]**。

您現在可以透過 Azure AD 應用程式 Proxy 從外部存取 SharePoint 網站。

## <a name="step-3-configure-kerberos-constrained-delegation"></a>步驟3：設定 Kerberos 限制委派

使用者一開始會在 Azure AD 中進行驗證，然後透過 Azure AD Proxy 連接器使用 Kerberos 來驗證 SharePoint。 若要允許連接器代表 Azure AD 使用者取得 Kerberos 權杖，您必須使用通訊協定轉換 (KCD) 設定 Kerberos 限制委派。 若要深入瞭解 KCD，請參閱 [Kerberos 限制委派總覽](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))。

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>設定 SharePoint 服務帳戶的 SPN

在本文中，內部 URL 是 `https://sharepoint` ，因此)  (SPN 的服務主體名稱 `HTTP/sharepoint` 。 您必須將這些值取代為您環境的對應值。
若要註冊 `HTTP/sharepoint` SharePoint 應用程式集區帳戶的 SPN `Contoso\spapppool` ，請從命令提示字元以網域的系統管理員身分執行下列命令：

`setspn -S HTTP/sharepoint Contoso\spapppool`

此 `Setspn` 命令會先搜尋 SPN 再加以新增。 如果 SPN 已存在，您會看到 **重複的 Spn 值** 錯誤。 在此情況下，如果未在正確的應用程式集區帳戶下設定，請考慮移除現有的 SPN。 您可以執行 `Setspn` 命令並搭配-L 選項，確認已成功加入 SPN。 若要深入了解此命令，請參閱 [Setspn](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11))。

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>請確定連接器受信任，可委派給已新增至 SharePoint 應用程式集區帳戶的 SPN

設定 KCD，讓 Azure AD 應用程式 Proxy 服務可以將使用者識別委派給 SharePoint 應用程式集區帳戶。 啟用應用程式 Proxy 連接器來設定 KCD，以便為已在 Azure AD 中驗證的使用者擷取 Kerberos 票證。 然後，在此案例中，該伺服器會將內容傳遞至目標應用程式 (SharePoint) 。

若要設定 KCD，請針對每部連接器電腦遵循下列步驟：

1. 以網域系統管理員身分登入網域控制站，然後開啟 Active Directory 消費者和電腦。
1. 尋找正在執行 Azure AD Proxy 連接器的電腦。 在此範例中，這是 SharePoint 伺服器本身。
1. 按兩下該電腦，然後選取 [委派] 索引標籤。
1. 請確定委派選項設定為 **[信任這台電腦，但只委派指定的服務**]。 然後，選取 [ **使用任何驗證通訊協定**]。
1. 選取 [ **新增** ] 按鈕，選取 [ **使用者或電腦**]，然後找出 SharePoint 應用程式集區帳戶。 例如：`Contoso\spapppool`。
1. 在 SPN 的清單中，選取您稍早針對服務帳戶建立的 SPN。
1. 選取 **[確定]** ，然後再次選取 **[確定]** 以儲存您的變更。
  
   ![委派設定](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

您現在已準備好使用外部 URL 來登入 SharePoint，以及向 Azure 進行驗證。

## <a name="troubleshoot-sign-in-errors"></a>對登入錯誤進行疑難排解

如果登入網站無法運作，您可以從執行連接器的電腦取得連接器記錄中問題的詳細資訊：從執行連接器的電腦開啟 [事件檢視器]，移至 [**應用程式及服務記錄**  >  **Microsoft**  >  **microsoftaadapplicationproxy**  >  **連接器**]，然後檢查 **管理** 記錄檔。

## <a name="next-steps"></a>後續步驟

* [使用 Azure AD 應用程式 Proxy 中的自訂網域](application-proxy-configure-custom-domain.md)
* [了解 Azure AD 應用程式 Proxy 連接器](application-proxy-connectors.md)