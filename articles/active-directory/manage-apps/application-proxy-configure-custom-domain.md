---
title: Azure AD 應用程式 Proxy 中的自訂網域 | Microsoft Docs
description: 在 Azure AD 應用程式代理中配置和管理自訂域。
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
ms.openlocfilehash: 3f35658a75adb4d4c6c279e45087e741b8117e65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481376"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>使用 Azure AD 應用程式代理配置自訂域

通過 Azure 活動目錄應用程式代理髮布應用程式時，會為使用者創建外部 URL。 此 URL 會取得預設網域 *yourtenant.msappproxy.net*。 例如，如果在*租戶中發佈*名為 *"支出"* 的應用，則外部 URL 為*HTTPs：/expenses-contoso.msappproxy.net\/*。 如果要使用自己的功能變數名稱而不是*msappproxy.net*，則可以為應用程式佈建自訂域。 

## <a name="benefits-of-custom-domains"></a>自訂域的優勢

最好盡可能為應用設置自訂域。 使用自訂域的一些原因包括：

- 即使在商業網路之外，應用之間的連結也能正常工作。 如果沒有自訂域，如果應用具有指向應用程式代理外部目標的硬編碼內部連結，並且這些連結無法外部解析，則這些連結將中斷。 當內部和外部 URL 相同時，可以避免此問題。 如果無法使用自訂域，請參閱[重定向使用 Azure AD 應用程式代理髮布的應用的硬編碼連結](../application-proxy-link-translation.md)，以瞭解解決此問題的其他方法。 
  
- 您的使用者將具有更簡單的體驗，因為他們可以從網路內部或外部使用相同的 URL 訪問應用。 他們不需要學習不同的內部和外部 URL，或跟蹤其當前位置。 

- 您可以控制品牌並創建所需的 URL。 自訂域可以説明建立使用者的信心，因為使用者看到並使用熟悉的名稱，而不是*msappproxy.net*。

- 某些配置僅適用于自訂域。 例如，對於使用安全斷言標記語言 （SAML） 的應用，例如使用活動目錄聯合服務 （AD FS） 但無法使用 WS-識別身分同盟時），需要自訂域。 有關詳細資訊，請參閱[在應用程式代理 中使用聲明感知應用](application-proxy-configure-for-claims-aware-applications.md)。 

如果無法使內部和外部 URL 匹配，則使用自訂域並不重要，但您仍可以利用其他優勢。 

## <a name="dns-configuration-options"></a>DNS 配置選項

根據您的要求，有幾個選項可用於設置 DNS 配置：

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>相同的內部和外部 URL，不同的內部和外部行為 

如果您不希望內部使用者通過應用程式代理定向，則可以設置*一個分腦 DNS*。 拆分 DNS 基礎結構將內部主機定向到內部功能變數名稱伺服器，將外部主機定向到外部功能變數名稱伺服器，以便進行名稱解析。 

![拆分式 DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>不同的內部和外部 URL 

如果內部和外部 URL 不同，則無需配置拆分大腦行為，因為使用者路由由 URL 決定。 在這種情況下，您只更改外部 DNS，並將外部 URL 路由到應用程式代理終結點。 

為外部 URL 選擇自訂域時，資訊列將顯示需要添加到外部 DNS 提供程式的 CNAME 條目。 您始終可以通過訪問應用**的應用程式代理**頁來查看此資訊。

## <a name="set-up-and-use-custom-domains"></a>設置和使用自訂域

要將本地應用配置為使用自訂域，需要經過驗證的 Azure 活動目錄自訂域、自訂域的 PFX 證書和要配置的本地應用。 

### <a name="create-and-verify-a-custom-domain"></a>創建和驗證自訂域

要創建和驗證自訂域，請進行以下操作：

1. 在 Azure 活動目錄中，在左側導航中選擇**自訂功能變數名稱**，然後選擇"**添加自訂域**"。 
1. 輸入自訂功能變數名稱並選擇 **"添加域**"。 
1. 在域頁上，複製域的 TXT 記錄資訊。 
1. 轉到域註冊商，並根據複製的 DNS 資訊為您的域創建新的 TXT 記錄。
1. 註冊域後，在 Azure 活動目錄中的域頁面上選擇 **"驗證**"。 **域狀態已驗證**後，可以跨所有 Azure AD 配置（包括應用程式代理）使用域。 

有關更詳細的說明，請參閱使用[Azure 活動目錄門戶添加自訂功能變數名稱](../fundamentals/add-custom-domain.md)。

### <a name="configure-an-app-to-use-a-custom-domain"></a>將應用配置為使用自訂域

要使用自訂域通過應用程式代理髮布應用，請執行以下操作：

1. 對於新應用，在 Azure 活動目錄中，選擇左側導航中的**企業應用程式**。 選擇 **"新應用程式**"。 在 [內部部署應用程式]**** 區段中，選取 [新增內部部署應用程式]****。 
   
   對於已在**企業應用程式中**的應用，請從清單中選擇它，然後在左側導航中選擇**應用程式代理**。 

2. 在"應用程式代理設置"頁上，如果要添加自己的本地應用程式，請輸入**名稱**。

3.  在 **"內部 Url"** 欄位中，輸入應用的內部 URL。
   
4. 在 **"外部 Url"** 欄位中，下拉清單並選擇要使用的自訂域。
   
5. 選取 [加入]****。
   
   ![選擇自訂域](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. 如果域已具有證書，**則"證書**"欄位將顯示證書資訊。 否則，選擇 **"證書"** 欄位。 
   
   ![按一下以上傳憑證](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. 在**SSL 憑證**頁上，流覽到並選擇您的 PFX 證書檔。 輸入證書的密碼，然後選擇 **"上載證書**"。 有關證書的詳細資訊，請參閱[自訂域的證書](#certificates-for-custom-domains)部分。
   
   ![Upload Certificate](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > 自訂域只需要上傳一次證書。 之後，當您對其他應用使用自訂域時，將自動應用上載的證書。
   
8. 如果添加了證書，請在**應用程式代理**頁上選擇 **"保存**"。 
   
9. 在**應用程式代理**頁上的資訊列中，請注意需要添加到 DNS 區域的 CNAME 條目。 
   
   ![添加 CNAME DNS 條目](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. 使用 Azure 門戶添加 DNS 記錄，將新的外部 URL 重定向到*msappproxy.net*域，請按照[管理 DNS 記錄和記錄集](../../dns/dns-operations-recordsets-portal.md)的說明進行操作。
   
11. 要檢查 DNS 記錄配置是否正確，請使用[nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx)命令確認外部 URL 是可訪問的，*並且msapproxy.net*域顯示為別名。

應用程式現在設置為使用自訂域。 在測試或釋放應用程式之前，請確保將使用者分配給應用程式。 

要更改應用的域，請從應用**應用程式代理**頁上**的"外部 URL"** 中的下拉清單中選擇其他域。 如有必要，上傳更新的域的證書，並更新 DNS 記錄。 如果在 **"外部 URL"** 的下拉清單中看不到所需的自訂域，則可能無法驗證該域。

有關應用程式代理的更多詳細資訊，請參閱[教程：在 Azure 活動目錄中通過應用程式代理添加用於遠端存取的本地應用程式](application-proxy-add-on-premises-application.md)。

## <a name="certificates-for-custom-domains"></a>自訂域的證書

證書為您的自訂域創建安全 TLS 連接。 

### <a name="certificate-formats"></a>證書格式

您必須使用 PFX 證書，以確保包含所有必需的中繼憑證。 證書必須包含私密金鑰。

證書簽名方法沒有限制。 支援橢圓曲線加密 （ECC）、主題替代名稱 （SAN） 和其他常見證書類型。 

只要萬用字元與外部 URL 匹配，就可以使用萬用字元證書。 您必須對[萬用字元應用程式使用萬用字元](application-proxy-wildcard.md)證書。 如果要使用證書同時訪問子域，則必須在同一證書中添加子域萬用字元作為主題替代名稱。 例如，除非將*\*.apps.adventure-works.com*添加為主題替代名稱，否則*\*.adventure-works.com*的證書將*\*apps.adventure-works.com*不起作用。 

如果憑證連結安裝在用戶端設備上，則可以使用自己的公開金鑰基礎結構 （PKI） 頒發的證書。 Intune 可以將這些證書部署到託管設備。 對於非託管設備，必須手動安裝這些證書。 

我們不建議使用私有根 CA，因為私有根 CA 也需要推送到用戶端電腦，這會帶來許多挑戰。

### <a name="certificate-management"></a>憑證管理

所有證書管理都通過各個應用程式頁。 轉到應用程式**的應用程式代理**頁以訪問**證書**欄位。

可以為多個應用程式使用相同的證書。 如果上載的證書適用于其他應用程式，則將自動應用它。 添加或配置應用時，不會提示您再次上載它。 

當證書過期時，您會收到一條警告，告訴您要上載其他證書。 如果證書被吊銷，則使用者在訪問應用時可能會看到安全警告。 要更新應用的證書，請導航到應用的應用程式**代理**頁，選擇 **"證書**"並上載新證書。 如果舊證書未被其他應用使用，則會自動刪除它。 

## <a name="next-steps"></a>後續步驟
* [啟用單一登入](application-proxy-configure-single-sign-on-with-kcd.md)以登入您使用 Azure AD 驗證發佈的應用程式。
* [啟用對](../conditional-access/overview.md)已發佈的應用的條件訪問。

