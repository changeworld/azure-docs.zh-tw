---
title: 應用程式 Proxy Cookie 設定 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) 有可透過「應用程式 Proxy」存取內部部署應用程式的存取和工作階段 Cookie。 在本文中，您將了解如何使用及設定 Cookie 設定。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcedb24a0efdbabaaef150fc3d5aff07d210ce23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481359"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Azure Active Directory 中用於存取內部部署網站的 Cookie 設定

Azure Active Directory (Azure AD) 有可透過「應用程式 Proxy」存取內部部署應用程式的存取和工作階段 Cookie。 了解如何使用「應用程式 Proxy」Cookie 設定。 

## <a name="what-are-the-cookie-settings"></a>什麼是 Cookie 設定？

[應用程式 Proxy](application-proxy.md) 會使用下列存取和工作階段 Cookie 設定。

| Cookie 設定 | 預設 | 描述 | 建議 |
| -------------- | ------- | ----------- | --------------- |
| 使用僅限 HTTP Cookie | **否** | 使用 [是]**** 時，可讓「應用程式 Proxy」在 HTTP 回應標頭中包含 HTTPOnly 旗標。 此旗標提供額外的安全性優點，例如可防止用戶端指令碼處理 (CSS) 複製或修改 Cookie。<br></br><br></br>在我們支援僅 HTTP 設置之前，應用程式代理通過安全的 TLS 通道加密和傳輸 Cookie，以防止修改。 | 為了獲得額外的安全性優點，請使用 [是]****。<br></br><br></br>針對不需要存取工作階段 Cookie 的用戶端或使用者代理程式，請使用 [否]****。 例如，針對透過「應用程式 Proxy」連線到「遠端桌面閘道伺服器」地 RDP 或 MTSC 用戶端，請使用 [否]****。|
| 使用安全的 Cookie | **否** | 使用 [是]**** 時，可讓「應用程式 Proxy」在 HTTP 回應標頭中包含 Secure 旗標。 安全 Cookie 可藉由透過 TLS 安全防護通道 (例如 HTTPS) 傳輸 Cookie 來提升安全性。 這可防止因以純文字傳輸 Cookie 而讓未經授權的對象得以看見 Cookie。 | 為了獲得額外的安全性優點，請使用 [是]****。|
| 使用永續性 Cookie | **否** | 使用 [是]**** 時，可讓「應用程式 Proxy」將其存取 Cookie 設定為在網頁瀏覽器關閉時不過期。 永續性會持續到存取權杖到期為止，或直到使用者手動刪除永續性 Cookie 為止。 | 由於讓使用者保持在已獲授權狀態會帶來安全性風險，因此請使用 [否]****。<br></br><br></br>建議只有針對無法在處理序之間共用 Cookie 的舊版應用程式，才使用 [是]****。 最好是更新您的應用程式以處理處理序之間的 Cookie 共用，而不要使用永續性 Cookie。 例如，您可能需要使用永續性 Cookie，才能允許使用者從 SharePoint 網站以總管檢視開啟 Office 文件。 在沒有永續性 Cookie 的情況下，如果未在瀏覽器、總管處理序及 Office 處理序之間共用存取 Cookie，此作業就會失敗。 |

## <a name="samesite-cookies"></a>同一網站餅乾
從 Chrome 80 版本開始，最終在瀏覽器中利用鉻，不指定[SameSite](https://web.dev/samesite-cookies-explained)屬性的 Cookie 將被視為"同**一網站_Lax**"。 SameSite 屬性聲明如何將 Cookie 限制為同一網站上下文。 當設置為 Lax 時，Cookie 只能發送到同一網站請求或頂級導航。 但是，應用程式代理要求在協力廠商上下文中保留這些 Cookie，以便在使用者會話期間保持正確登錄。 因此，我們正在更新應用程式代理訪問和會話 Cookie，以避免此更改的不利影響。 更新包括：

* 將**同一網站**屬性設置為 **"無**"。 這允許在協力廠商上下文中正確發送應用程式代理訪問和會話 Cookie。
* 將 **"使用安全 Cookie"** 設置設置為預設使用 **"是**"。 Chrome 還要求 Cookie 指定安全標誌，否則將被拒絕。 此更改將應用於通過應用程式代理髮布的所有現有應用程式。 請注意，應用程式代理訪問 Cookie 始終設置為"安全"，並且僅通過 HTTPS 傳輸。 此更改僅適用于會話 Cookie。

對應用程式代理 Cookie 的這些更改將在 Chrome 80 發佈日期之前的幾周內推出。

此外，如果您的後端應用程式具有需要在協力廠商上下文中可用的 Cookie，則必須通過將應用程式更改為對這些 Cookie 使用 SameSite_None 來明確加入宣告。 應用程式代理將 Set-Cookie 標頭轉換為其 URLS，並將尊重後端應用程式設定的這些 Cookie 的設置。



## <a name="set-the-cookie-settings---azure-portal"></a>設定 Cookie 設定 - Azure 入口網站
使用 Azure 入口網站來設定 Cookie 設定：

1. 登錄到 Azure[門戶](https://portal.azure.com)。 
2. 導航到**Azure 活動目錄** > **企業應用程式** > **所有應用程式**。
3. 選取您要啟用 Cookie 設定的應用程式。
4. 按一下**應用程式代理**。
5. 在 [其他設定]**** 底下，將 Cookie 設定設為 [是]**** 或 [否]****。
6. 按一下 [儲存]**** 套用變更。 

## <a name="view-current-cookie-settings---powershell"></a>查看當前 Cookie 設置 - 電源外殼

要查看應用程式的當前 Cookie 設置，請使用此 PowerShell 命令：  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>設置 Cookie 設置 - 電源外殼

在以下 PowerShell 命令```<ObjectId>```中，是應用程式的 ObjectId。 

**僅 Http-只 Cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**安全餅乾**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**持久餅乾**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
