---
title: 教學課程：Azure Active Directory 與 Soloinsight-CloudGate SSO 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Soloinsight-CloudGate SSO 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.openlocfilehash: 5ac0f0777ea341036950550e19c5d8e7fb71a91f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545058"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>教學課程：整合 Soloinsight-CloudGate SSO 與 Azure Active Directory

在本教學課程中，您將了解如何整合 Soloinsight-CloudGate SSO 與 Azure Active Directory (Azure AD)。 在整合 Soloinsight-CloudGate SSO 與 Azure AD 後，您可以：

* 在 Azure AD 中控制可存取 Soloinsight-CloudGate SSO 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Soloinsight-CloudGate SSO。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 Soloinsight-CloudGate SSO 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 Soloinsight-CloudGate SSO 支援由 **SP** 起始的 SSO。

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>從資源庫新增 Soloinsight-CloudGate SSO

若要設定將 Soloinsight-CloudGate SSO 整合到 Azure AD 中，您需要從資源庫將 Soloinsight-CloudGate SSO 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Soloinsight-CloudGate SSO**。
1. 從結果面板中選取 [Soloinsight-CloudGate SSO]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **Britta Simon** 的測試使用者，設定及測試與 Soloinsight-CloudGate SSO 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Soloinsight-CloudGate SSO 中相關使用者之間的連結關聯性。

若要設定及測試與 Soloinsight-CloudGate SSO 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
2. **[設定 Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso)** - 在應用程式端設定 SSO 設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Soloinsight-CloudGate SSO 測試使用者](#create-soloinsight-cloudgate-sso-test-user)** ，使 Soloinsight-CloudGate SSO 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Soloinsight-CloudGate SSO]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  頁面上，輸入下列欄位的值：

    1. 在 [登入 URL]  文字方塊中，使用下列模式輸入 URL：`https://<SUBDOMAIN>.sigateway.com/login`

    1. 在 [識別碼 (實體識別碼)]  文字方塊中，使用下列模式輸入 URL：`https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > 這些都不是真正的值。 請使用實際的「登入 URL」及「識別碼」來更新這些值 (稍後在本教學課程 **設定 Soloinsight-CloudGate SSO 單一登入**一節中會加以說明)。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

   ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Soloinsight-CloudGate SSO]  區段上，依據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>設定 Soloinsight-CloudGate SSO

1. 若要自動執行 Soloinsight-CloudGate SSO 內的設定，您必須按一下 [安裝擴充功能]  以安裝「我的應用程式安全登入瀏覽器擴充功能」  。

    ![我的應用程式擴充功能](common/install-myappssecure-extension.png)

2. 將擴充功能新增至瀏覽器之後，按一下 [設定 Soloinsight-CloudGate SSO]  便會將您導向至 Soloinsight-CloudGate SSO 應用程式。 請從該處提供用以登入 Soloinsight-CloudGate SSO 的管理員認證。 瀏覽器擴充功能會自動為您設定應用程式，並自動執行步驟 3 到 8。

    ![設定組態](common/setup-sso.png)

3. 如果您想要手動設定 Soloinsight-CloudGate SSO，請開啟新的網頁瀏覽器視窗，並以系統管理員身分登入 Soloinsight-CloudGate SSO 公司網站，然後執行下列步驟：

4. 若要取得要在設定基本 SAML 時貼到 Azure 入口網站的值，請使用認證登入 CloudGate 入口網站，然後存取 SSO 設定 (可於下列路徑找到：**首頁 > 管理 > 系統設定 > 一般**)。

    ![CloudGate SSO 設定](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **SAML 取用者 URL**

    * 針對 [SAML 取用者 URL]  和 [重新導向 URL]  欄位複製可用的連結，然後將其分別貼到 Azure 入口網站中的 [識別碼 (實體識別碼)]  和 [回覆 URL]  欄位的 [基本 SAML 組態]  區段。

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML 簽署憑證**

    * 移至從 Azure 入口網站 SAML 簽署憑證清單下載的憑證 (Base64) 檔案來源，然後對其按一下滑鼠右鍵。 從清單中選擇 [使用 Notepad++ 來編輯]  選項。 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * 複製憑證 (Base64) Notepad++ 檔案中的內容。

        ![憑證複製](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * 將內容貼到 CloudGate 入口網站 SSO 設定的 [憑證]  欄位，然後按一下 [儲存] 按鈕。

        ![憑證入口網站](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **預設群組**

    * 在 CloudGate 入口網站中，從 [預設群組]  選項的下拉式清單選取 [商務管理員] 

        ![預設群組](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD 識別碼和登入 URL**

    * 從 Azure 入口網站的 [設定 Soloinsight-CloudGate SSO]  組態中複製的 [登入 URL]  ，應輸入 CloudGate 入口網站的 [SSO 設定] 區段中。

    * 將 Azure 入口網站中的 [登入 URL]  連結貼到 CloudGate 入口網站的 [AD 登入 URL]  欄位。

    * 將 Azure 入口網站中的 [Azure AD 識別碼]  連結貼到 CloudGate 入口網站的 [AD 識別碼]  欄位

        ![Ad 登入](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `Britta Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `BrittaSimon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Soloinsight-CloudGate SSO 的存取權授與 Britta Simon，使其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Soloinsight-CloudGate SSO]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊中，從使用者清單選取 **Britta Simon**，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>建立 Soloinsight-CloudGate SSO 測試使用者

若要建立測試使用者，請從 CloudGate 入口網站的主要功能表選取 [員工]  ，然後填妥 [新增員工] 表單。 要指派給測試使用者的授權層級是**商務管理員**。填妥所有必要欄位後，按一下 [建立]  。

![員工測試](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 [Soloinsight-CloudGate SSO] 圖格時，應該會自動登入您已設定 SSO 的 Soloinsight-CloudGate SSO。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)