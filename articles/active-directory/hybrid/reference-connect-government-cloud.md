---
title: Azure AD 連線:Azure 政府的混合識別注意事項
description: 部署 Azure AD 與政府雲連接的特殊注意事項。
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378921"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Azure 政府的混合識別注意事項 
以下文檔介紹了使用 Azure 政府雲實現混合環境的注意事項。  此資訊作為使用 Azure 政府雲的管理員和架構師的參考。  
> [!NOTE] 
> 為了將本地 AD 環境與 Azure 治理雲端整合,您需要升級到最新版本的[Azure AD 連線](https://www.microsoft.com/download/details.aspx?id=47594)。 

> [!NOTE] 
> 有關美國政府 DoD 終結點的完整清單,請參閱[文件](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>傳遞驗證 
為實現直通身份驗證 (PTA) 和 Azure 政府雲提供了以下資訊。

### <a name="allow-access-to-urls"></a>允許存取 URL  
在部署直通身份驗證代理之前,請驗證伺服器和 Azure AD 之間是否有防火牆。 如果您的防火牆或代理允許 DNS 白名單,請新增以下連線: 
> [!NOTE]
> 以下指南也適用於 Azure 政府環境安裝[應用程式代理連線器](https://aka.ms/whyappproxy)。

|URL |使用方式|
|-----|-----| 
|*.msappproxy.us *.servicebus.usgovcloudapi.net|代理和 Azure AD 雲服務之間的通訊 |
|mscrl.microsoft.us:80crl.microsoft.us:80 </br>ocsp.msocsp.us:80www.microsoft.us:80| 代理使用這些 URL 來驗證證書。| 
|login.windows.us secure.aadcdn.microsoftonline-p.com *.microsoftonline.us </br> *.microsoftonline-p.us </br>*.msauth.net </br> *.msauthimages.net </br>*.msecnd.net</br>*.msftauth.net </br>*.msftauthimages.net</br>*.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| 代理在註冊過程中使用這些 URL。| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>安裝 Azure 政府雲的代理 
要安裝 Azure 政府雲的代理,必須遵循以下特定步驟:在命令行終端中,導航到用於安裝代理的可執行檔所在的資料夾。 運行以下命令,該命令指定安裝適用於 Azure 政府。 

對於直通身份驗證: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

對應用程式代理:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>單一登入 
設置 Azure AD 連接伺服器:如果使用直通身份驗證作為登入方法,則無需進行其他先決條件檢查。 如果使用密碼哈希同步作為登入方法,並且 Azure AD 連接和 Azure AD 之間存在防火牆,請確保:
- 您使用 Azure AD Connect 1.1.644.0 或更新版本。 
- 如果您的防火牆或代理允許 DNS 白名單,請通過埠 443 將連接添加到 *.msapproxy.us URL。 如果沒有,則允許訪問 Azure 資料中心 IP 範圍,該範圍每周更新一次。 只有啟用此功能時，此必要條件才適用。 不需要實際的使用者登入。 

### <a name="rolling-out-seamless-sso"></a>推出無縫 SSO 
您可以使用下面提供的指示，為使用者逐步推出無縫 SSO。 首先,透過使用活動目錄中的群組原則將以下 Azure AD URL 新增到所有或選定的使用者的 Intranet 區域設定:https://autologon.microsoft.us 

此外,您需要啟用一個 Intranet 區域策略設置,稱為"通過組策略通過腳本允許更新到狀態列」。 瀏覽器注意事項 Mozilla Firefox(所有平臺)Mozilla Firefox 不會自動使用 Kerberos 身份驗證。 每個使用者都必須使用下列步驟，手動將 Azure AD URL 新增至其 Firefox 設定： 
1. 運行 Firefox 並在位址列中輸入約:配置。 關閉任何您看到的通知。 
2. 搜尋網路.協商-身份驗證.信任-uris 首選項。 此喜好設定列出 Firefox 進行 Kerberos 驗證的受信任網站。 
3. 右鍵按兩下並選擇" 
4. 輸入https://autologon.microsoft.us欄位。
5. 選擇"確定",然後重新打開瀏覽器。 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>基於鉻的微軟邊緣(所有平臺) 
如果已覆蓋環境中 `AuthNegotiateDelegateAllowlist`  `AuthServerAllowlist` 的或策略設定,請確保向它們添加 Azure ADhttps://autologon.microsoft.us)的 URL( 也將其添加到環境中)。 

### <a name="google-chrome-all-platforms"></a>Google Chrome (所有平台) 
如果已覆蓋環境中 `AuthNegotiateDelegateWhitelist`  `AuthServerWhitelist` 的或策略設定,請確保向它們添加 Azure ADhttps://autologon.microsoft.us)的 URL( 也將其添加到環境中)。 

## <a name="next-steps"></a>後續步驟
[直通身份驗證](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[單一登入](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
