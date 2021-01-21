---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 NetDocuments 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 NetDocuments 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 1c5fa1c704e51026d5cd4defdb7f5e85827ccb9b
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624787"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 NetDocuments 整合

在此教學課程中，您將了解如何整合 NetDocuments 與 Azure Active Directory (Azure AD)。 在整合 NetDocuments 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 NetDocuments 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 NetDocuments。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 NetDocuments 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* NetDocuments 支援由 **SP** 起始的 SSO

## <a name="adding-netdocuments-from-the-gallery"></a>從資源庫新增 NetDocuments

若要設定將 NetDocuments 整合到 Azure AD 中，您需要從資源庫將 NetDocuments 新增到受控 SaaS app 清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中輸入 [NetDocuments]  。
1. 從結果面板選取 [NetDocuments]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso-for-netdocuments"></a>設定及測試 Azure AD SSO for NetDocuments

以名為 **B.Simon** 的測試使用者，設定及測試與 NetDocuments 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 NetDocuments 中相關使用者之間的連結關聯性。

若要設定及測試與 NetDocuments 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
1. **[設定 NetDocuments SSO](#configure-netdocuments-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 NetDocuments 測試使用者](#create-netdocuments-test-user)** - 使 NetDocuments 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站的 [ **NetDocuments** ] 應用程式整合頁面上，尋找 [ **管理** ] 區段並選取 [ **單一登入**]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，輸入下列欄位的值：

    a. 在 [登入 **URL** ] 文字方塊中，輸入下列其中一個 URL 模式：

    |登入 URL|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    b. 在 [ **識別碼 (實體識別碼)** ] 文字方塊中，輸入其中一個 url：

    |識別碼|
    |-----------|
    |`http://netdocuments.com/VAULT`|
    |`http://netdocuments.com/EU`|
    |`http://netdocuments.com/AU`|
    |`http://netdocuments.com/DE`|
    |

    c. 在 [ **回復 URL** ] 文字方塊中，輸入下列其中一個 URL 模式：

    |回覆 URL|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    > [!NOTE]
    > 這些都不是真正的值。 請使用實際的「登入 URL」和「回覆 URL」來更新這些值。 存放庫識別碼是一個以 **CA-** 開頭，後面再加上與您 NetDocuments 存放庫相關聯的 8 字元代碼所組成的值。 您可以查看 [NetDocuments 同盟身分識別支援文件](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login)，以取得詳細資訊。 或者，如果您無法使用上述資訊進行設定，您可以連絡 [NetDocuments 用戶端支援小組](https://support.netdocuments.com/hc/)以取得這些值。 您也可以參考 Azure 入口網站中 **基本 SAML 組態** 區段所示的模式。

1. NetDocuments 應用程式需要特定格式的 SAML 判斷提示，因此您必須將自訂屬性對應新增至 SAML 權杖屬性組態中。 下列螢幕擷取畫面顯示預設屬性清單，其中的 **nameidentifier** 與 **user.userprincipalname** 相對應。 NetDocuments 應用程式預期 **nameidentifier** 必須與 **ObjectID** 或任何其他宣告（適用于您的組織做為 **nameidentifier**）對應，因此您必須按一下 [ **編輯** ] 圖示來編輯屬性對應，並變更屬性對應。

    ![image](common/edit-attribute.png)

1. 在 [ **以 Saml 設定單一登入** ] 頁面的 [ **saml 簽署憑證** ] 區段中，尋找 [ **應用程式同盟中繼資料 url** ] 並複製 URL。

    ![憑證下載連結](common/copy-metadataurl.png)

1. 在 [安裝 NetDocuments]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 NetDocuments 的存取權授與 B.Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [NetDocuments]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您需要將角色指派給使用者，您可以從 [選取角色] 下拉式清單中選取。 如果未設定此應用程式的角色，您會看到已選取 [預設存取] 角色。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

## <a name="configure-netdocuments-sso"></a>設定 NetDocuments SSO

1. 在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 NetDocuments 公司網站。

2. 在右上角選取您的名稱>**Admin**。

3. 選取 [資訊安全中心]  。
   
    ![存放庫](./media/netdocuments-tutorial/security-center.png "資訊安全中心")

4. 選取 [進階驗證]  。
    
    ![設定進階驗證選項](./media/netdocuments-tutorial/advance-authentication.png "設定進階驗證選項")

5.  在 [同盟識別碼]  索引標籤上，執行下列步驟：   
   
    [同盟身分 ![識別](./media/netdocuments-tutorial/federated-id.png "同盟身分識別")](./media/netdocuments-tutorial/federated-id.png#lightbox)
   
    a. 針對 [同盟 **識別伺服器類型**]，選取 [為 **Windows Azure Active Directory**]。
    
    b.  選取 [選擇檔案]  ，上傳您從 Azure 入口網站下載的中繼資料檔案。
    
    c.  選取 [儲存]  。

### <a name="create-netdocuments-test-user"></a>建立 NetDocuments 測試使用者

為了讓 Azure AD 使用者能夠登入 NetDocuments，必須將他們佈建到 NetDocuments 中。 在 NetDocuments 的情況下，需以手動方式佈建。

**若要佈建使用者帳戶，請執行下列步驟：**

1. 以系統管理員身分登入 **NetDocuments** 公司網站。

2. 在右上角選取您的名稱>**Admin**。
   
    ![管理員](./media/netdocuments-tutorial/user-admin.png "管理")

3. 選取 [使用者和群組]  。
   
    ![使用者和群組](./media/netdocuments-tutorial/users-groups.png "Repository")

4. 在 [電子郵件地址]  文字方塊中輸入您想要佈建的有效 Azure Active Directory 帳戶電子郵件地址，然後按一下 [新增使用者]  。
   
    ![電子郵件地址](./media/netdocuments-tutorial/user-mail.png "電子郵件地址")
   
    > [!NOTE]
    > Azure Active Directory 帳戶的持有者會收到一封電子郵件，其中中包含在帳戶變成作用中之前確認帳戶的連結。 您可以使用任何其他的 NetDocuments 使用者帳戶建立工具或 NetDocuments 提供的 API 來佈建 Azure Active Directory 使用者帳戶。

## <a name="test-sso"></a>測試 SSO 

在本節中，您會使用下列選項來測試您的 Azure AD 單一登入組態。 

* 在 Azure 入口網站中按一下 [測試此應用程式]。 這會重新導向至 NetDocuments 登入 URL，您可以在其中起始登入流程。 

* 直接前往 NetDocuments 登入 URL，並從該處起始登入流程。

* 您可以使用 Microsoft 的「我的應用程式」。 當您在我的應用程式中按一下 [NetDocuments] 圖格時，應該會自動登入您已設定 SSO 的 NetDocuments。 如需「我的應用程式」的詳細資訊，請參閱[我的應用程式簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。


## <a name="next-steps"></a>後續步驟

設定 NetDocuments 後，您可以強制執行會話控制項，以即時防止組織的敏感性資料遭到外泄和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。
