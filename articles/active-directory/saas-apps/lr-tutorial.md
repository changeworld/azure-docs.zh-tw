---
title: 教學課程：Azure Active Directory 與 LoginRadius 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 LoginRadius 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 1376dcb76c22bcd70937f533d337ee9679e9dc59
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455884"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>教學課程：Azure Active Directory 與 LoginRadius 整合

在本教學課程中，您將了解如何整合 LoginRadius 與 Azure Active Directory (Azure AD)。

LoginRadius 與 Azure AD 整合可提供下列優點：

* 您可以在 Azure AD 中控制可存取 LoginRadius 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 LoginRadius (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Azure AD 與 LoginRadius 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 LoginRadius 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* LoginRadius 支援 **SP** 起始的 SSO

## <a name="adding-loginradius-from-the-gallery"></a>從資源庫新增 LoginRadius

若要進行將 LoginRadius 整合到 Azure AD 中的設定，您需要從資源庫將 LoginRadius 新增至受控 SaaS 應用程式清單。

**若要從資源庫新增 LoginRadius：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左側瀏覽窗格中，選取 [Azure Active Directory] 圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增應用程式，請選取 [新增應用程式] 按鈕：

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中輸入 **LoginRadius**，在結果面板中選取 [LoginRadius]，然後選取 [新增] 按鈕以新增應用程式。

    ![結果清單中的 LoginRadius](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者為基礎，設定及測試與 LoginRadius 搭配運作的 Azure AD 單一登入。
若要讓單一登入能夠運作，必須建立 Azure AD 使用者與 LoginRadius 中相關使用者之間的連結關聯性。

若要設定及測試與 LoginRadius 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 LoginRadius 單一登入](#configure-loginradius-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
5. **[建立 LoginRadius 測試使用者](#create-loginradius-test-user)** - 讓 LoginRadius 中對應的 Britta Simon 連結到使用者在 Azure AD 中的代表項目。
6. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 LoginRadius 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [LoginRadius]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法] 窗格上選取 [SAML/WS-Fed] 模式，以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上選取 [編輯] 圖示，以開啟 [基本 SAML 設定] 窗格。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 設定] 區段上：

   ![LoginRadius 網域和 URL 單一登入資訊](common/sp-identifier.png)

   1. 在 [登入 URL] 文字方塊中，輸入 URL `https://secure.loginradius.com/login`

   1. 在 [識別碼 (實體識別碼)] 文字方塊中，輸入URL `https://lr.hub.loginradius.com/`

   1. 在 [回覆 URL (判斷提示取用者服務 URL)] 文字方塊中，輸入 LoginRadius ACS URL `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` 

5. 在 [以 SAML 設定單一登入] 頁面的 [SAML 簽署憑證] 區段中選取 [下載]，以依據您的需求從指定選項下載 [同盟中繼資料 XML]，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

6. 在 [設定 LoginRadius] 區段中，依據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

   - 登入 URL

   - Azure AD 識別碼

   - 登出 URL

## <a name="configure-loginradius-single-sign-on"></a>設定 LoginRadius 單一登入

在本節中，您會在 LoginRadius 管理主控台中啟用 Azure AD 單一登入。

1. 登入您的 LoginRadius [管理主控台](https://adminconsole.loginradius.com/login)帳戶。

2. 在 [LoginRadius 管理主控台](https://secure.loginradius.com/account/team)中，移至您的 [小組管理] 區段。

3. 選取 [單一登入] 索引標籤，然後選取 [Azure AD]：

   ![顯示 LoginRadius 小組管理主控台中的單一登入功能表的螢幕擷取畫面](./media/loginradius-tutorial/azure-ad.png)
4. 在 Azure AD 設定頁面中，完成下列步驟：

   ![顯示 LoginRadius 小組管理主控台中的 Azure Active Directory 設定的螢幕擷取畫面](./media/loginradius-tutorial/single-sign-on.png)

    1. 在 [識別碼提供者位置] 中，輸入您從 Azure AD 帳戶取得的登入端點。

    1. 在 [識別碼提供者登出 URL] 中，輸入您從 Azure AD 帳戶取得的登出端點。
 
    1. 在 [識別碼提供者憑證] 中，輸入您從 Azure AD 帳戶取得的 Azure AD 憑證。 輸入具有頁首和頁尾的憑證值。 範例： `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. 在 [服務提供者憑證] 和 [伺服器提供者憑證金鑰] 中，輸入您的憑證和金鑰。 

       您可以在命令列上執行下列命令，以建立自我簽署憑證 (Linux/Mac)：

       - 取得 SP 憑證金鑰的命令：`openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - 取得 SP 憑證的命令：`openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`
     
       > [!NOTE]
       > 請確實輸入具有頁首和頁尾的憑證和憑證金鑰值：
       > - 憑證值範例格式：`-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - 憑證金鑰值範例格式：`-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. 在 [資料對應] 區段中選取 [欄位 (SP 欄位)]，然後輸入對應的 Azure AD 欄位 (IdP 欄位)。

    以下列出 Azure AD 的部分欄位名稱。

    | 欄位    | 設定檔金鑰                                                          |
    | --------- | -------------------------------------------------------------------- |
    | 電子郵件     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | 名字 | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | 姓氏  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > [電子郵件] 欄位對應是必要項目。 [FirstName] 和 [LastName] 欄位對應是選擇性的。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者 

本節的目標是要在 Azure 入口網站中建立一個名為 "Britta Simon" 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者屬性] 中執行下列步驟。

   ![[使用者] 對話方塊](common/user-properties.png)

   1. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
   1. 在 [使用者名稱] 欄位中，輸入 `brittasimon@yourcompanydomain.extension`。 例如： BrittaSimon@contoso.com 。

   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。

   1. 選取 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 LoginRadius 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]、[所有應用程式] 及 [LoginRadius]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [LoginRadius]。

    ![應用程式清單中的 LoginRadius 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 選取 [新增使用者] 按鈕，然後在 [新增指派] 窗格中選取 [使用者和群組]。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組] 窗格的 [使用者] 清單中，選取 [Britta Simon]，然後選擇畫面底部的 [選取] 按鈕。

6. 如果您預期使用 SAML 判斷提示中的角色值，請在 [選取角色] 窗格的清單中選取適當的使用者角色。 然後選擇畫面底部的 [選取] 按鈕。

7. 在 [新增指派]  窗格中，選取 [指派]  按鈕。

### <a name="create-loginradius-test-user"></a>建立 LoginRadius 測試使用者

1. 登入您的 LoginRadius [管理主控台](https://adminconsole.loginradius.com/login)帳戶。

2. 在 LoginRadius 管理主控台中，移至您的小組管理區段。

   ![顯示 LoginRadius 管理主控台的螢幕擷取畫面](./media/loginradius-tutorial/team-management.png)
3. 選取側邊功能表中的 [新增小組成員]，以開啟表單。 

4. 在 [新增小組成員] 表單中，您可以提供使用者的詳細資料，並指派您想要讓使用者擁有的權限，藉以在 LoginRadius 網站中建立名為 Britta Simon 的使用者。 若要深入了解以角色為基礎的權限，請參閱 LoginRadius [管理小組成員](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0)文件中的[角色存取權限](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0)一節。 您必須先建立和啟動使用者，然後才能使用單一登入。

### <a name="test-single-sign-on"></a>測試單一登入 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

1. 在瀏覽器中移至 https://accounts.loginradius.com/auth.aspx ，並選取 [同盟 SSO 登入]。
2. 輸入您的 LoginRadius 應用程式名稱，然後選取 [登入]。
3. 此時應該會開啟一個快顯視窗，要求您登入您的 Azure AD 帳戶。
4. 驗證之後，快顯視窗即會關閉，而您將會登入 LoginRadius 管理主控台。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](./tutorial-list.md)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

- [什麼是 Azure Active Directory 中的條件式存取？](../conditional-access/overview.md)
