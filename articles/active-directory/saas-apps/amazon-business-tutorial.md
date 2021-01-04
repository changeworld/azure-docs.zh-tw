---
title: 教學課程：Azure Active Directory 與 Amazon Business 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Amazon Business 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: 1424669af9fe28257e66c8c5438f6e1d171954e0
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672894"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>教學課程：整合 Amazon Business 與 Azure Active Directory

在本教學課程中，您會了解如何整合 Amazon Business 與 Azure Active Directory (Azure AD)。 在整合 [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Amazon Business 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Amazon Business。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的免費試用。
* 已啟用 Amazon Business 單一登入 (SSO) 的訂用帳戶。 前往 [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) 頁面以建立 Amazon Business 帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在現有的 Amazon Business 帳戶中設定和測試 Azure AD SSO。

* Amazon Business 支援 **SP 和 IDP** 起始的 SSO
* Amazon Business 支援 **Just In Time** 使用者佈建

## <a name="adding-amazon-business-from-the-gallery"></a>從資源庫新增 Amazon Business

若要設定 Amazon Business 與 Azure AD 整合，您需要從資源庫將 Amazon Business 新增到受控 SaaS 應用程式清單中。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Amazon Business**。
1. 從結果面板選取 [Amazon Business]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Amazon Business 搭配運作的 Azure AD SSO。

若要設定及測試與 Amazon Business 搭配運作的 Azure AD SSO，請完成下列建置步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
2. **[設定 Amazon Business SSO](#configure-amazon-business-sso)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Amazon Business 測試使用者](#create-amazon-business-test-user)** - 使 Amazon Business 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Amazon Business] 應用程式整合頁面上，尋找 [管理] 區段並選取 [單一登入]。
1. 在 [選取單一登入方法] 頁面上，選取 [SAML]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

    ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態] 區段上，若您想要以 **IDP** 起始模式進行設定，請執行下列步驟：

    1. 在 [識別碼 (實體識別碼)] 文字方塊中，使用下列其中一個模式輸入 URL：

       | URL | 區域 |
       |-|-|
       | `https://www.amazon.com`| 北美洲 |
       | `https://www.amazon.co.jp`| 東亞 |
       | `https://www.amazon.de`| 歐洲 |

    1. 在 [回覆 URL] 文字方塊中，以下列其中一個模式輸入 URL：

       | URL | 區域 |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`| 北美洲 |
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`| 東亞 |
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`| 歐洲 |

       > [!NOTE]
       > [回覆 URL] 不是真實的值。 請使用實際的「回覆 URL」來更新此值。 您將從 Amazon Business SSO 設定一節取得 `<idpid>` 值，本教學課程稍後會加以說明。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. 如果您要以 **SP** 起始模式設定應用程式，必須將 Amazon 商務組態中提供的完整 URL 新增至 **設定其他 URL** 區段中的 [登入 URL]。

1. 以下螢幕擷取畫面顯示預設屬性清單。 按一下 [使用者屬性與宣告] 區段中的 [編輯] 圖示來編輯屬性。

    ![顯示使用者屬性和宣告的螢幕擷取畫面，其中具有預設值，例如名字 user.givenname 和電子郵件地址 user.mail。](media/amazon-business-tutorial/map-attribute3.png)

1. 編輯屬性並將這些屬性的 [命名空間] 值複製到 [記事本] 中。

    ![顯示使用者屬性和宣告的螢幕擷取畫面，其中具有適用於宣告名稱和值的資料行。](media/amazon-business-tutorial/map-attribute4.png)

1. 除了以上屬性外，Amazon Business 應用程式還需要在 SAML 回應中傳回更多屬性。 在 [群組宣告] 對話方塊的 [使用者屬性與宣告] 區段中，執行下列步驟：

    1. 按一下 [宣告中傳回的群組] 旁的 **筆**。

        ![顯示使用者屬性和宣告的螢幕擷取畫面，其中具有所選取宣告中傳回的群組圖示。](./media/amazon-business-tutorial/config04.png)

        ![顯示群組宣告的螢幕擷取畫面，其中具有此程序中所述的值。](./media/amazon-business-tutorial/config05.png)

    1. 選取選項按鈕清單中的 [所有群組]  。

    1. 選取 [群組識別碼] 作為 [來源屬性]。

    1. 勾選 [自訂群組宣告的名稱] 核取方塊，並根據貴組織需求輸入群組名稱。

    1. 按一下 [檔案] 。

1. 在 [以 SAML 設定單一登入] 頁面上的 [SAML 簽署憑證] 區段中，尋找 [中繼資料 XML]，然後選取 [下載] 來下載憑證，並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Amazon Business] 區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>設定 Amazon Business SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Amazon Business 公司網站。

1. 按一下 [使用者設定檔]，然後選取 [Business 設定]。

    ![使用者設定檔](media/amazon-business-tutorial/user-profile.png)

1. 在 [系統整合] 精靈上，選取 [單一登入 (SSO)]。

    ![單一登入 (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. 在 [設定 SSO] 精靈上，根據貴組織的需求選取提供者，然後按 [下一步]。

    ![顯示設定 SSO 的螢幕擷取畫面，其中具有 Microsoft Azure AD 和已選取下一步。](media/amazon-business-tutorial/default-group1.png)

    > [!NOTE]
    > 雖然已列出 Microsoft ADFS 選項，但其無法與 Azure AD SSO 搭配使用。

1. 在 [新增使用者帳戶預設值] 精靈上，選取 [預設群組]並根據貴組織中的使用者角色選取 [預設購買角色]，然後按 [下一步]。

    ![顯示新增使用者預設值的螢幕擷取畫面，其中具有 Microsoft SSO、申購者，以及已選取下一步。](media/amazon-business-tutorial/dafault-group2.png)

1. 在 [上傳您的中繼資料檔案] 精靈上，按一下 [瀏覽] 以上傳您從 Azure 入口網站下載的 [中繼資料 XML] 檔案，然後按一下 [上傳]。

    ![顯示上傳您的中繼資料檔的螢幕擷取畫面，可讓您瀏覽至 xml 檔案並且上傳。](media/amazon-business-tutorial/connection-data1.png)

1. 上傳所下載的中繼資料檔案之後，就會自動填入 [連線資料] 區段中的欄位。 然後按 [下一步]。

    ![顯示連線資料的螢幕擷取畫面，您可以在其中指定 Azure AD 識別碼、登入 URL 和 SAML 簽署憑證。](media/amazon-business-tutorial/connection-data2.png)

1. 在 [上傳您的屬性陳述式] 精靈上，按一下 [跳過]。

    ![顯示上傳您的屬性陳述式的螢幕擷取畫面，可讓您瀏覽至屬性陳述式，但是在此案例中，選取 [跳過]。](media/amazon-business-tutorial/map-attribute1.png)

1. 在 [屬性對應] 精靈上，按一下 [+ 新增欄位] 選項來新增需求欄位。 新增屬性值，包括您從 Azure 入口網站的 [使用者屬性與宣告] 區段複製到 [SAML 屬性名稱] 欄位中的命名空間，然後按 [下一步]。

    ![顯示屬性對應的螢幕擷取畫面，您可以在其中編輯您的 Amazon 資料 SAML 屬性名稱。](media/amazon-business-tutorial/map-attribute2.png)

1. 在 [ Amazon 連線資料] 精靈上，按 [下一步]。

    ![顯示 Amazon 連線資料的螢幕擷取畫面，您可以在其中按下一步以繼續作業。](media/amazon-business-tutorial/amazon-connect.png)

1. 請檢查已設定步驟的 [狀態]，然後按一下 [開始測試]。

    ![顯示 SSO 連線詳細資料的螢幕擷取畫面，其中具有用來啟動測試的選項。](media/amazon-business-tutorial/sso-connection1.png)

1. 在 [測試 SSO 連線] 精靈上，按一下 [測試]。

    ![顯示測試 SSO 連線的螢幕擷取畫面，其中具有 [測試] 按鈕。](media/amazon-business-tutorial/sso-connection2.png)

1. 在 [IDP 起始的 URL] 精靈上，按一下 [啟動] 之前，請複製指派給 **idpid** 的值，並貼到 Azure 入口網站的 [基本 SAML 組態] 區段中 [回覆 URL] 的 **idpid** 參數中。

    ![顯示 IDP 起始 URL 的螢幕擷取畫面，您可以在其中取得測試所需的 URL 然後選取 [啟動]。](media/amazon-business-tutorial/sso-connection3.png)

1. 在 [您準備好切換到使用中的 SSO 嗎?] 精靈上，勾選 [我已完整測試過 SSO 並準備好上線] 核取方塊，然後按一下 [切換到使用中]。

    ![顯示您是否準備好切換至使用中 SSO 確認的螢幕擷取畫面，您可以在其中選取 [切換] 為使用中。](media/amazon-business-tutorial/sso-connection4.png)

1. 最後在 [SSO 連線詳細資料] 區段中，[狀態] 會顯示為 [使用中]。

    ![顯示 SSO 連線詳細資料的螢幕擷取畫面，具有 [使用中] 狀態。](media/amazon-business-tutorial/sso-connection5.png)

    > [!NOTE]
    > 如果您要以 **SP** 起始模式設定應用程式，請完成下列步驟，並將上述螢幕擷取畫面中的登入 URL 貼入 Azure 入口網站中 [設定其他 URL] 區段的 [登入 URL] 文字方塊中。 請使用下列格式：
    >
    > `https://www.amazon.<TLD>/bb/feature/sso/action/start?domain_hint=<uniqueid>`

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

> [!NOTE]
> 管理員需要在其租使用者中建立測試使用者 (如有需要)。 下列步驟說明如何建立測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]、[使用者] 和 [所有使用者]。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure AD 安全性群組

