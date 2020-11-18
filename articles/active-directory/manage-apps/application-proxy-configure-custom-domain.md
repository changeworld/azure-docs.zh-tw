---
title: Azure AD 應用程式 Proxy 中的自訂網域
description: 在 Azure AD 應用程式 Proxy 中設定和管理自訂網域。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: bef120e754c84798b2d1b48f4f00fbb8f5fb3c1d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656372"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 設定自訂網域

當您透過 Azure Active Directory 應用程式 Proxy 發佈應用程式時，您會為使用者建立外部 URL。 此 URL 會取得預設網域 *yourtenant.msappproxy.net*。 例如，如果您在名為 Contoso 的租用戶中發佈一個名為 Expenses 的應用程式，則外部 URL 為 *https:\//expenses-contoso.msappproxy.net*。 如果您想要使用自己的網域名稱 (而不是 msappproxy.net)，請為您的應用程式設定自訂網域。 

## <a name="benefits-of-custom-domains"></a>自訂網域的優點

建議您盡可能為您的應用程式設定自訂網域。 使用自訂網域的一些原因包括：

- 應用程式之間的連結甚至可以在公司網路外部運作。 在沒有自訂網域的情形下，如果您的應用程式具有硬式編碼的內部連結指向應用程式 Proxy 外部的目標，且連結無法從外部解析，則會中斷。 當您的內部和外部 URL 相同時，即可避免這個問題。 如果您無法使用自訂網域，請參閱[重新導向使用 Azure AD 應用程式 Proxy 發佈之應用程式的硬式編碼連結](./application-proxy-configure-hard-coded-link-translation.md)，以取得解決此問題的其他方式。 
  
- 您的使用者會有更輕鬆的體驗，因為他們可以從您的網路內部或外部透過相同的 URL 移至應用程式。 他們不需要得知不同的內部和外部 URL，或追蹤其目前的位置。 

- 您可控制您的品牌，並建立您想要的 URL。 自訂網域可協助您建立使用者的信心，因為使用者會看到並使用熟悉的名稱，而不是 msappproxy.net。

- 某些設定只會適用於自訂網域。 例如，您需要自訂網域，以供使用安全性聲明標記語言 (SAML) 的應用程式使用，例如當您使用 Active Directory 同盟服務 (AD FS) 但無法使用 WS-同盟時。 如需詳細資訊，請參閱[在應用程式 Proxy 中使用宣告感知應用程式](application-proxy-configure-for-claims-aware-applications.md)。 

如果您無法讓內部與外部 URL 相符，使用自訂網域並不重要，但您仍可利用其他優點。 

## <a name="dns-configuration-options"></a>DNS 組態選項

根據您的需求，有數個選項可用於設定您的 DNS 設定：

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>相同的內部和外部 URL，不同的內部和外部行為 

如果您不希望內部使用者透過應用程式 Proxy 導向，您可以設定「拆分式 DNS」。 拆分式 DNS 基礎結構會將內部主機導向內部網域名稱伺服器，將外部主機導向外部網域名稱伺服器，以便進行名稱解析。 

