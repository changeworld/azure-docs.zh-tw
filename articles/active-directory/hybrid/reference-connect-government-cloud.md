---
title: Azure AD Connect： Azure Government 的混合式身分識別考慮
description: 部署 Azure AD Connect 與政府雲端的特殊考慮。
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
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378921"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Azure Government 的混合式身分識別考量 
下列檔說明使用 Azure Government cloud 來執行混合式環境的考慮。  此資訊會提供給使用 Azure Government 雲端的系統管理員和架構設計人員參考。  
> [!NOTE] 
> 若要將內部部署 AD 環境與 Azure Governemnt cloud 整合，您需要升級至最新版本的[Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。 

> [!NOTE] 
> 如需美國政府 DoD 端點的完整清單，請參閱[檔](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>傳遞驗證 
下列資訊是針對傳遞驗證（PTA）和 Azure Government 雲端的執行所提供。

### <a name="allow-access-to-urls"></a>允許存取 URL  
在部署傳遞驗證代理程式之前，請確認您的伺服器與 Azure AD 之間是否有防火牆。 如果您的防火牆或 proxy 允許 DNS 允許清單，請新增下列連接： 
> [!NOTE]
> 下列指導方針也適用于為 Azure Government 環境安裝[應用程式 Proxy 連接器](https://aka.ms/whyappproxy)。

|URL |使用方式|
|-----|-----| 
|*. msappproxy.us *. servicebus.usgovcloudapi.net|代理程式與 Azure AD 雲端服務之間的通訊 |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| 代理程式會使用這些 Url 來驗證憑證。| 
|login.windows.us secure.aadcdn.microsoftonline-p.com *. microsoftonline.us </br> *. microsoftonline-p.us </br>*. msauth.net </br> *. msauthimages.net </br>*. msecnd.net</br>*. msftauth.net </br>*. msftauthimages.net</br>*. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| 代理程式會在註冊過程中使用這些 Url。| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>安裝適用于 Azure Government 雲端的代理程式 
若要安裝 Azure Government 雲端的代理程式，您必須遵循下列特定步驟：在命令列終端機中，流覽至安裝代理程式可執行檔所在的資料夾。 執行下列命令，以指定 Azure Government 的安裝。 

針對傳遞驗證： 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

針對應用程式 Proxy：
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>單一登入 
設定您的 Azure AD Connect 伺服器：如果您使用傳遞驗證作為登入方法，則不需要額外的必要條件檢查。 如果您使用密碼雜湊同步處理作為登入方法，而且 Azure AD Connect 和 Azure AD 之間有防火牆，請確定：
- 您使用 Azure AD Connect 1.1.644.0 或更新版本。 
- 如果您的防火牆或 proxy 允許 DNS 允許清單，請透過埠443新增對 *. msapproxy.us Url 的連線。 如果不是，則允許存取 Azure 資料中心 IP 範圍（每週更新一次）。 只有啟用此功能時，此必要條件才適用。 不需要實際的使用者登入。 

### <a name="rolling-out-seamless-sso"></a>推出無縫 SSO 
您可以使用下面提供的指示，為使用者逐步推出無縫 SSO。 首先，您可以使用 Active Directory 中的群組原則，將下列 Azure AD URL 新增至所有或選取的使用者內部網路區域設定：https://autologon.microsoft.us 

此外，您還需要啟用名為 [允許透過腳本更新狀態列] 的內部網路區域原則設定群組原則。 瀏覽器考慮 Mozilla Firefox （所有平臺） Mozilla Firefox 不會自動使用 Kerberos 驗證。 每個使用者都必須使用下列步驟，手動將 Azure AD URL 新增至其 Firefox 設定： 
1. 執行 Firefox，並在網址列中輸入 about： config。 關閉任何您看到的通知。 
2. 搜尋網路. negotiate-auth. 信任 uri 的喜好設定。 此喜好設定列出 Firefox 進行 Kerberos 驗證的受信任網站。 
3. 按一下滑鼠右鍵，然後選取 [修改]。 
4. 在https://autologon.microsoft.us欄位中輸入。
5. 選取 [確定]，然後重新開啟瀏覽器。 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>以 Chromium 為基礎的 Microsoft Edge （所有平臺） 
如果您已在環境 `AuthNegotiateDelegateAllowlist` 中覆 `AuthServerAllowlist` 寫或原則設定，請確定您已新增 Azure AD 的 URL （https://autologon.microsoft.us)也就是）。 

### <a name="google-chrome-all-platforms"></a>Google Chrome (所有平台) 
如果您已在環境 `AuthNegotiateDelegateWhitelist` 中覆 `AuthServerWhitelist` 寫或原則設定，請確定您已新增 Azure AD 的 URL （https://autologon.microsoft.us)也就是）。 

## <a name="next-steps"></a>後續步驟
[傳遞驗證](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[單一登入](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
