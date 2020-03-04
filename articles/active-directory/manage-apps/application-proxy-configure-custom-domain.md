---
title: Azure AD 應用程式 Proxy 中的自訂網域 | Microsoft Docs
description: 設定和管理 Azure AD 應用程式 Proxy 中的自訂網域。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80f8d66795971c6a5c84be7088387e63d7acd7a7
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78248691"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 設定自訂網域

當您透過 Azure Active Directory 應用程式 Proxy 發行應用程式時，會為您的使用者建立外部 URL。 此 URL 會取得預設網域 *yourtenant.msappproxy.net*。 例如，如果您在名為*Contoso*的租使用者中發佈名為「*費用*」的應用程式，則外部 URL 會是*HTTPs：\//expenses-contoso.msappproxy.net*。 如果您想要使用您自己的功能變數名稱，而不是*msappproxy.net*，可以為您的應用程式設定自訂網域。 

## <a name="benefits-of-custom-domains"></a>自訂網域的優點

建議您盡可能為您的應用程式設定自訂網域。 使用自訂網域的一些原因包括：

- 應用程式之間的連結甚至可以在公司網路外部執行。 如果沒有自訂網域，如果您的應用程式具有硬式編碼的內部連結，指向應用程式 Proxy 外部的目標，而且連結無法從外部解析，則會中斷。 當您的內部和外部 Url 相同時，您可以避免這個問題。 如果您無法使用自訂網域，請參閱重新[導向使用 Azure AD 應用程式 Proxy 發佈之應用程式的硬式編碼連結](../application-proxy-link-translation.md)，以瞭解解決此問題的其他方式。 
  
- 您的使用者將會有更簡單的體驗，因為他們可以從您的網路內部或外部取得具有相同 URL 的應用程式。 他們不需要學習不同的內部和外部 Url，或追蹤其目前的位置。 

- 您可以控制您的商標，並建立您想要的 Url。 自訂網域可協助您建立使用者的信心，因為使用者會看到並使用熟悉的名稱，而不是*msappproxy.net*。

- 某些設定只會使用自訂網域。 例如，您需要使用安全性聲明標記語言（SAML）之應用程式的自訂網域，例如當您使用 Active Directory 同盟服務（AD FS）但無法使用 WS-同盟時。 如需詳細資訊，請參閱[在應用程式 Proxy 中使用宣告感知應用程式](application-proxy-configure-for-claims-aware-applications.md)。 

如果您無法讓內部和外部 Url 相符，使用自訂網域並不重要，但您仍然可以利用其他優點。 

## <a name="dns-configuration-options"></a>DNS 設定選項

根據您的需求，有數個選項可設定您的 DNS 設定：

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>相同的內部和外部 URL，不同的內部和外部行為 

如果您不想要讓內部使用者通過應用程式 Proxy，您可以設定*分割大腦的 DNS*。 分割 DNS 基礎結構會將內部主機導向內部功能變數名稱伺服器，以及外部主機到外部功能變數名稱伺服器，以進行名稱解析。 

