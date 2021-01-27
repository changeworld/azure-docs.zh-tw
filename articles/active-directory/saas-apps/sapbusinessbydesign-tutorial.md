---
title: 教學課程：Azure Active Directory 與 SAP Business ByDesign 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 SAP Business ByDesign 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: e8f1bff50c5f163894392a9e5d08cc8451d835fa
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896405"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>教學課程：Azure Active Directory 與 SAP Business ByDesign 整合

在本教學課程中，您將瞭解如何整合 SAP Business ByDesign 與 Azure Active Directory (Azure AD) 。 當您整合 SAP Business ByDesign 與 Azure AD 時，您可以：

* 在 Azure AD 中控制可存取 SAP Business ByDesign 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 SAP Business ByDesign。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 SAP Business ByDesign 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* SAP Business ByDesign 支援由 **SP** 起始的 SSO

## <a name="add-sap-business-bydesign-from-the-gallery"></a>從資源庫新增 SAP Business ByDesign

若要設定 SAP Business ByDesign 與 Azure AD 整合，您需要從資源庫將 SAP Business ByDesign 加入到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [ **從資源庫新增** ] 區段的 [搜尋] 方塊中，輸入 **SAP Business ByDesign** 。
1. 從結果面板中選取 [ **SAP Business ByDesign** ]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO

使用名為 **b. Simon** 的測試使用者，設定及測試與 SAP Business ByDesign 搭配的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 SAP Business ByDesign 中相關使用者之間的連結關聯性。

若要設定及測試與 SAP Business ByDesign Azure AD SSO，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 SAP Business BYDESIGN SSO](#configure-sap-business-bydesign-sso)** -在應用程式端設定單一 Sign-On 設定。
    1. **[建立 SAP Business ByDesign 測試使用者](#create-sap-business-bydesign-test-user)** - 使 SAP Business ByDesign 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [ **SAP Business ByDesign** 應用程式整合] 頁面的 [Azure 入口網站] 中，尋找 [ **管理** ] 區段並選取 [ **單一登入**]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的鉛筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    a. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<servername>.sapbydesign.com`

    b. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<servername>.sapbydesign.com`

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值。 請連絡 [SAP Business ByDesign 用戶端支援小組](https://www.sap.com/products/cloud-analytics.support.html)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

5. SAP Business ByDesign 應用程式需要特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性]  區段中，管理這些屬性的值。 在 [以 SAML 設定單一登入]  頁面上，按一下 [編輯]  按鈕以開啟 [使用者屬性]  對話方塊。

    ![image1](common/edit-attribute.png)

6. 按一下 [編輯]  圖示以編輯 [名稱識別碼值]  。

    ![image2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. 在 [管理使用者宣告]  區段上，執行下列步驟：

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. 選取 [轉換]  作為 [來源]  。

    b. 從 [轉換]  下拉式清單中，選取 [ExtractMailPrefix()]  。

    > [!NOTE]
    > 依預設，ByD 會使用 **未指定** 的 NameID 格式進行使用者對應。 ByD 會對應 ByD 使用者別名上的 SAML 判斷提示 NameID。 此外，ByD 支援名稱識別碼格式 **emailAddress**。 在此情況下，ByD 會將 SAM 判斷提示的 NameID 對應到 ByD 員工連絡人資料的 ByD 使用者電子郵件地址。 如需詳細資料，您可以參考 [此 SAP blog](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/)。

8. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中按一下 [下載]，以依據您的需求從指定選項下載 **同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

9. 在 [設定 SAP Business ByDesign]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

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

在本節中，您會將 SAP Business ByDesign 的存取權授與 b. Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [SAP Business ByDesign]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。

1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-sap-business-bydesign-sso"></a>設定 SAP Business ByDesign SSO

1. 使用系統管理員權限登入 SAP Business ByDesign 入口網站。

2. 瀏覽至 [應用程式和使用者管理常見工作]  ，然後按一下 [識別提供者]  索引標籤。

3. 按一下 [新增識別提供者]  ，然後選取您從 Azure 入口網站下載的中繼資料 XML 檔案。 藉由匯入中繼資料，系統會自動上傳所需的簽章憑證和加密憑證。

    ![設定單一登入 1](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. 為了在 SAML 要求中包含 **判斷提示取用者服務 URL**，請選取 [包含判斷提示取用者服務 URL]  。

5. 按一下 [啟用單一登入]  。

6. 儲存您的變更。

7. 按一下 [我的系統]  索引標籤。

    ![設定單一登入 2](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. 在 [Azure AD 登入 URL]  文字方塊中，貼上您從 Azure 入口網站複製的 [登入 URL]  值。

    ![設定單一登入 3](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. 透過選取 [手動選取識別提供者]  ，指定員工是否可以在以使用者識別碼和密碼登入或 SSO 之間進行手動選擇。

10. 在 [SSO URL]  區段中，指定員工應用來登入系統的 URL。
    在 [傳送給員工的 URL] 下拉式清單中，您可以在下列選項之間做選擇︰

    **非 SSO URL**

    系統只會傳送一般系統 URL 給員工。 員工無法使用 SSO 來登入，而是必須改為使用密碼或憑證。

    **SSO URL**

    系統只會傳送 SSO URL 給員工。 員工可以使用 SSO 來登入。 驗證要求會透過 IdP 重新導向。

    **自動選取**

    如果未啟用 SSO，系統會傳送一般系統 URL 給員工。 如果已啟用 SSO，系統會檢查員工是否有密碼。 如果有密碼，便會將 SSO URL 和非 SSO URL 傳送給員工。 但如果員工沒有密碼，則只會傳送 SSO URL 給員工。

11. 儲存您的變更。

### <a name="create-sap-business-bydesign-test-user"></a>建立 SAP Business ByDesign 測試使用者

在本節中，您要在 SAP Business ByDesign 中建立名為 Britta Simon 的使用者。 請與 [SAP Business ByDesign 用戶端支援小組](https://www.sap.com/products/cloud-analytics.support.html)合作，以在 SAP Business ByDesign 平台中新增使用者。 

> [!NOTE]
> 請確定 NameID 值符合 SAP Business ByDesign 平台中的使用者名稱欄位。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

1. 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至 SAP Business ByDesign 登入 URL，您可以在其中起始登入流程。 

2. 直接移至 SAP Business ByDesign 登入 URL，並從該處起始登入流程。

3. 您可以使用 Microsoft 的「我的應用程式」。 當您在我的應用程式中按一下 [SAP Business ByDesign] 圖格時，這會重新導向至 SAP Business ByDesign 登入 URL。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="next-steps"></a>後續步驟

* 設定 SAP Business ByDesign 之後，您可以強制執行會話控制項，以即時防止組織的敏感性資料遭到外泄和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
