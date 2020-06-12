---
title: Azure AD Connect：Azure Government 雲端的混合式身分識別考量
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
ms.openlocfilehash: 217cf8822fcd8ef515ac9ce2dacdac3682e5fd12
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680173"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Azure Government 雲端的混合式身分識別考量

本文說明整合混合式環境與 Microsoft Azure Government 雲端的考量。 此資訊會提供給使用 Azure Government 雲端的系統管理員和架構設計人員參考。

> [!NOTE]
> 若要整合內部部署 Microsoft Azure Active Directory (Azure AD) 環境與 Azure Government 雲端，您需要升級至最新版的 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)。

如需美國國防部的完整端點清單，請參閱這份[文件](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints)。

## <a name="azure-ad-pass-through-authentication"></a>Azure AD 傳遞驗證

下列資訊說明如何實作傳遞驗證和 Azure Government 雲端。

### <a name="allow-access-to-urls"></a>允許存取 URL

在您部署傳遞驗證代理程式之前，請先確認您的伺服器與 Azure AD 之間是否有防火牆存在。 如果您的防火牆或 Proxy 允許網域名稱系統 (DNS) 封鎖或安全的程式，請新增下列連線。

> [!NOTE]
> 下列指引也適用於針對 Azure Government 環境安裝 [Azure AD 應用程式 Proxy 連接器](https://aka.ms/whyappproxy)。

|URL |使用方式|
|-----|-----|
|&#42;.msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|代理程式會使用這些 URL 來與 Azure AD 雲端服務進行通訊。 |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| 代理程式會使用這些 URL 來驗證憑證。|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;.microsoftonline.us </br>&#42;.microsoftonline-p.us </br>&#42;.msauth.net </br>&#42;.msauthimages.net </br>&#42;.msecnd.net</br>&#42;.msftauth.net </br>&#42;.msftauthimages.net</br>&#42;.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| 代理程式會在註冊過程中使用這些 URL。

### <a name="install-the-agent-for-the-azure-government-cloud"></a>安裝適用於 Azure Government 雲端的代理程式

請遵循下列步驟來安裝適用於 Azure Government 雲端的代理程式：

1. 在命令列終端機中，移至包含可執行檔的資料夾，而該可執行檔用於安裝代理程式。
1. 執行下列命令，以指定安裝適用於 Azure Government。

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

如果您使用傳遞驗證作為登入方法，則不需要其他必要條件檢查。 如果您使用密碼雜湊同步處理作為登入方法，而且 Azure AD Connect 與 Azure AD 之間有防火牆，請確定︰

- 您使用 Azure AD Connect 1.1.644.0 版或更新版本。
- 如果您的防火牆或 Proxy 允許 DNS 封鎖或安全的程式，請新增透過連接埠 443 的 &#42;.msappproxy.us 連線。

  如果不允許建立，請允許存取每週更新的 Azure 資料中心 IP 範圍。 只有啟用此功能時，才適用此必要條件。 不需要實際的使用者登入。

### <a name="roll-out-seamless-single-sign-on"></a>推出無縫單一登入

您可以使用下列指示，逐步向您的使用者推出 Azure AD 無縫單一登入。 您一開始可使用 Active Directory 中的群組原則，將 Azure AD URL `https://autologon.microsoft.us` 新增至所有或已選取之使用者的內部網路區域設定。

您也需要透過群組原則，啟用內部網路區域原則設定 [允許透過指令碼更新狀態列]。

## <a name="browser-considerations"></a>瀏覽器考量

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (所有平台)

Mozilla Firefox 不會自動使用 Kerberos 驗證。 每個使用者都必須依照下列步驟，手動將 Azure AD URL 新增至其 Firefox 設定：

1. 執行 Firefox 並在網址列中輸入  **about:config** 。 關閉任何您看到的通知。
1. 搜尋  **network.negotiate-auth.trusted-uris** 喜好設定。 此喜好設定列出 Firefox 進行 Kerberos 驗證時信任的網站。
1. 以滑鼠右鍵按一下喜好設定名稱，然後選取 [修改] **** 。
1. 在方塊中輸入 `https://autologon.microsoft.us`。
1. 選取 [確定] ****  ，然後重新開啟瀏覽器。

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>以 Chromium 為基礎的 Microsoft Edge (所有平台)

如果您已覆寫環境中的  `AuthNegotiateDelegateAllowlist` 或 `AuthServerAllowlist` 原則設定，請確保在其中新增 Azure AD URL `https://autologon.microsoft.us`。

### <a name="google-chrome-all-platforms"></a>Google Chrome (所有平台)

如果您已覆寫環境中的  `AuthNegotiateDelegateWhitelist` 或 `AuthServerWhitelist`  原則設定，請確保在其中新增 Azure AD URL `https://autologon.microsoft.us`。

## <a name="next-steps"></a>後續步驟

- [傳遞驗證](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [單一登入](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