1. 按一下 [Azure Active Directory] > [所有群組]。

    ![顯示 Azure 入口網站功能表的螢幕擷取畫面，其中已選取 Azure Active Directory 並且已選取 [群組] 窗格中的所有群組。](./media/amazon-business-tutorial/all-groups-tab.png)

1. 按一下 [新增群組]：

    ![顯示新增群組按鈕的螢幕擷取畫面。](./media/amazon-business-tutorial/new-group-tab.png)

1. 填寫 [群組類型]、[群組名稱]、[群組描述]、[成員資格類型]。 按一下箭頭來選取成員，然後搜尋或按一下您要新增至群組的成員。 按一下 [選取] 來新增選取的成員，然後按一下 [建立]。

    ![顯示 [群組] 窗格的螢幕擷取畫面，具有包括選取成員和邀請外部使用者的選項。](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Amazon Business 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]，然後選取 [所有應用程式]。
1. 在應用程式清單中，選取 [Amazon Business]。
1. 在應用程式的概觀頁面中尋找 [管理] 區段，然後選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![顯示新增使用者按鈕的螢幕擷取畫面。](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊的 [使用者] 清單中選取 [B.Simon]，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

    >[!NOTE]
    > 如果您未在 Azure AD 中指派使用者，則會收到下列錯誤。

    ![顯示您無法登入的錯誤訊息螢幕擷取畫面。](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>在 Azure 入口網站中指派 Azure AD 安全性群組

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [Amazon Business]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，輸入及選取 [Amazon Business]。

    ![應用程式清單中的 Amazon Business](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 搜尋您要使用的安全性群組，然後按一下該群組，將其新增至 [選取成員] 區段。 按一下 [選取]，然後按一下 [指派]。

    ![搜尋安全性群組](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > 檢查 Azure 入口網站功能表列中的通知，以取得群組已成功指派至企業應用程式的通知。

### <a name="create-amazon-business-test-user"></a>建立 Amazon Business 測試使用者

本節會在 Amazon Business 中建立名為 B.Simon 的使用者。 Amazon Business 支援依預設啟用的 Just-In-Time 使用者佈建。 在這一節沒有您需要進行的動作項目。 如果 Amazon Business 中還沒有任何使用者存在，在驗證之後就會建立新的使用者。

### <a name="test-sso"></a>測試 SSO

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 Amazon Business 圖格時，應該會自動登入您已設定 SSO 的 Amazon Business。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../user-help/my-apps-portal-end-user-access.md)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)