---
title: 瞭解並解決 Azure AD 應用程式 Proxy CORS 問題
description: 提供對 Azure AD 應用程式 Proxy 中的 CORS 的瞭解，以及如何識別和解決 CORS 問題。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84759924"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>瞭解並解決 Azure Active Directory 應用程式 Proxy CORS 問題

[跨原始資源分享 (CORS) ](https://www.w3.org/TR/cors/)  有時可能會對您透過 Azure Active Directory 應用程式 Proxy 發佈的應用程式和 Api 帶來挑戰。 本文討論 Azure AD 的應用程式 Proxy CORS 問題和解決方案。

瀏覽器安全性通常會防止網頁對另一個網域提出 AJAX 要求。 這項限制稱為 *相同來源原則*，可防止惡意網站從另一個網站讀取敏感性資料。 不過，有時候您可能會想要讓其他網站呼叫您的 web API。 CORS 是一種 W3C 標準，可讓伺服器放寬相同來源原則，並允許某些跨原始來源的要求，同時拒絕其他要求。

## <a name="understand-and-identify-cors-issues"></a>瞭解並識別 CORS 問題

如果兩個 Url 具有相同的配置、主機和埠 ([RFC 6454](https://tools.ietf.org/html/rfc6454)) ，其來源相同，例如：

-   HTTP： \/ /contoso.com/foo.html
-   HTTP： \/ /contoso.com/bar.html

下列 Url 的來源與前兩個不同：

-   HTTP： \/ /contoso.net-不同的網域
-   HTTP： \/ /contoso.com:9000/foo.html-不同的埠
-   HTTPs： \/ /contoso.com/foo.html-不同的配置
-   HTTP： \/ /www.contoso.com/foo.html-不同的子域

相同來源原則會防止應用程式從其他來源存取資源，除非它們使用正確的存取控制標頭。 如果 CORS 標頭不存在或不正確，跨原始來源的要求就會失敗。 

您可以使用瀏覽器偵錯工具來識別 CORS 問題：

1. 啟動瀏覽器並流覽至 web 應用程式。
1. 按 **F12** 以顯示 debug 主控台。
1. 嘗試重現交易，並檢查主控台訊息。 CORS 違規會產生關於來源的主控台錯誤。

在下列螢幕擷取畫面中，選取 [ **試試看** ] 按鈕會導致 CORS 錯誤訊息，指出 \/ 在存取控制-允許來源標頭中找不到 HTTPs：/corswebclient-contoso.msappproxy.net。

![CORS 問題](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>應用程式 Proxy 的 CORS 挑戰

下列範例顯示典型的 Azure AD 應用程式 Proxy CORS 案例。 內部伺服器會裝載**CORSWebService** web API 控制器，以及呼叫**CORSWebService**的**CORSWebClient** 。 有一個從 **CORSWebClient** 到 **CORSWebService**的 AJAX 要求。

![內部部署的相同原始要求](./media/application-proxy-understand-cors-issues/image1.png)

當您在內部部署裝載應用程式時，CORSWebClient 應用程式可運作，但在透過 Azure AD 應用程式 Proxy 發佈時，可能無法載入或發生錯誤。 如果您透過應用程式 Proxy 將 CORSWebClient 和 CORSWebService 應用程式分別發佈為不同的應用程式，則兩個應用程式會裝載于不同的網域。 從 CORSWebClient 到 CORSWebService 的 AJAX 要求是一個跨原始來源的要求，它會失敗。

![應用程式 Proxy CORS 要求](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>應用程式 Proxy CORS 問題的解決方案

您可以透過數種方式來解決上述的 CORS 問題。

### <a name="option-1-set-up-a-custom-domain"></a>選項1：設定自訂網域

使用 Azure AD 應用程式 Proxy [自訂網域](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) 從相同的來源發佈，而不需要對應用程式來源、程式碼或標頭進行任何變更。 

### <a name="option-2-publish-the-parent-directory"></a>選項2：發行上層目錄

發佈這兩個應用程式的父目錄。 如果 web 伺服器上只有兩個應用程式，則此解決方案的運作效果特別好。 您可以發佈共同的父目錄，而不是個別發佈每個應用程式，這會導致相同的來源。

下列範例顯示 CORSWebClient 應用程式的入口網站 Azure AD 應用程式 Proxy] 頁面。  當 **內部 URL** 設定為 *contoso.com/CORSWebClient*時，應用程式無法對 *contoso.com/CORSWebService* 目錄提出成功的要求，因為它們是跨原始來源。 

![個別發佈應用程式](./media/application-proxy-understand-cors-issues/image4.png)

相反地，請設定 **內部 URL** 以發佈上層目錄，其中包含 *CORSWebClient* 和 *CORSWebService* 目錄：

![發佈上層目錄](./media/application-proxy-understand-cors-issues/image5.png)

產生的應用程式 Url 會有效地解決 CORS 問題：

- HTTPs： \/ /corswebclient-contoso.msappproxy.net/CORSWebService
- HTTPs： \/ /corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>選項3：更新 HTTP 標頭

在 web 服務上加入自訂 HTTP 回應標頭，以符合原始要求。 針對 Internet Information Services (IIS) 中執行的網站，請使用 IIS 管理員來修改標頭：

![在 IIS 管理員中新增自訂回應標頭](./media/application-proxy-understand-cors-issues/image6.png)

這種修改不需要變更任何程式碼。 您可以在 Fiddler 追蹤中確認它：

**貼上標頭新增**\
HTTP/1.1 200 確定 \
快取-控制：無快取 \
Pragma： no-cache \
Content-type： text/純文字;字元集 = utf-8 \
到期日：-1 \
Vary：接受編碼 \
伺服器： Microsoft-IIS/8.5 Microsoft-HTTPAPI.DLL/2.0 \
**存取控制-允許-來源： HTTPs \: //corswebclient-contoso.msappproxy.net**\
X-AspNet-Version： 4.0.30319 \
X-支援： ASP.NET \
內容-長度：17

### <a name="option-4-modify-the-app"></a>選項4：修改應用程式

您可以藉由新增具有適當值的存取控制-允許來源標頭來變更您的應用程式，以支援 CORS。 新增標頭的方式會視應用程式的程式碼語言而定。 變更程式碼是最不建議的選項，因為它需要最多的工作。

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>選項5：延長存取權杖的存留期

無法解決某些 CORS 問題，例如，當您的應用程式重新導向至 *login.microsoftonline.com* 進行驗證，且存取權杖到期時。 CORS 呼叫會失敗。 此案例的因應措施是延長存取權杖的存留期，以防止它在使用者會話期間過期。 如需如何進行這項操作的詳細資訊，請參閱 [Azure AD 中的可設定權杖存留期](../develop/active-directory-configurable-token-lifetimes.md)。

## <a name="see-also"></a>另請參閱
- [教學課程：新增內部部署應用程式以便透過 Azure Active Directory 中的應用程式 Proxy 進行遠端存取](application-proxy-add-on-premises-application.md) 
- [規劃 Azure AD 應用程式 Proxy 部署](application-proxy-deployment-plan.md) 
- [透過 Azure Active Directory 應用程式 Proxy 從遠端存取內部部署應用程式](application-proxy.md) 