![拆分式 DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>不同的內部和外部 Url 

如果內部和外部 Url 不同，您就不需要設定分割大腦行為，因為使用者路由是由 URL 決定。 在此情況下，您只會變更外部 DNS，並將外部 URL 路由傳送至應用程式 Proxy 端點。 

當您選取外部 URL 的自訂網域時，資訊列會顯示您需要新增至外部 DNS 提供者的 CNAME 專案。 您一律可以前往應用程式的 [**應用程式 proxy** ] 頁面來查看這項資訊。

## <a name="set-up-and-use-custom-domains"></a>設定和使用自訂網域

若要將內部部署應用程式設定為使用自訂網域，您需要已驗證的 Azure Active Directory 自訂網域、自訂網域的 PFX 憑證，以及要設定的內部部署應用程式。 

### <a name="create-and-verify-a-custom-domain"></a>建立和驗證自訂網域

若要建立並驗證自訂網域：

1. 在 Azure Active Directory 中，選取左側導覽中的 [**自訂功能變數名稱**]，然後選取 [**新增自訂網域**]。 
1. 輸入您的自訂功能變數名稱，然後選取 [**新增網域**]。 
1. 在 [網域] 頁面上，複製您網域的 TXT 記錄資訊。 
1. 移至您的網域註冊機構，並根據您複製的 DNS 資訊，為您的網域建立新的 TXT 記錄。
1. 註冊網域之後，在 Azure Active Directory 的網域頁面上，選取 [**驗證**]。 一旦**確認**網域狀態之後，您就可以在所有 Azure AD 設定（包括應用程式 Proxy）上使用網域。 

如需更詳細的指示，請參閱[使用 Azure Active Directory 入口網站新增自訂功能變數名稱](../fundamentals/add-custom-domain.md)。

### <a name="configure-an-app-to-use-a-custom-domain"></a>將應用程式設定為使用自訂網域

若要透過具有自訂網域的應用程式 Proxy 發佈您的應用程式：

1. 針對新的應用程式，請在 Azure Active Directory 中，選取左側導覽中的 **企業應用程式**。 選取 [新增應用程式]。 在 [內部部署應用程式] 區段中，選取 [新增內部部署應用程式]。 
   
   針對已在**企業應用程式**中的應用程式，請從清單中選取它，然後在左側導覽中選取 [**應用程式 proxy** ]。 

2. 在 [應用程式 Proxy 設定] 頁面上，如果您要新增自己的內部部署應用程式，請輸入**名稱**。

3.  在 [**內部 url** ] 欄位中，輸入您應用程式的內部 url。
   
4. 在 [**外部 Url** ] 欄位中，下拉清單並選取您想要使用的自訂網域。
   
5. 選取 [新增]。
   
   ![選取自訂網域](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. 如果網域已經有憑證，[**憑證**] 欄位就會顯示憑證資訊。 否則，請選取 [**憑證**] 欄位。 
   
   ![按一下以上傳憑證](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. 在 [ **SSL 憑證**] 頁面上，流覽並選取您的 PFX 憑證檔案。 輸入憑證的密碼，然後選取 [**上傳憑證**]。 如需憑證的詳細資訊，請參閱[自訂網域的憑證](#certificates-for-custom-domains)一節。
   
   ![Upload Certificate](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > 自訂網域只需要上傳一次它的憑證。 之後，當您使用其他應用程式的自訂網域時，就會自動套用上傳的憑證。
   
8. 如果您已新增憑證，請在 [**應用程式 proxy** ] 頁面上選取 [**儲存**]。 
   
9. 在 [**應用程式 proxy** ] 頁面的資訊列中，記下您需要新增至 DNS 區域的 CNAME 專案。 
   
   ![新增 CNAME DNS 專案](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. 請依照使用 Azure 入口網站新增 DNS 記錄，將新的外部 URL 重新導向至*msappproxy.net*網域中的指示來[管理 dns 記錄和記錄集](../../dns/dns-operations-recordsets-portal.md)。
   
11. 若要檢查 DNS 記錄是否已正確設定，請使用[nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx)命令來確認您的外部 URL 可供連線，且*msapproxy.net*網域會顯示為別名。

您的應用程式現在已設定為使用自訂網域。 請務必先將使用者指派給您的應用程式，然後再進行測試或發行。 

若要變更應用程式的網域，請在應用程式的 [**應用程式 proxy** ] 頁面上，從 [**外部 URL** ] 的下拉式清單中選取不同的網域。 視需要為更新的網域上傳憑證，並更新 DNS 記錄。 如果您在 [**外部 URL**] 的下拉式清單中看不到您想要的自訂網域，則可能不會進行驗證。

如需應用程式 Proxy 的詳細指示，請參閱[教學課程：在 Azure Active Directory 中新增透過應用程式 proxy 進行遠端存取的內部部署應用程式](application-proxy-add-on-premises-application.md)。

## <a name="certificates-for-custom-domains"></a>自訂網域的憑證

憑證會為您的自訂網域建立安全的 SSL 連線。 

### <a name="certificate-formats"></a>憑證格式

您必須使用 PFX 憑證，以確保包含所有必要的中繼憑證。 憑證必須包含私密金鑰。

憑證簽章方法沒有任何限制。 支援橢圓曲線密碼編譯（ECC）、主體別名（SAN）和其他常見的憑證類型。 

只要萬用字元符合外部 URL，您就可以使用萬用字元憑證。 [萬用字元應用程式](application-proxy-wildcard.md)必須使用萬用字元憑證。 如果您想要使用憑證來同時存取子域，您必須在相同的憑證中新增子域萬用字元做為主體的替代名稱。 例如， *\** 的憑證\*將無法用於 *\*. apps.adventure-works.com* ，除非您新增*apps.adventure-works.com*做為主體的替代名稱。 

如果您的用戶端裝置上已安裝憑證鏈，您可以使用自己的公開金鑰基礎結構（PKI）所發行的憑證。 Intune 可以將這些憑證部署至受管理的裝置。 對於不受管理的裝置，您必須手動安裝這些憑證。 

我們不建議使用私人根 CA，因為私人根 CA 也必須推送至用戶端電腦，這可能會造成許多挑戰。

### <a name="certificate-management"></a>憑證管理

所有憑證管理都是透過個別的應用程式頁面來進行。 移至應用程式的 [**應用程式 proxy** ] 頁面，以存取 [**憑證**] 欄位。

您可以針對多個應用程式使用相同的憑證。 如果上傳的憑證適用于另一個應用程式，則會自動套用。 當您新增或設定應用程式時，系統不會提示您再次上傳它。 

當憑證過期時，您會收到警告，告訴您上傳另一個憑證。 如果憑證已撤銷，您的使用者在存取應用程式時可能會看到安全性警告。 若要更新應用程式的憑證，請流覽至應用程式的 [**應用程式 proxy** ] 頁面，選取 [**憑證**]，然後上傳新的憑證。 如果其他應用程式未使用舊的憑證，則會自動刪除它。 

## <a name="next-steps"></a>後續步驟
* [啟用單一登入](application-proxy-configure-single-sign-on-with-kcd.md)以登入您使用 Azure AD 驗證發佈的應用程式。
* [啟用](../conditional-access/overview.md)已發佈應用程式的條件式存取。

