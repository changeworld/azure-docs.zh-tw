---
title: 瞭解和解決 Azure AD 應用程式 Proxy CORS 問題
description: 提供 Azure AD 應用程式 Proxy 中的 CORS 的瞭解，以及如何識別和解決 CORS 問題。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 2019802725e36c2400f57952fedf7af40877c8c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84759924"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>瞭解和解決 Azure Active Directory 應用程式 Proxy CORS 問題

[跨原始來源資源分享（CORS）](https://www.w3.org/TR/cors/)  有時可能會對您透過 Azure Active Directory 應用程式 Proxy 發佈的應用程式和 Api 帶來挑戰。 本文討論 Azure AD 應用程式 Proxy CORS 的問題和解決方案。

瀏覽器安全性通常會防止網頁對另一個網域發出 AJAX 要求。 這種限制稱為「*相同來源原則*」，可防止惡意網站從另一個網站讀取敏感性資料。 不過，有時候您可能會想要讓其他網站呼叫您的 Web API。 CORS 是一種 W3C 標準，可讓伺服器放寬相同的來源原則，並允許某些跨原始來源要求，同時拒絕其他要求。

## <a name="understand-and-identify-cors-issues"></a>瞭解和識別 CORS 問題

如果兩個 Url 具有相同的配置、主機和埠（[RFC 6454](https://tools.ietf.org/html/rfc6454)），就會有相同的來源，例如：

-   HTTP： \/ /contoso.com/foo.html
-   HTTP： \/ /contoso.com/bar.html

下列 Url 的來源與前兩個不同：

-   HTTP： \/ /contoso.net-不同的網域
-   HTTP： \/ /contoso.com:9000/foo.html-不同的埠
-   HTTPs： \/ /contoso.com/foo.html-不同的配置
-   HTTP： \/ /www.contoso.com/foo.html-不同的子域

相同來源原則會防止應用程式存取來自其他來源的資源，除非它們使用正確的存取控制標頭。 如果 CORS 標頭不存在或不正確，跨原始來源要求將會失敗。 

您可以使用瀏覽器偵錯工具來識別 CORS 問題：

1. 啟動瀏覽器並流覽至 web 應用程式。
1. 按**F12**鍵以顯示 [調試] 主控台。
1. 請嘗試重現交易，並查看主控台訊息。 CORS 違規會產生關於來源的主控台錯誤。

在下列螢幕擷取畫面中，選取 [**試試看**] 按鈕會造成 CORS 錯誤訊息，表示 \/ 在存取控制-允許來源標頭中找不到 HTTPs：/corswebclient-contoso.msappproxy.net。

![CORS 問題](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>應用程式 Proxy 的 CORS 挑戰

下列範例顯示一般 Azure AD 應用程式 Proxy CORS 案例。 內部伺服器裝載**CORSWebService** Web API 控制器，以及呼叫**CORSWebService**的**CORSWebClient** 。 有一個從**CORSWebClient**到**CORSWebService**的 AJAX 要求。

![內部部署相同來源要求](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient 應用程式會在您裝載于內部部署環境時運作，但無法在透過 Azure AD 應用程式 Proxy 發佈時載入或發生錯誤。 如果您透過應用程式 Proxy 將 CORSWebClient 和 CORSWebService 應用程式個別發佈為不同的應用程式，這兩個應用程式會裝載于不同的網域。 從 CORSWebClient 到 CORSWebService 的 AJAX 要求是一個跨原始來源要求，而它會失敗。

![應用程式 Proxy CORS 要求](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>應用程式 Proxy CORS 問題的解決方案

您可以透過數種方式的任何一種來解決上述的 CORS 問題。

### <a name="option-1-set-up-a-custom-domain"></a>選項1：設定自訂網域

使用 Azure AD 應用程式 Proxy[自訂網域](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains)，從相同的來源發佈，而不需要對應用程式來源、代碼或標頭進行任何變更。 

### <a name="option-2-publish-the-parent-directory"></a>選項2：發行上層目錄

發佈這兩個應用程式的上層目錄。 如果 web 伺服器上只有兩個應用程式，此解決方案的運作方式特別好。 您可以發佈通用上層目錄，而不是個別發佈每個應用程式，而這會產生相同的來源。

下列範例顯示 CORSWebClient 應用程式的入口網站 Azure AD 應用程式 Proxy 頁面。  當**內部 URL**設定為*contoso.com/CORSWebClient*時，應用程式無法對*contoso.com/CORSWebService*目錄進行成功的要求，因為它們是跨原始來源。 

![個別發行應用程式](./media/application-proxy-understand-cors-issues/image4.png)

相反地，請設定**內部 URL**來發佈父目錄，其中同時包含*CORSWebClient*和*CORSWebService*目錄：

![發佈上層目錄](./media/application-proxy-understand-cors-issues/image5.png)

產生的應用程式 Url 會有效地解決 CORS 問題：

- HTTPs： \/ /corswebclient-contoso.msappproxy.net/CORSWebService
- HTTPs： \/ /corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>選項3：更新 HTTP 標頭

在 web 服務上新增自訂 HTTP 回應標頭，以符合原始要求。 對於在 Internet Information Services （IIS）中執行的網站，請使用 IIS 管理員來修改標頭：

![在 IIS 管理員中新增自訂回應標頭](./media/application-proxy-understand-cors-issues/image6.png)

這種修改並不需要變更任何程式碼。 您可以在 Fiddler 追蹤中驗證它：

**貼上標頭新增**\
HTTP/1.1 200 正常 \
Cache-控制項：無快取 \
Pragma：無快取 \
內容類型：文字/純文字;字元集 = utf-8 \
過期時間：-1 \
Vary：接受-編碼 \
伺服器： Microsoft-IIS/8.5 Microsoft-HTTPAPI.DLL/2.0 \
**存取控制-允許-來源： HTTPs \: //corswebclient-contoso.msappproxy.net**\
X-AspNet-版本： 4.0.30319 \
X-支援： ASP.NET \
內容長度：17

### <a name="option-4-modify-the-app"></a>選項4：修改應用程式

您可以藉由新增具有適當值的存取控制-允許來源標頭，來變更您的應用程式以支援 CORS。 新增標頭的方式取決於應用程式的程式碼語言。 變更程式碼是最小的建議選項，因為它需要最多的工作。

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>選項5：延長存取權杖的存留期

無法解析某些 CORS 問題，例如當您的應用程式重新導向至*login.microsoftonline.com*進行驗證時，存取權杖會過期。 然後，CORS 呼叫會失敗。 此案例的因應措施是延長存取權杖的存留期，以避免在使用者的會話期間過期。 如需如何執行這項操作的詳細資訊，請參閱[Azure AD 中可設定的權杖存留期](../develop/active-directory-configurable-token-lifetimes.md)。

## <a name="see-also"></a>另請參閱
- [教學課程：新增內部部署應用程式以便透過 Azure Active Directory 中的應用程式 Proxy 進行遠端存取](application-proxy-add-on-premises-application.md) 
- [規劃 Azure AD 應用程式 Proxy 部署](application-proxy-deployment-plan.md) 
- [透過 Azure Active Directory 應用程式 Proxy 從遠端存取內部部署應用程式](application-proxy.md) 
