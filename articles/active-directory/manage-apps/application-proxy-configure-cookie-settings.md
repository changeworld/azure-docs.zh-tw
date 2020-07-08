---
title: 應用程式 Proxy Cookie 設定 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) 有可透過「應用程式 Proxy」存取內部部署應用程式的存取和工作階段 Cookie。 在本文中，您將了解如何使用及設定 Cookie 設定。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 01/16/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 656841fc8e62e81318ffd568069c0664192b1747
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764888"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Azure Active Directory 中用於存取內部部署網站的 Cookie 設定

Azure Active Directory (Azure AD) 有可透過「應用程式 Proxy」存取內部部署應用程式的存取和工作階段 Cookie。 了解如何使用「應用程式 Proxy」Cookie 設定。 

## <a name="what-are-the-cookie-settings"></a>什麼是 Cookie 設定？

[應用程式 Proxy](application-proxy.md) 會使用下列存取和工作階段 Cookie 設定。

| Cookie 設定 | 預設 | 描述 | 建議 |
| -------------- | ------- | ----------- | --------------- |
| 使用僅限 HTTP Cookie | **否** | 使用 [是]**** 時，可讓「應用程式 Proxy」在 HTTP 回應標頭中包含 HTTPOnly 旗標。 此旗標提供額外的安全性優點，例如可防止用戶端指令碼處理 (CSS) 複製或修改 Cookie。<br></br><br></br>在我們支援僅限 HTTP 設定之前，應用程式 Proxy 已透過安全的 TLS 通道加密和傳輸 cookie，以防止修改。 | 為了獲得額外的安全性優點，請使用 [是]****。<br></br><br></br>針對不需要存取工作階段 Cookie 的用戶端或使用者代理程式，請使用 [否]****。 例如，針對透過「應用程式 Proxy」連線到「遠端桌面閘道伺服器」地 RDP 或 MTSC 用戶端，請使用 [否]****。|
| 使用安全的 Cookie | **否** | 使用 [是]**** 時，可讓「應用程式 Proxy」在 HTTP 回應標頭中包含 Secure 旗標。 安全 Cookie 可藉由透過 TLS 安全防護通道 (例如 HTTPS) 傳輸 Cookie 來提升安全性。 這可防止因以純文字傳輸 Cookie 而讓未經授權的對象得以看見 Cookie。 | 為了獲得額外的安全性優點，請使用 [是]****。|
| 使用永續性 Cookie | **否** | 使用 [是]**** 時，可讓「應用程式 Proxy」將其存取 Cookie 設定為在網頁瀏覽器關閉時不過期。 永續性會持續到存取權杖到期為止，或直到使用者手動刪除永續性 Cookie 為止。 | 由於讓使用者保持在已獲授權狀態會帶來安全性風險，因此請使用 [否]****。<br></br><br></br>建議只有針對無法在處理序之間共用 Cookie 的舊版應用程式，才使用 [是]****。 最好是更新您的應用程式以處理處理序之間的 Cookie 共用，而不要使用永續性 Cookie。 例如，您可能需要使用永續性 Cookie，才能允許使用者從 SharePoint 網站以總管檢視開啟 Office 文件。 在沒有永續性 Cookie 的情況下，如果未在瀏覽器、總管處理序及 Office 處理序之間共用存取 Cookie，此作業就會失敗。 |

## <a name="samesite-cookies"></a>SameSite Cookie
從版本 Chrome 80 開始，最後在利用 Chromium 的瀏覽器中，未指定[SameSite](https://web.dev/samesite-cookies-explained)屬性的 cookie 會被視為設定為**SameSite = 不嚴格**。 SameSite 屬性會宣告 cookie 應如何限制為相同網站的內容。 當設定為 [寬鬆] 時，cookie 只會傳送至相同的網站要求或最上層的導覽。 不過，應用程式 Proxy 需要在協力廠商內容中保留這些 cookie，才能讓使用者在其會話期間正確登入。 因此，我們會對應用程式 Proxy 存取和會話 cookie 進行更新，以避免對此變更造成不良影響。 這些更新包括：

* 將**SameSite**屬性設定為**None**。 這可讓您在協力廠商內容中正確傳送應用程式 Proxy 存取和會話 cookie。
* 將 [**使用安全 Cookie** ] 設定設為使用 [**是]** 做為預設值。 Chrome 也會要求 cookie 指定安全旗標，否則將會遭到拒絕。 這種變更會套用至透過應用程式 Proxy 發佈的所有現有應用程式。 請注意，應用程式 Proxy 存取 cookie 一律設定為安全的，而且只會透過 HTTPS 傳輸。 這種變更只會套用至會話 cookie。

這些對應用程式 Proxy cookie 的變更將在 Chrome 80 發行日期之前的幾個星期推出。

此外，如果您的後端應用程式具有需要在協力廠商內容中提供的 cookie，您必須將應用程式變更為針對這些 cookie 使用 SameSite = None，以明確加入宣告。 應用程式 Proxy 會將設定 Cookie 標頭轉譯為其 URL，並遵循後端應用程式所設定之 cookie 的設定。



## <a name="set-the-cookie-settings---azure-portal"></a>設定 Cookie 設定 - Azure 入口網站
使用 Azure 入口網站來設定 Cookie 設定：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 
2. 流覽至**Azure Active Directory**   >  **企業應用**程式] [   >  **所有應用程式**]。
3. 選取您要啟用 Cookie 設定的應用程式。
4. 按一下 [**應用程式 Proxy**]。
5. 在 [其他設定]**** 底下，將 Cookie 設定設為 [是]**** 或 [否]****。
6. 按一下 [儲存] 套用變更。 

## <a name="view-current-cookie-settings---powershell"></a>查看目前的 cookie 設定-PowerShell

若要查看應用程式目前的 cookie 設定，請使用此 PowerShell 命令：  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>設定 cookie 設定-PowerShell

在下列 PowerShell 命令中， ```<ObjectId>``` 是應用程式的 ObjectId。 

**僅限 Http Cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**安全 Cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**持續性 Cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
