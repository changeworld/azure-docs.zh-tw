---
title: 教學課程：Azure AD SSO 與 Trend Micro Web Security (TMWS) 的整合
description: 了解如何設定 Azure Active Directory 與 Trend Micro Web Security (TMWS) 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: f2a6598cc28c39719d73be333bd74c24fce9371b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88551886"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Trend Micro Web Security (TMWS) 整合

在本教學課程中，您將了解如何整合 Trend Micro Web Security (TMWS) 與 Azure Active Directory (Azure AD)。 在整合 TMWS 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 TMWS 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 TMWS。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 的整合，請參閱 [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已針對 SSO 啟用的 TMWS 訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* TMWS 支援 SP 起始的 SSO。
* 在設定 TMWS 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 若要了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制，請參閱[上線和部署任何應用程式的條件式存取應用程式控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-tmws-from-the-gallery"></a>從資源庫新增 TMWS

若要設定將 TMWS 整合到 Azure AD 中，您需要將 TMWS 從資源庫新增到受控 SaaS 應用程式清單。

1. 使用公司、學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左窗格中選取 [Azure Active Directory]  服務。
1. 選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增新的應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Trend Micro Web Security (TMWS)** 。
1. 在搜尋結果中選取 [Trend Micro Web Security (TMWS)]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>設定和測試 TMWS 的 Azure AD SSO

您會以名為 B.Simon 的測試使用者，設定及測試與 TMWS 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 TMWS 中相關使用者之間的連結。

您將完成下列基本步驟，以使用 TMWS 來設定及測試 Azure AD SSO：

1. [設定 Azure AD SSO](#configure-azure-ad-sso)，讓使用者能夠使用此功能。
    1. [建立 Azure AD 使用者](#create-an-azure-ad-test-user)，以測試 Azure AD 單一登入。
    1. [授與 Azure AD 測試使用者](#grant-the-azure-ad-test-user-access-to-tmws) TMWS 的存取權。
    1. [在 Azure AD 中設定使用者和群組同步處理設定](#configure-user-and-group-synchronization-settings-in-azure-ad)。
1. 在應用程式端[設定 TMWS SSO](#configure-tmws-sso)。
1. [測試 SSO](#test-sso) 以確認設定。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

完成下列步驟，在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Trend Micro Web Security (TMWS)]  應用程式整合頁面上，於 [管理]  區段選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 組態]  的畫筆按鈕，以編輯設定：

   ![編輯 [基本 SAML 組態] 設定](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段中，於下列方塊中輸入值：

    a. 在 [識別碼 (實體識別碼)]  方塊中，以下列模式輸入 URL：

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. 在 [回覆 URL]  方塊中，輸入下列 URL：

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > 請勿輸入上一個步驟中的識別碼值。 您必須使用實際的識別碼。 您可以從 [系統管理] > [目錄服務]  ，在 Azure AD [驗證方法]  頁面上的 [Azure 系統管理入口網站的服務提供者設定]  區段中取得這個值。

1. TMWS 會預期要有特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性：

    ![預設屬性](common/default-attributes.png)

1. 除了上述螢幕擷取畫面中的屬性外，TMWS 還預期要在 SAML 回應中另外傳回兩個屬性。 下表會顯示這兩個屬性。 屬性會預先填入值，但可予以變更以符合您的需求。
    
    | 名稱 | 來源屬性|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  。 選取此憑證名稱旁的 [下載]  連結以下載憑證，並將其儲存在您的電腦上：

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Trend Micro Web Security (TMWS)]  區段中，依據您的需求複製一或多個適當的 URL：

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左側窗格中，選取 [Azure Active Directory]  。 選取 [使用者 ]  ，然後選取 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  方塊中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  方塊中，輸入 ***username *@* companydomain *.* extension***。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  ，然後記下 [密碼]  方塊中顯示的值。
   1. 選取 [建立]  。

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>授與 Azure AD 測試使用者 TMWS 的存取權

在本節中，您會將 TMWS 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Trend Micro Web Security (TMWS)]  。
1. 在應用程式的概觀頁面中，請在 [管理]  區段中，選取 [使用者和群組]  ：

   ![選取 [使用者和群組]](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![選取 [新增使用者]](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者]  清單中，選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，選取 [指派]  。

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>在 Azure AD 中設定使用者和群組同步處理設定

1. 在左窗格中，選取 [Azure Active Directory]  。

1. 在 [管理]  底下選取 [應用程式註冊]  ，然後在 [所有應用程式]  底下選取您的新企業應用程式。

1. 在 [管理]  下，選取 [憑證和密碼]  。

1. 在 [用戶端密碼]  區域中，選取 [新增用戶端密碼]  。

1. 在 [新增用戶端密碼]  畫面上，選擇性地新增描述並選取此用戶端密碼的到期期間，然後選取 [新增]  。 新的用戶端密碼便會出現在 [用戶端密碼]  區域中。

1. 記錄用戶端密碼值。 稍後，您會將其輸入至 TMWS。

1. 在 [管理]  之下選取 [API 權限]  。 

1. 在 [API 權限]  視窗中，選取 [新增權限]  。

1. 在 [要求 API 權限]  視窗的 [Microsoft API]  索引標籤上，依序選取 [Microsoft Graph]  和 [應用程式權限]  。

1. 找到並新增下列權限： 

    * Group.Read.All
    * User.Read.All

1. 選取 [新增權限]  。 此時會出現訊息來確認系統已儲存您的設定。 新的權限會出現在 [API 權限]  視窗中。

1. 在 [授與同意]  區域中，選取 [授與管理員同意 (您的系統管理員帳戶  ) (預設目錄)]  ，然後選取 [是]  。 此時會出現訊息來確認您已授與所要求權限的管理員同意。

1. 選取 [概觀]  。 

1. 記下您在右窗格中看到的 [應用程式 (用戶端) 識別碼]  和 [目錄 (租用戶) 識別碼]  。 稍後，您會在 TMWS 中輸入該資訊。 您也可以選取 [Azure Active Directory] > [管理]  底下的 [自訂網域名稱]  ，然後記下您在右窗格中看到的網域名稱。

## <a name="configure-tmws-sso"></a>設定 TMWS SSO

請完成下列步驟以在應用程式端設定 TMWS SSO。

1. 登入 TMWS 管理主控台，然後移至 [系統管理]   > [使用者與驗證]   > [目錄服務]  。

1. 選取畫面上方區域中的**這裡**。

1. 在 [驗證方法]  頁面上，選取 [Azure AD]  。

1. 選取 [開啟]  或 [關閉]  以設定是否要允許貴組織的 Azure AD 使用者透過 TMWS 瀏覽網站 (如果這些使用者的資料未同步處理到 TMWS 的話)。

    > [!NOTE]
    > 未從 Azure AD 同步處理的使用者只能透過已知的 TMWS 閘道或貴組織的專用連接埠進行驗證。

1. 在 [識別提供者設定]  區段中，完成下列步驟：

    a. 在 [服務 URL]  方塊中，輸入您從 Azure 入口網站複製的 [登入 URL]  值。

    b. 在 [登入名稱屬性]  方塊中，輸入**使用者宣告名稱**與來自 Azure 入口網站的 **user.onpremisessamaccountname** 來源屬性。

    c. 在 [公用 SSL 憑證]  方塊中，使用從 Azure 入口網站下載的 [憑證 (Base64)]  。

1. 在 [同步處理設定]  區段中，完成下列步驟：

    a. 在 [租用戶]  方塊中，輸入 [目錄 (租用戶) 識別碼]  或來自 Azure 入口網站的 [自訂網域名稱]  值。

    b. 在 [應用程式識別碼]  方塊中，輸入來自 Azure 入口網站的 [應用程式 (用戶端) 識別碼]  值。

    c. 在 [用戶端秘密]  方塊中，輸入來自 Azure 入口網站的 [用戶端秘密]  。

    d. 選取 [同步處理排程]  以手動方式或根據排程來與 Azure AD 進行同步處理。 如果您選取 [手動]  ，每當 Active Directory 使用者資訊有變更時，請記得回到 [目錄服務]  頁面並執行手動同步處理，讓 TMWS 中的資訊保持最新狀態。

    e. 選取 [測試連線]  ，檢查是否可以成功連線至 Azure AD 服務。
    
    f. 選取 [儲存]  。
 
 > [!NOTE]
 > 如需如何使用 Azure AD 來設定 TMWS 的詳細資訊，請參閱[在 TMWS 上設定 Azure AD 設定](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx)。

## <a name="test-sso"></a>測試 SSO 

在設定 Azure AD 服務並指定 Azure AD 作為使用者驗證方法後，您就可以登入 TMWS Proxy 伺服器來確認您的設定。 在 Azure AD 登入確認您的帳戶後，您就可以造訪網際網路。

> [!NOTE]
> TMWS 不支援從 Azure AD 入口網站測試單一登入，請在 [概觀]   > [單一登入]   > [以 SAML 設定單一登入]   > [測試您的新企業應用程式]  底下進行。

1. 清除瀏覽器的所有 Cookie，然後重新啟動瀏覽器。 

1. 將瀏覽器指向 TMWS Proxy 伺服器。 如需詳細資訊，請參閱[使用 PAC 檔案來轉送流量](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B)。

1. 造訪任何網際網路網站。 TMWS 會將您導向至 TMWS 網頁驗證入口。

1. 指定 Active Directory 帳戶 (格式：*domain*\\*sAMAccountName* or *sAMAccountName*@*domain*)、電子郵件地址或 UPN，然後選取 [登入]  。 TMWS 會將您傳送至 [Azure AD 登入] 視窗。

1. 在 [Azure AD 登入] 視窗中，輸入您的 Azure AD 帳號認證。 您現在應已登入 TMWS。

## <a name="additional-resources"></a>其他資源

- [如何整合 SaaS 應用程式與 Azure Active Directory 的教學課程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 Trend Micro Web Security](https://aad.portal.azure.com/)

- [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用進階可見性和控制項保護 Trend Micro Web Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

