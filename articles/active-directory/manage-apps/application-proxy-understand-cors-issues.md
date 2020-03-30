---
title: 瞭解並解決 Azure AD 應用程式代理 CORS 問題
description: 在 Azure AD 應用程式代理中提供對 CORS 的瞭解，以及如何識別和解決 CORS 問題。
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c49535ad11139ac5145d4f283374bf9cc6d71f52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025780"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>瞭解並解決 Azure 活動目錄應用程式代理 CORS 問題

[跨源資源分享 （CORS）](https://www.w3.org/TR/cors/) 有時會對通過 Azure 活動目錄應用程式代理髮布的應用和 API 帶來挑戰。 本文討論 Azure AD 應用程式代理 CORS 問題和解決方案。

瀏覽器安全性通常阻止網頁向另一個域發出 AJAX 請求。 此限制稱為*同源策略*，並防止惡意網站從其他網站讀取敏感性資料。 但是，有時您可能希望讓其他網站調用您的 Web API。 CORS 是一個 W3C 標準，它允許伺服器放鬆同源策略，並允許某些跨源請求，同時拒絕其他請求。

## <a name="understand-and-identify-cors-issues"></a>瞭解並識別 CORS 問題

如果兩個 URL 具有相同的方案、主機和埠[（RFC 6454），](https://tools.ietf.org/html/rfc6454)則它們具有相同的源，例如：

-   HTTP：\//contoso.com/foo.html
-   HTTP：\//contoso.com/bar.html

以下 URL 與前兩個 URL 的源不同：

-   HTTP：\//contoso.net - 不同的域
-   HTTP：\//contoso.com:9000/foo.html - 不同的埠
-   HTTPs：\//contoso.com/foo.html - 不同的方案
-   HTTP：\//www.contoso.com/foo.html - 不同的子域

同源策略阻止應用從其他源訪問資源，除非它們使用正確的存取控制標頭。 如果 CORS 標頭不存在或不正確，則跨源請求將失敗。 

您可以使用瀏覽器調試工具識別 CORS 問題：

1. 啟動瀏覽器並流覽到 Web 應用。
1. 按**F12**可啟動調試主控台。
1. 嘗試重現事務，並查看主控台消息。 CORS 衝突會產生有關源的主控台錯誤。

在下面的螢幕截圖中，選擇 **"試用"** 按鈕會導致 CORS 錯誤訊息 HTTPs：\//corswebclient-contoso.msappproxy.net 在訪問-控制-允許源標頭中找不到。

![CORS 問題](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>應用程式代理的 CORS 挑戰

下面的示例顯示了典型的 Azure AD 應用程式代理 CORS 方案。 內部伺服器託管一個**CORSWeb服務WebAPI**控制器，以及一個調用**CORSWeb服務的****CORSWeb用戶端**。 有一個AJAX請求從**CORSWeb用戶端****到CORSWeb服務**。

![本地同源請求](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient 應用在本地託管時有效，但在通過 Azure AD 應用程式代理髮布時無法載入或錯誤。 如果您通過應用程式代理將 CORSWebClient 和 CORSWeb 服務應用單獨發佈為不同的應用，則這兩個應用將託管在不同的域中。 從 CORSWebClient 到 CORSWeb 服務的 AJAX 請求是一個跨源請求，並且失敗。

![應用程式代理 CORS 請求](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>應用程式代理 CORS 問題的解決方案

您可以通過多種方式中的任何一種方式解決前面的 CORS 問題。

### <a name="option-1-set-up-a-custom-domain"></a>選項 1：設置自訂域

使用 Azure AD 應用程式代理[自訂域](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains)從同一源發佈，而無需對應用源、代碼或標頭進行任何更改。 

### <a name="option-2-publish-the-parent-directory"></a>選項 2：發佈父目錄

發佈兩個應用的父目錄。 如果 Web 服務器上只有兩個應用，則此解決方案效果特別好。 您可以發佈公共父目錄，而不是單獨發佈每個應用，這將導致相同的源。

以下示例顯示 CORSWebClient 應用的門戶 Azure AD 應用程式代理頁。  當**內部 URL**設置為*contoso.com/CORSWebClient*時，應用無法成功向*contoso.com/CORSWebService*目錄發出請求，因為它們是跨源的。 

![單獨發佈應用](./media/application-proxy-understand-cors-issues/image4.png)

相反，將**內部 URL**設置為發佈父目錄，其中包括*CORSWebClient*和*CORSWeb 服務*目錄：

![發佈父目錄](./media/application-proxy-understand-cors-issues/image5.png)

生成的應用 URL 可有效解決 CORS 問題：

- HTTPs：\//corswebclient-contoso.msappproxy.net/CORSWebService
- HTTPs：\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>選項 3：更新 HTTP 標頭

在 Web 服務上添加自訂 HTTP 回應標頭以匹配源請求。 對於在互聯網資訊服務 （IIS） 中運行的網站，請使用 IIS 管理器修改標頭：

![在 IIS 管理器中添加自訂回應標頭](./media/application-proxy-understand-cors-issues/image6.png)

此修改不需要任何代碼更改。 您可以在 Fiddler 跟蹤中驗證它：

**過帳標題添加**\
HTTP/1.1 200 OK。
緩存控制：無緩存*
普拉格瑪：無緩存*
內容類型：文本/純;字元_utf-8_
過期： -1*
變化：接受編碼*
伺服器：微軟-IIS/8.5 微軟-HTTPAPI/2.0*
**存取控制-允許-原點：HTTPs\:/corswebclient-contoso.msappproxy.net**\
X-AspNet 版本：4.0.30319*
X 供電：ASP.NET*
內容長度： 17

### <a name="option-4-modify-the-app"></a>選項 4：修改應用

您可以通過添加具有適當值的存取控制允許源標頭來更改應用以支援 CORS。 添加標頭的方式取決於應用的代碼語言。 更改代碼是最不推薦的選項，因為它需要付出最大的努力。

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>選項 5：延長訪問權杖的存留期

某些 CORS 問題無法解決，例如，當應用重定向到*login.microsoftonline.com*進行身份驗證時，訪問權杖將過期。 然後，CORS 調用將失敗。 此方案的解決方法是延長訪問權杖的存留期，以防止它在使用者會話期間過期。 有關如何執行此操作的詳細資訊，請參閱 Azure AD[中的可配置權杖存留期](../develop/active-directory-configurable-token-lifetimes.md)。

## <a name="see-also"></a>另請參閱
- [教程：在 Azure 活動目錄中添加本地應用程式，以便通過應用程式代理進行遠端存取](application-proxy-add-on-premises-application.md) 
- [規劃 Azure AD 應用程式代理部署](application-proxy-deployment-plan.md) 
- [通過 Azure 活動目錄應用程式代理遠端存取本地應用程式](application-proxy.md) 
