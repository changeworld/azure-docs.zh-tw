---
title: 教學課程：Azure Active Directory 與 SAP Analytics Cloud 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAP Analytics Cloud 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.openlocfilehash: 9edbb499788b61135d761c5dfa69ce42c9275932
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548730"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>教學課程：整合 SAP Analytics Cloud 與 Azure Active Directory

在本教學課程中，您將了解如何整合 SAP Analytics Cloud 與 Azure Active Directory (Azure AD)。 在整合 SAP Analytics Cloud 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 SAP Analytics Cloud 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 SAP Analytics Cloud。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 SAP Analytics Cloud 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* SAP Analytics Cloud 支援由 **SP** 起始的 SSO

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>從資源庫新增 SAP Analytics Cloud

若要進行將 SAP Analytics Cloud 整合到 Azure AD 中的設定，您必須從資源庫將 SAP Analytics Cloud 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **SAP Analytics Cloud**。
1. 從結果面板中選取 [SAP Analytics Cloud]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 SAP Analytics Cloud 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 SAP Analytics Cloud 中相關使用者之間的連結關聯性。

若要設定及測試與 SAP Analytics Cloud 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
2. **[設定 SAP Analytics Cloud 單一登入](#configure-sap-analytics-cloud-sso)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
5. **[建立 SAP Analytics Cloud 測試使用者](#create-sap-analytics-cloud-test-user)** - 使 SAP Analytics Cloud 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [SAP Analytics Cloud]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > 這些 URL 中的值僅供示範。 使用實際的「登入 URL」及「識別碼 URL」來更新這些值。 若要取得登入 URL，請連絡 [SAP Analytics Cloud 用戶端支援小組](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/)。 您可以從管理員主控台下載 SAP Analytics Cloud 中繼資料，以取得識別碼 URL。 稍後在本教學課程中會加以說明。

4. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  來下載憑證，並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 SAP Analytics Cloud]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>設定 SAP Analytics Cloud SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 SAP Analytics Cloud 公司網站。

2. 選取 [功能表]   > [系統]   > [管理]  。
    
    ![依序選取 [功能表]、[系統] 和 [管理]](./media/sapboc-tutorial/config1.png)

3. 在 [安全性]  索引標籤上，選取 [編輯]  \(畫筆) 圖示。
    
    ![在 [安全性] 索引標籤上，選取 [編輯] 圖示](./media/sapboc-tutorial/config2.png)  

4. 選取 [SAML 單一登入 (SSO)]  作為 [驗證方法]  。

    ![選取 [SAML 單一登入] 作為驗證方法](./media/sapboc-tutorial/config3.png)  

5. 選取 [下載]  ，以下載服務提供者中繼資料 (步驟 1)。 在中繼資料檔案中，尋找並複製 **entityID** 值。 在 Azure 入口網站的 [基本 SAML 設定]  對話方塊上，將值貼入 [識別碼]  方塊。

    ![複製並貼上 entityID 值](./media/sapboc-tutorial/config4.png)  

6. 若要在您從 Azure 入口網站下載的檔案中上傳服務提供者中繼資料 (步驟 2) **，請在 [上傳識別提供者中繼資料]** 下，選取 [上傳]  。  

    ![在上傳識別提供者中繼資料下，選取 [上傳]](./media/sapboc-tutorial/config5.png)

7. 在 [使用者屬性值]  清單中，選取您需要用於實作的使用者屬性 (步驟 3)。 此使用者屬性會對應到識別提供者。 若要在使用者的頁面上輸入自訂屬性，請使用 [自訂 SAML 對應]  選項。 或者，您可以選取 [電子郵件]  或 [使用者識別碼]  作為使用者屬性。 在本範例中，我們選取 [電子郵件]  ，因為我們在 Azure 入口網站的 [使用者屬性和宣告]  區段中，使用 **userprincipalname** 屬性來對應使用者識別項宣告。 這會提供唯一的使用者電子郵件，在每個成功 SAML 回應中傳送至 SAP Analytics Cloud 應用程式。

    ![選取使用者屬性](./media/sapboc-tutorial/config6.png)

8. 若要使用識別提供者 (步驟 4) 來驗證帳戶，請在 [登入認證 (電子郵件)]  方塊中，輸入使用者的電子郵件地址。 然後，選取 [驗證帳戶]  。 系統會將登入認證新增至使用者帳戶。

    ![輸入電子郵件，並選取 [驗證帳戶]](./media/sapboc-tutorial/config7.png)

9. 選取 [儲存]  圖示。

    ![[儲存] 圖示](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 SAP Analytics Cloud 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [SAP Analytics Cloud]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-sap-analytics-cloud-test-user"></a>建立 SAP Analytics Cloud 測試使用者

必須在 SAP Analytics Cloud 中佈建 Azure AD 使用者之後，他們才能登入 SAP Analytics Cloud。 在 SAP Analytics Cloud 中，佈建必須以手動方式進行。

若要佈建使用者帳戶：

1. 以系統管理員身分登入您的 SAP Analytics Cloud 公司網站。

2. 選取 [功能表]   > [安全性]   > [使用者]  。

    ![新增員工](./media/sapboc-tutorial/user1.png)

3. 若要在 [使用者]  頁面上新增新的使用者詳細資料，請選取 [+]  。 

    ![新增使用者頁面](./media/sapboc-tutorial/user4.png)

    然後完成下列步驟：

    a. 在 [使用者識別碼]  方塊中，輸入使用者的使用者識別碼，例如 **B**。

    b. 在 [名字]  方塊中，輸入使用者的名字，例如 **B**。

    c. 在 [姓氏]  方塊中，輸入使用者的姓氏，例如 **Simon**。

    d. 在 [顯示名稱]  方塊中，輸入使用者的全名，例如 **B.Simon**。

    e. 在 [電子郵件]  方塊中，輸入使用者的電子郵件地址，例如 `b.simon@contoso.com`。

    f. 在 [選取角色]  頁面上，選取適當的使用者角色，然後選取 [確定]  。

      ![選取角色](./media/sapboc-tutorial/user3.png)

    g. 選取 [儲存]  圖示。

### <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [SAP Analytics Cloud] 圖格時，應該會自動登入您已設定 SSO 的 SAP Analytics Cloud。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