![拆分式 DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>不同的內部和外部 URL 

如果內部與外部 URL 不同，您就不需要設定拆分式行為，因為使用者路由是由 URL 決定。 在此情況下，您只能變更外部 DNS，並將外部 URL 路由傳送至應用程式 Proxy 端點。 

當您選取外部 URL 的自訂網域時，資訊列會顯示您需要新增至外部 DNS 提供者的 CNAME 項目。 您隨時都可以前往應用程式的 [應用程式 Proxy] 頁面來查看此資訊。

## <a name="set-up-and-use-custom-domains"></a>設定和使用自訂網域

若要將內部部署應用程式設定為使用自訂網域，您需要已經過驗證的 Azure Active Directory 自訂網域、自訂網域的 PFX 憑證，以及要設定的內部部署應用程式。 

### <a name="create-and-verify-a-custom-domain"></a>建立和驗證自訂網域

若要建立和驗證自訂網域：

1. 在 Azure Active Directory 中，從左側導覽選取 [自訂網域名稱]，然後選取 [新增自訂網域]。 
1. 輸入您的自訂網域名稱，然後選取 [新增網域]。 
1. 在網域頁面上，複製您網域的 TXT 記錄資訊。 
1. 移至您的網域註冊機構，並根據您複製的 DNS 資訊，為您的網域建立新的 TXT 記錄。
1. 註冊網域之後，在 Azure Active Directory 的網域頁面上，選取 [驗證]。 網域狀態變成 [已驗證] 後，您就可以在所有 Azure AD 設定 (包括應用程式 Proxy) 中使用該網域。 

如需詳細指示，請參閱[使用 Azure Active Directory 入口網站新增自訂網域名稱](../fundamentals/add-custom-domain.md)。

### <a name="configure-an-app-to-use-a-custom-domain"></a>將應用程式設定為使用自訂網域

若要使用自訂網域透過應用程式 Proxy 發佈您的應用程式：

1. 針對新的應用程式，在 Azure Active Directory 中，選取左側導覽中的 [企業應用程式]。 選取 [新增應用程式]。 在 [內部部署應用程式] 區段中，選取 [新增內部部署應用程式]。 
   
   對於已在 [企業應用程式] 中的應用程式，從清單中予以選取，然後在左側導覽中選取 [應用程式 Proxy]。 

2. 如果您要新增自己的內部部署應用程式，請在 [應用程式 Proxy 設定] 頁面上，輸入 [名稱]。

3.  在 [內部 URL] 欄位中，輸入您應用程式的內部 URL。
   
4. 在 [外部 URL] 欄位中，下拉清單並選取您要使用的自訂網域。
   
5. 選取 [新增]。
   
   ![選取自訂網域](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. 如果網域已經具有憑證，則 [憑證] 欄位會顯示憑證資訊。 否則，選取 [憑證] 欄位。
   
   ![按一下以上傳憑證](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. 在 [SSL 憑證] 頁面上，瀏覽並選取您的 PFX 憑證檔案。 輸入憑證的密碼，然後選取 [上傳憑證]。 如需憑證的詳細資訊，請參閱[自訂網域的憑證](#certificates-for-custom-domains)一節。 如果憑證無效或密碼有問題，您會看到錯誤訊息。 [應用程式 PROXY 常見問題](application-proxy-faq.md#application-configuration)包含一些您可以嘗試的疑難排解步驟。
   
   ![Upload Certificate](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > 自訂網域只需要上傳其憑證一次。 之後，當您使用其他應用程式的自訂網域時，系統就會自動套用所上傳的憑證。
   
8. 如果您已新增憑證，請在 [應用程式 Proxy] 頁面上，選取 [儲存]。 
   
9. 在 [應用程式 Proxy] 頁面的資訊列中，記下您需要新增至 DNS 區域的 CNAME 項目。 
   
   ![新增 CNAME DNS 項目](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. 遵循[使用 Azure 入口網站管理 DNS 記錄和記錄集](../../dns/dns-operations-recordsets-portal.md)中的指示，新增 DNS 記錄，以將新的外部 URL 重新導向至 msappproxy.net 網域。
   
11. 若要檢查 DNS 記錄是否已正確設定，請使用 [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) 命令來確認您的外部 URL 可觸達，且 msapproxy.net 網域會顯示為別名。

您的應用程式現已設定為使用自訂網域。 請務必先將使用者指派給您的應用程式，再進行測試或發行。 

若要變更應用程式的網域，請從應用程式的 [應用程式 Proxy] 頁面上 [外部 URL] 的下拉式清單中，選取不同的網域。 視需要為已更新的網域上傳憑證，並更新 DNS 記錄。 如果您在 [外部 URL] 的下拉式清單中看不到您想要的自訂網域，則可能未經驗證。

如需應用程式 Proxy 的詳細指示，請參閱[教學課程：新增內部部署應用程式以便透過 Azure Active Directory 中的應用程式 Proxy 進行遠端存取](application-proxy-add-on-premises-application.md)。

## <a name="certificates-for-custom-domains"></a>自訂網域的憑證

憑證會為您的自訂網域建立安全的 TLS 連線。 

### <a name="certificate-formats"></a>憑證格式

您必須使用 PFX 憑證，以確保包含所有必要的中繼憑證。 憑證必須包含私密金鑰。

支援最常見的憑證簽章方法，例如 (SAN) 的主體替代名稱。 

只要萬用字元符合外部 URL，便可使用萬用字元憑證。 您必須對[萬用字元應用程式](application-proxy-wildcard.md)使用萬用字元憑證。 如果您要使用憑證來同時存取子網域，則必須新增子網域萬用字元作為在相同憑證中的主體替代名稱。 例如， *\*.adventure-works.com* 的憑證將無法用於 *\*.apps.adventure-works.com*，除非您新增 *\*.apps.adventure-works.com* 作為主體替代名稱。 

如果您的用戶端裝置上已安裝憑證鏈，即可使用自己的公開金鑰基礎結構 (PKI) 所發出的憑證。 Intune 可以將這些憑證部署至受控裝置。 對於非受控裝置，您必須手動安裝這些憑證。 

我們不建議使用私人根 CA，因為私人根 CA 也必須推送至用戶端電腦，這可能會帶來許多挑戰。

### <a name="certificate-management"></a>憑證管理

所有憑證管理都是透過個別的應用程式頁面進行。 移至應用程式的 [應用程式 Proxy] 頁面，以存取 [憑證] 欄位。

上傳應用程式的憑證後，也會自動套用至使用相同憑證設定的 **新** 應用程式。 您必須為您租使用者中現有的應用程式重新上傳憑證。

您會在憑證到期時收到警告，告知您上傳另一個憑證。 如果已撤銷憑證，使用者在存取應用程式時可能會看到安全性警告。 若要更新應用程式的憑證，請瀏覽至應用程式的 [應用程式 Proxy] 頁面，選取 [憑證]，然後上傳新的憑證。 如果其他應用程式並未使用舊憑證，則系統會自動予以刪除。 

## <a name="next-steps"></a>後續步驟

* [啟用單一登入](application-proxy-configure-single-sign-on-with-kcd.md)以登入您使用 Azure AD 驗證發佈的應用程式。
* 已發佈雲端應用程式的[條件式存取](../conditional-access/concept-conditional-access-cloud-apps.md)。