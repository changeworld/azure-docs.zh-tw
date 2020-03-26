---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 EBSCO 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 EBSCO 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f37085744b9a0e7785ef3a411d53e4df5d15e494
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "72595016"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 EBSCO 整合

在本教學課程中，您將了解如何整合 EBSCO 與 Azure Active Directory (Azure AD)。 在整合 EBSCO 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 EBSCO 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 EBSCO。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 EBSCO 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* EBSCO 支援由 **SP 和 IDP** 起始的 SSO
* EBSCO 支援 **Just In Time** 使用者佈建

> [!NOTE]
> 此應用程式的識別碼是固定的字串值，因此一個租用戶中只能設定一個執行個體。

## <a name="adding-ebsco-from-the-gallery"></a>從資源庫新增 EBSCO

若要設定將 EBSCO 整合到 Azure AD 中，您必須從資源庫將 EBSCO 新增至受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 **EBSCO**。
1. 從結果面板選取 [EBSCO]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>設定及測試 EBSCO 的 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 EBSCO 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 EBSCO 中相關使用者之間的連結關聯性。

若要設定及測試與 EBSCO 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 EBSCO SSO](#configure-ebsco-sso)** - 在應用程式端設定單一登入設定。
    * **[建立 EBSCO 測試使用者](#create-ebsco-test-user)** - 在 EBSCO 中建立 B.Simon 的對應項目，使該項目與 Azure AD 中代表該使用者的項目連結。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [EBSCO]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  區段上，如果您想要以 **IDP** 起始模式設定應用程式，請輸入下列欄位的值：

    在 [識別碼]  文字方塊中，鍵入 URL：`pingsso.ebscohost.com`

1. 如果您想要以 **SP** 起始模式設定應用程式，請按一下 [設定其他 URL]  ，然後執行下列步驟：

    在 [登入 URL]  文字方塊中，以下列模式輸入 URL︰`http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > [登入 URL] 的值不是真正的值。 請使用實際的「登入 URL」來更新此值。 請連絡 [EBSCO 客戶支援小組](mailto:support@ebsco.com)以取得這些值。 您也可以參考 Azure 入口網站中**基本 SAML 組態**區段所示的模式。

    o   **唯一元素：**  

    o   **Custid** = 輸入唯一的 EBSCO 客戶識別碼 

    o   **Profile** = 客戶可以自訂連結，將使用者導向至特定的設定檔 (視他們向 EBSCO 購買的產品而定)。 他們可以輸入特定的設定檔識別碼。 主要識別碼是 eds (EBSCO 探索服務) 和 ehost (EBSOCOhost 資料庫)。 相同項目的指示提供於[這裡](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)。

1. EBSCO 應用程式會預期特定格式的 SAML 判斷提示，這會需要您將自訂屬性對應加入至您的 SAML 權杖屬性設定。 以下螢幕擷取畫面顯示預設屬性清單。

    ![image](common/default-attributes.png)

    > [!Note]
    > **name** 是必要屬性，其會與 EBSCO 應用程式中的 [名稱識別碼值]  相對應。 依預設會新增此屬性，因此您無須手動新增。

1. 除了上述屬性外，EBSCO 應用程式還需要在 SAML 回應中多傳回幾個屬性，如下所示。 這些屬性也會預先填入，但您可以根據您的需求來檢閱這些屬性。

    | 名稱 | 來源屬性|
    | ---------------| --------------- |
    | 名字   | user.givenname |
    | 姓氏   | user.surname |
    | 電子郵件   | user.mail |

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 EBSCO]  區段上，根據您的需求複製適當的 URL。

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

在本節中，您會將 EBSCO 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [EBSCO]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-ebsco-sso"></a>設定 EBSCO SSO

若要在 **EBSCO** 端設定單一登入，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML]  和複製的適當 URL 傳送給 [EBSCO 支援小組](mailto:support@ebsco.com)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-ebsco-test-user"></a>建立 EBSCO 測試使用者

使用 EBSCO 時，會自動佈建使用者。

**若要佈建使用者帳戶，請執行下列步驟：**

Azure AD 會將所需的資料傳遞至 EBSCO 應用程式。 EBSCO 的使用者佈建可以自動執行，或是使用一次性表單。 這取決於客戶是否有已儲存個人設定的大量現有 EBSCOhost 帳戶。 相同的問題也可在實作期間與 [EBSCO 支援小組](mailto:support@ebsco.com)討論。 無論如何，客戶都不需要在測試之前建立任何 EBSCOhost 帳戶。

   > [!Note]
   > 您可以自動化 EBSCOhost 使用者佈建/個人化。 向 [EBSCO 支援小組](mailto:support@ebsco.com)洽詢關於 Just-in-Time 使用者佈建的問題。

## <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

1. 當您在存取面板中按一下 EBSCO 圖格時，應該會自動登入您的 EBSCO 應用程式。
如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

1. 登入應用程式後，請按一下右上角的 [登入]  按鈕。

    ![應用程式清單中的 EBSCO 登入](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. 您會收到一次性的提示，要您透過 [立即將您現有的 MyEBSCOhost 帳戶連結至機構帳戶]  或 [建立新的 MyEBSCOhost 帳戶並將其連結至您的機構帳戶]  ，進行機構/SAML 登入的配對。 此帳戶用於 EBSCOhost 應用程式的個人化。 請選取 [建立新的帳戶]  選項，您會看到個人化的表單已使用 saml 回應中的值預先完成，如以下螢幕擷取畫面所示。 請按一下 [繼續]  以儲存此選取項目。
    
     ![應用程式清單中的 EBSCO 使用者](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. 完成上述設定後，請清除 cookie/快取，然後重新登入。 您無須手動重新登入，系統會記住個人化設定。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [嘗試搭配 Azure AD 使用 EBSCO](https://aad.portal.azure.com/)