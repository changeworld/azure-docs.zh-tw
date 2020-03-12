---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 PureCloud by Genesys 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 PureCloud by Genesys 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dac8e0f2e10906f2cc56ecf86e0cc70947cb7e85
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897773"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 PureCloud by Genesys 整合

在本教學課程中，您將了解如何整合 PureCloud by Genesys 與 Azure Active Directory (Azure AD)。 完成整合之後，您可以：

* 使用 Azure AD 來控制哪些使用者可以存取 PureCloud by Genesys。
* 讓使用者使用其 Azure AD 帳戶自動登入 PureCloud by Genesys。
* 在 Azure 入口網站中集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有訂用帳戶，可取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 PureCloud by Genesys 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* PureCloud by Genesys 支援由 **SP 和 IDP** 起始的 SSO。

> [!NOTE]
> 由於此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>從資源庫新增 PureCloud by Genesys

若要設定將 PureCloud by Genesys 整合到 Azure AD 中，您必須從資源庫將 PureCloud by Genesys 新增到受控 SaaS 應用程式清單中。 若要這樣做，請遵循下列步驟：

1. 使用公司或學校帳戶或個人 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **PureCloud by Genesys**。
1. 從結果面板選取 [PureCloud by Genesys]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>設定及測試 PureCloud by Genesys 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 PureCloud by Genesys 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 PureCloud by Genesys 中相關使用者之間的連結關聯性。

若要設定及測試與 PureCloud by Genesys 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 PureCloud by Genesys SSO](#configure-purecloud-by-genesys-sso)** 以在應用程式端設定單一登入設定。
    1. **[建立 PureCloud by Genesys 測試使用者](#create-purecloud-by-genesys-test-user)** 以在 PureCloud by Genesys 中建立 B.Simon 的對應項目，且該項目須與 Azure AD 中代表使用者的項目連結。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [PureCloud by Genesys]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，選取 [基本 SAML 設定]  的畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段中，如果您想要以 **IDP 起始**模式設定應用程式，請輸入下列欄位的值：

    a. 在 [識別碼]  方塊中，輸入對應至您所在區域的 URL：

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. 在 [回覆 URL]  方塊中，輸入對應至您所在區域的 URL：

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. 如果您想要以 **SP** 起始模式設定應用程式，請選取 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  方塊中，輸入對應至您所在區域的 URL：
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. PureCloud by Genesys 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性 組態中。 以下螢幕擷取畫面顯示預設屬性清單：

    ![image](common/default-attributes.png)

1. 此外，PureCloud by Genesys 應用程式還需要於 SAML 回應中再傳回一些屬性，如下表所示。 這些屬性也會預先填入，但您可以視需要來檢閱這些屬性。

    | 名稱 | 來源屬性|
    | ---------------| --------------- |
    | 電子郵件 | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 PureCloud by Genesys]  區段中，依據您的需求複製適當的 URL (一或多個)。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名為 B.Simon 的測試使用者：

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，以下列格式輸入使用者名稱：username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 選取 [建立]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 PureCloud by Genesys 的存取權授與 B.Simon，來將其設定為使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [PureCloud by Genesys]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊中，從 [使用者] 清單中選取 [B.Simon]  ，然後選擇畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後選擇畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，選取 [指派]  按鈕。

## <a name="configure-purecloud-by-genesys-sso"></a>設定 PureCloud by Genesys SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入 PureCloud by Genesys。

1. 選取頂端的 [系統管理]  ，然後移至 [整合]  下的 [單一登入]  。

    ![設定單一登入](./media/purecloud-by-genesys-tutorial/configure01.png)

1. 切換至 [ADFS/Azure AD (進階)]  索引標籤，然後遵循下列步驟：

    ![設定單一登入](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. 選取 [瀏覽]  ，將您從 Azure 入口網站下載的 base-64 編碼憑證上傳至 [ADFS 憑證]  。

    b. 在 [ADFS 簽發者 URI]  方塊中，貼上您從 Azure 入口網站複製的 **Azure AD 識別碼**值。

    c. 在 [目標 URI]  方塊中，貼上您從 Azure 入口網站複製的**登入 URL** 值。

    d. 針對 [信賴憑證者識別碼]  的值，請移至 Azure 入口網站，然後在 [PureCloud by Genesys]  應用程式整合頁面上選取 [屬性]  索引標籤，並複製**應用程式識別碼**的值。 將值貼到 [信賴憑證者識別碼]  方塊中。

    ![設定單一登入](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. 選取 [儲存]  。

### <a name="create-purecloud-by-genesys-test-user"></a>建立 PureCloud by Genesys 測試使用者

若要讓 Azure AD 使用者能夠登入 PureCloud by Genesys，必須將他們佈建到 PureCloud by Genesys 中。 在 PureCloud by Genesys 中，需以手動方式佈建。

**若要佈建使用者帳戶，請遵循下列步驟：**

1. 以系統管理員的身分登入 PureCloud by Genesys。

1. 選取頂端的 [系統管理]  ，然後移至 [人員和權限]  底下的 [人員]  。

    ![設定單一登入](./media/purecloud-by-genesys-tutorial/configure03.png)

1. 在 [人員]  頁面上，選取 [新增人員]  。

    ![設定單一登入](./media/purecloud-by-genesys-tutorial/configure04.png)

1. 在 [將人員新增至組織]  對話方塊中，遵循下列步驟：

    ![設定單一登入](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. 在 [全名]  方塊中，輸入使用者的名稱。 例如：**B.simon**。

    b. 在 [電子郵件]  方塊中，輸入使用者的電子郵件。 例如：**b.simon\@contoso.com**。

    c. 選取 [建立]  。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入組態。

當您在存取面板中選取 [PureCloud by Genesys]  圖格時，應該會自動登入您已設定了 SSO 的 PureCloud by Genesys 帳戶。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [有關如何整合 SaaS 應用程式與 Azure AD 的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是應用程式存取與使用 Azure AD 進行單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure AD 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 PureCloud by Genesys](https://aad.portal.azure.com/)
