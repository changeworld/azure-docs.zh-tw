---
title: 教學課程：Azure Active Directory 與 Projectplace 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Projectplace 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.openlocfilehash: cbc65e64dfd18cdc16873a2d82aecadabeec4c28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553504"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>教學課程：整合 Projectplace 與 Azure Active Directory

在本教學課程中，您將了解如何整合 Projectplace 與 Azure Active Directory (Azure AD)。 在整合 Projectplace 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Projectplace 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Projectplace。
* 在 Azure 入口網站集中管理您的帳戶。
* 使用者可以自動佈建在 Projectplace 中。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Projectplace 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。 Projectplace 支援 **SP 和 IDP** 起始的 SSO，並且支援 **Just In Time** 使用者佈建。

## <a name="adding-projectplace-from-the-gallery"></a>從資源庫新增 Projectplace

若要設定將 Projectplace 整合到 Azure AD 中，您需要從資源庫將 Projectplace 加入受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **Projectplace**。
1. 從結果面板選取 [Projectplace]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B. Simon** 的測試使用者，設定及測試與 Projectplace 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Projectplace 中相關使用者之間的連結關聯性。

若要設定及測試與 Projectplace 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
2. **[設定 Projectplace](#configure-projectplace)** 以在應用程式端設定 SSO 設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B. Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B. Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Projectplace 測試使用者](#create-projectplace-test-user)** - 使 Projectplace 中對應的 B. Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Projectplace]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，則已預先設定好應用程式，並已經為 Azure 預先填入必要的 URL。 使用者必須按一下 [儲存]  按鈕，才能儲存設定。

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，輸入 URL：`https://service.projectplace.com`

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，按一下複製**圖示**，依據您的需求複製 [應用程式同盟中繼資料 URL]  並儲存在 [記事本] 中。

   ![憑證下載連結](common/copy-metadataurl.png)

1. 在 [設定 Projectplace]  區段上，依據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>設定 Projectplace

若要在 **Projectplace** 端設定單一登入，您必須將從 Azure 入口網站複製的 [應用程式同盟中繼資料 URL]  傳送給 [Projectplace 支援小組](https://success.planview.com/Projectplace/Support)。 此小組會確定兩端的 SAML SSO 連線都已正確設定。

>[!NOTE]
>單一登入設定必須由 [Projectplace 支援小組](https://success.planview.com/Projectplace/Support)執行。 設定完成後，您將會收到通知。 

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 B. Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B. Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `BrittaSimon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Projectplace 的存取權授與 B. Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 **Projectplace**。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B. Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-projectplace-test-user"></a>建立 Projectplace 測試使用者

>[!NOTE]
>如果已在 Projectplace 中啟用佈建，您可以略過此步驟。 您可以要求 [Projectplace 支援小組](https://success.planview.com/Projectplace/Support)啟用佈建，一旦完成，就會於第一次登入期間在 Projectplace 中建立使用者。

若要讓 Azure AD 使用者能夠登入 Projectplace，必須在 Projectplace 中新增他們。 您需要手動新增他們。

**若要建立使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入您的 **Projectplace** 公司網站。

2. 移至 [人員]  ，然後選取 [成員]  ：
   
    ![移至 [人員]，然後選取 [成員]](./media/projectplace-tutorial/ic790228.png "人員")

3. 選取 [新增成員]  ：
   
    ![選取 [新增成員]](./media/projectplace-tutorial/ic790232.png "新增成員")

4. 在 [新增成員]  區段中，執行下列步驟。
   
    ![[新增成員] 區段](./media/projectplace-tutorial/ic790233.png "新成員")
   
    1. 在 [新增成員]  方塊中，輸入您要新增的有效 Azure AD 帳戶電子郵件地址。
   
    1. 選取 [傳送]  。

   系統會傳送一封電子郵件給 Azure AD 帳戶的持有者，郵件中包含用來在啟用帳戶前確認帳戶的連結。

>[!NOTE]
>您也可以使用由 Projectplace 提供的任何其他使用者帳戶特定工具或 API 來新增 Azure AD 使用者帳戶。


### <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 Projectplace 圖格時，應該會自動登入您設定 SSO 的 Projectplace。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)