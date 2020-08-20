---
title: 教學課程：Azure Active Directory 與 Rackspace SSO 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Rackspace SSO 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: a2c2e7358c32453daf53c40a9322df4fe30642d8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548874"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>教學課程：Azure Active Directory 與 Rackspace SSO 整合

在本教學課程中，您會了解如何整合 Rackspace SSO 與 Azure Active Directory (Azure AD)。
Rackspace SSO 與 Azure AD 整合提供下列優點：

* 您可以在 Azure AD 中控制可存取 Rackspace SSO 的人員。
* 您可以讓使用者使用其 Azure AD 帳戶自動登入 Rackspace SSO (單一登入)。
* 您可以在 Azure 入口網站中集中管理您的帳戶。

若您想了解 SaaS app 與 Azure AD 整合的更多詳細資訊，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要設定 Rackspace SSO 與 Azure AD 的整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以申請[免費帳戶](https://azure.microsoft.com/free/)
* 已啟用 Rackspace SSO 單一登入的訂用帳戶

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Rackspace SSO 支援 **SP** 起始的 SSO

## <a name="adding-rackspace-sso-from-the-gallery"></a>從資源庫新增 Rackspace SSO

若要設定將 Rackspace SSO 整合到 Azure AD 中，您需要從資源庫將 Rackspace SSO 新增到受控 SaaS 應用程式清單。

**若要從資源庫新增 Rackspace SSO，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左方瀏覽窗格中，按一下 [Azure Active Directory]  圖示。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 瀏覽至 [企業應用程式]  ，然後選取 [所有應用程式]  選項。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請按一下對話方塊頂端的 [新增應用程式]  按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入 **Rackspace SSO**，從結果面板中選取 [Rackspace SSO]  ，然後按一下 [新增]  按鈕以新增應用程式。

     ![結果清單中的 Rackspace SSO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

在本節中，您會以名為 **Britta Simon** 的測試使用者身分，使用 Rackspace SSO 設定及測試 Azure AD 單一登入。
使用 Rackspace 單一登入時，系統會在 Rackspace 使用者第一次登入 Rackspace 入口網站時自動為其建立帳戶。 

若要設定及測試與 Rackspace SSO 搭配運作的 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Rackspace SSO 單一登入](#configure-rackspace-sso-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[在 Rackspace 控制台中設定屬性對應](#set-up-attribute-mapping-in-the-rackspace-control-panel)** - 將 Rackspace 角色指派給 Azure AD 使用者。
1. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入。

若要設定與 Rackspace SSO 搭配運作的 Azure AD 單一登入，請執行下列步驟：

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Rackspace SSO]  應用程式整合頁面上，選取 [單一登入]  。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。   

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [基本 SAML 組態]  區段上，上傳可從 [URL](https://login.rackspace.com/federate/sp.xml) 下載的**服務提供者中繼資料檔案**，然後執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]  。

    ![image](common/upload-metadata.png)

    b. 按一下**資料夾圖示**以選取中繼資料檔案，然後按一下 [上傳]  。

    ![image](common/browse-upload-metadata.png)

    c. 成功上傳中繼資料檔案後，就會自動填入所需的 URL。

    d. 在 [登入 URL]  文字方塊中，輸入 URL：`https://login.rackspace.com/federate/`

    ![Rackspace SSO 網域與 URL 單一登入資訊](common/sp-signonurl.png)   

5. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中按一下 [下載]  ，以依據您的需求從指定選項下載**同盟中繼資料 XML**，並儲存在您的電腦上。

    ![憑證下載連結](common/metadataxml.png)

此檔案會上傳至 Rackspace 來填入所需的身分識別同盟組態設定。

### <a name="configure-rackspace-sso-single-sign-on"></a>設定 Rackspace SSO 單一登入

若要在 **Rackspace SSO** 端設定單一登入：

1. 請參閱[在控制台新增識別提供者](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)上的文件
1. 其會引導您完成下列相關步驟：
    1. 建立新的識別提供者
    1. 指定使用者將會在登入時用來識別貴公司的電子郵件網域。
    1. 上傳先前從 Azure 控制台下載的**同盟中繼資料 XML**。

這會正確地設定要讓 Azure 和 Rackspace 能夠連線所需的基本 SSO 設定。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

本節的目標是要在 Azure 入口網站中建立一個名為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。

    ![[使用者和群組] 與 [所有使用者] 連結](common/users.png)

2. 在畫面頂端選取 [新增使用者]  。

    ![[新增使用者] 按鈕](common/new-user.png)

3. 在 [使用者] 屬性中，執行下列步驟。

    ![[使用者] 對話方塊](common/user-properties.png)

    a. 在 [名稱]  欄位中，輸入 **BrittaSimon**。
  
    b. 在 [使用者名稱]  欄位中，輸入 `brittasimon@yourcompanydomain.extension`。 例如， BrittaSimon@contoso.com

    c. 選取 [顯示密碼]  核取方塊，然後記下 [密碼] 方塊中顯示的值。

    d. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Rackspace SSO 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，依序選取 [企業應用程式]  、[所有應用程式]  及 [Rackspace SSO]  。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 [Rackspace SSO]  。

    ![應用程式清單中的 Rackspace SSO 連結](common/all-applications.png)

3. 在左側功能表中，選取 [使用者和群組]  。

    ![[使用者和群組] 連結](common/users-groups-blade.png)

4. 按一下 [新增使用者]  按鈕，然後在 [新增指派]  對話方塊中，選取 [使用者和群組]  。

    ![[新增指派] 窗格](common/add-assign-user.png)

5. 在 [使用者和群組]  對話方塊的 [使用者] 清單中，選取 [Britta Simon]  ，然後按一下畫面底部的 [選取]  按鈕。

6. 如果您預期使用 SAML 判斷提示中的任何角色值，請在 [選取角色]  對話方塊的清單中選取適當使用者角色，然後按一下畫面底部的 [選取]  按鈕。

7. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>在 Rackspace 控制台中設定屬性對應

Rackspace 使用**屬性對應原則**來將 Rackspace 角色和群組指派給單一登入使用者。 **屬性對應原則**會將 Azure AD SAML 宣告轉譯成 Rackspace 所需的使用者設定欄位。 您可以在 Rackspace 的[屬性對應基本概念文件](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)中找到更多文件。 一些考量事項如下：

* 如果您想要使用 Azure AD 群組來指派各種 Rackspace 存取層級，則必須在 Azure 的 **Rackspace SSO** 單一登入設定中啟用群組宣告。 然後，系統就會使用**屬性對應原則**來比對這些群組與所需的 Rackspace 角色和群組：

    ![群組宣告設定](common/sso-groups-claim.png)

* 根據預設，Azure AD 會在 SAML 宣告中傳送 Azure AD 群組的 UID 而非群組的名稱。 不過，如果您要將內部部署 Active Directory 同步至 Azure AD，則可以選擇傳送群組的實際名稱：

    ![群組宣告名稱設定](common/sso-groups-claims-names.png)

下例**屬性對應原則**範例會示範：
1. 將 Rackspace 使用者的名稱設定為 `user.name` SAML 宣告。 可使用任何宣告，但最常會將此宣告設定為包含使用者電子郵件地址的欄位。
1. 藉由比對 Azure AD 群組 (根據群組名稱或群組 UID)，來對使用者設定 Rackspace 角色 `admin` 和 `billing:admin`。 `roles` 欄位中的 `"{0}"` 會使用「替代字元」  ，並由 `remote` 規則運算式的結果加以取代。
1. 使用 `"{D}"`「預設替代字元」  讓 Rackspace 藉由尋找 SAML 交換中的標準與已知 SAML 宣告來擷取其他 SAML 欄位。

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> 在編輯原則檔時，請務必使用會驗證 YAML 語法的文字編輯器。

如需更多範例，請參閱 Rackspace 的[屬性對應基本概念文件](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Rackspace SSO] 圖格時，應該會自動登入您已設定 SSO 的 Rackspace SSO。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

您也可以使用 **Rackspace SSO** 單一登入設定中的 [驗證]  按鈕：

   ![SSO 驗證按鈕](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

