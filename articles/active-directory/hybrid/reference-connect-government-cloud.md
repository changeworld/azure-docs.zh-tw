---
title: Azure AD Connect： Azure Government cloud 的混合式身分識別考慮
description: 使用 Azure Government 雲端部署 Azure AD Connect 的特殊考慮。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: acdc99ca50255bd9b75828f0a051f364c5218471
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115484"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Azure Government 雲端的混合式身分識別考慮

本文說明將混合式環境與 Microsoft Azure Government 雲端整合的考慮。 此資訊是提供給使用 Azure Government 雲端之系統管理員和架構設計人員的參考。

> [!NOTE]
> 若要將內部部署 Microsoft Azure Active Directory （Azure AD）環境與 Azure Government 雲端整合，您需要升級至最新版本的[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

如需美國政府部門國防部的完整清單，請參閱[檔](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints)。

## <a name="azure-ad-pass-through-authentication"></a>Azure AD 傳遞驗證

下列資訊說明傳遞驗證和 Azure Government 雲端的執行。

### <a name="allow-access-to-urls"></a>允許存取 URL

在您部署傳遞驗證代理程式之前，請先確認您的伺服器與 Azure AD 之間是否有防火牆存在。 如果您的防火牆或 proxy 允許「網域名稱系統」（DNS）已封鎖或「安全」程式，請新增下列連接。

> [!NOTE]
> 下列指導方針也適用于為 Azure Government 環境安裝[Azure AD 應用程式 Proxy 連接器](https://aka.ms/whyappproxy)。

|URL |使用方式|
|-----|-----|
|&#42;。 msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|代理程式會使用這些 Url 來與 Azure AD 雲端服務進行通訊。 |
|mscrl.microsoft.us:80 </br>crl.microsoft.us:80 </br>ocsp.msocsp.us:80 </br>www.microsoft.us:80| 代理程式會使用這些 Url 來驗證憑證。|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;。 microsoftonline.us </br>&#42;。 microsoftonline-p.us </br>&#42;。 msauth.net </br>&#42;。 msauthimages.net </br>&#42;。 msecnd.net</br>&#42;。 msftauth.net </br>&#42;。 msftauthimages.net</br>&#42;。 phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| 代理程式會在註冊過程中使用這些 Url。

### <a name="install-the-agent-for-the-azure-government-cloud"></a>安裝適用于 Azure Government 雲端的代理程式

請遵循下列步驟來安裝適用于 Azure Government 雲端的代理程式：

1. 在命令列終端機中，移至包含安裝代理程式之可執行檔的資料夾。
1. 執行下列命令，以指定安裝適用于 Azure Government。

   針對傳遞驗證：

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   針對應用程式 Proxy：

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>單一登入

### <a name="set-up-your-azure-ad-connect-server"></a>設定您的 Azure AD Connect 伺服器

如果您使用傳遞驗證作為登入方法，則不需要進行其他先決條件檢查。 如果您使用密碼雜湊同步處理作為登入方法，而且 Azure AD Connect 和 Azure AD 之間有防火牆，請確定：

- 您使用 Azure AD Connect 版1.1.644.0 或更新版本。
- 如果您的防火牆或 proxy 允許 DNS 封鎖或安全程式，請透過埠443將連線新增至 &#42; 的 msappproxy.us Url。

  如果不是，則允許存取 Azure 資料中心 IP 範圍（每週更新一次）。 只有當您啟用此功能時，此必要條件才適用。 不需要實際的使用者登入。

### <a name="roll-out-seamless-single-sign-on"></a>推出無縫單一登入

您可以使用下列指示，逐步推出 Azure AD 無縫單一登入給您的使用者。 首先，您可以使用 Active Directory 中的群組原則，將 Azure AD URL 新增 [https://autologon.microsoft.us](https://autologon.microsoft.us) 至所有或選取的使用者內部網路區域設定。

您也需要啟用內部網路區域原則設定 [**允許透過腳本更新狀態列] 群組原則**。

## <a name="browser-considerations"></a>瀏覽器考量

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (所有平台)

Mozilla Firefox 不會自動使用 Kerberos 驗證。 每位使用者都必須遵循下列步驟，手動將 Azure AD URL 新增至其 Firefox 設定：

1. 執行 Firefox，並在網址列中輸入 **about： config**   。 關閉您可能會看到的任何通知。
1. 搜尋 **網路. negotiate-auth. 信任 uri 的**   喜好設定。 此喜好設定會列出 Firefox 針對 Kerberos 驗證所信任的網站。
1. 以滑鼠右鍵按一下喜好設定名稱，然後選取 [ **修改**]。
1.  [**https://autologon.microsoft.us**](https://autologon.microsoft.us**)   在方塊中輸入。
1. 選取 **[確定]**   ，然後重新開啟瀏覽器。

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>以 Chromium 為基礎的 Microsoft Edge （所有平臺）

如果您已  `AuthNegotiateDelegateAllowlist`   `AuthServerAllowlist`   在環境中覆寫或原則設定，請務必將 Azure AD URL 加入 [https://autologon.microsoft.us](https://autologon.microsoft.us) 其中。

### <a name="google-chrome-all-platforms"></a>Google Chrome (所有平台)

如果您已  `AuthNegotiateDelegateWhitelist`   `AuthServerWhitelist`   在環境中覆寫或原則設定，請務必將 Azure AD URL 加入 [https://autologon.microsoft.us](https://autologon.microsoft.us) 其中。

## <a name="next-steps"></a>後續步驟

- [傳遞驗證](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [單一登入](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
