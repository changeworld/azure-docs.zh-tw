---
title: 教學課程：Azure Active Directory 單一登入 (SSO) 與 Alibaba Cloud Service (角色型 SSO) 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Alibaba Cloud Service (角色型 SSO) 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: ef057b64fa04387d0ebec5a6e5c38f0e06c55a7d
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368965"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>教學課程：Azure Active Directory 單一登入 (SSO) 與 Alibaba Cloud Service (角色型 SSO) 整合

在本教學課程中，您將了解如何整合 Alibaba Cloud Service (角色型 SSO) 與 Azure Active Directory (Azure AD)。 當您將 Alibaba Cloud Service (角色型 SSO) 與 Azure AD 整合時，您可以：

* 在 Azure AD 中控制可存取 Alibaba Cloud Service (角色型 SSO) 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Alibaba Cloud Service (角色型 SSO)。
* 在 Azure 入口網站集中管理您的帳戶。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Alibaba Cloud Service (角色型 SSO) 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Alibaba Cloud Service (角色型 SSO) 支援由 **IDP** 起始的 SSO

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>從資源庫新增 Alibaba Cloud Service (角色型 SSO)

若要設定將 Alibaba Cloud Service (角色型 SSO) 整合到 Azure AD 中，您必須將 Alibaba Cloud Service (角色型 SSO) 從資源庫新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 Azure 入口網站。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Alibaba Cloud Service (角色型 SSO)** 。
1. 從結果面板中選取 [Alibaba Cloud Service (角色型 SSO)]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。
5. 在 [Alibaba Cloud Service (角色型 SSO)]  頁面上，按一下左側瀏覽窗格中的 [屬性]  ，然後複製 **物件識別碼** 並將其儲存在電腦上以供後續使用。

    ![屬性設定](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-sso-for-alibaba-cloud-service-role-based-sso"></a>設定及測試適用於 Alibaba Cloud Service (角色型 SSO) 的 Azure AD SSO

以名為 **B.Simon** 的測試使用者，設定及測試與 Alibaba Cloud Service (角色型 SSO) 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Alibaba Cloud Service (角色型 SSO) 中相關使用者之間的連結關聯性。

若要設定與 Alibaba Cloud Service (角色型 SSO) 搭配運作的 Azure AD SSO 並且進行測試，請執行下列步驟：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
    1. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 Britta Simon 測試 Azure AD 單一登入。
    1. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 Britta Simon 能夠使用 Azure AD 單一登入。
2. **[在 Alibaba Cloud Service 中設定角色型單一登入](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** - 讓您的使用者能夠使用此功能。
    1. **[設定 Alibaba Cloud Service (角色型 SSO) 的 SSO](#configure-alibaba-cloud-service-role-based-sso-sso)** - 在應用程式端設定單一登入設定。
    1. **[建立 Alibaba Cloud Service (角色型 SSO) 測試使用者](#create-alibaba-cloud-service-role-based-sso-test-user)** ：使 Alibaba Cloud Service (角色型 SSO) 中對應的 Britta Simon 連結到該使用者在 Azure AD 中的代表項目。
3. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 Azure 入口網站 的 [Alibaba Cloud Service (角色型 SSO)] 應用程式整合頁面上，找到 **管理** 區段並選取 [單一登入]。
1. 在 [**選取單一登入方法**] 頁面上，選取 [**SAML**]。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

4. 如果您有 **服務提供者中繼資料檔案**，請在 [基本 SAML 設定]  區段上執行下列步驟：

    a. 按一下 [上傳中繼資料檔案]  。

    b. 按一下 **資料夾圖示** 以選取中繼資料檔案，然後按一下 [上傳]  。

    
    >[!NOTE]
    >1. 針對 Alibaba Cloud 國際網站，請從[這個](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)連結下載服務提供者中繼資料。
    > 1. 針對 Alibaba Cloud Service (CN) 網站，請從[這個](https://signin.aliyun.com/saml-role/sp-metadata.xml)連結下載服務提供者中繼資料。

    c. 當中繼資料檔案成功上傳後，會自動在 [Alibaba Cloud Service (角色型 SSO)] 區段文字方塊中填入 [識別碼]  及 [回覆 URL]  值：

    > [!Note]
    > 如果 [識別碼]  和 [回覆 URL]  值未自動填入，則請根據您的需求手動填入這些值。

1. Alibaba Cloud Service (角色型 SSO) 需要在 Azure AD 中設定角色。 角色宣告已預先設定，因此您不需要進行設定，但您仍然需要使用[本文](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui)在 Azure AD 中建立角色。

1. 在 [以 SAML 設定單一登入]  頁面上的 [SAML 簽署憑證]  區段中，尋找 [同盟中繼資料 XML]  ，然後選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

1. 在 [設定 Alibaba Cloud Service (角色型 SSO)]  區段上，依據您的需求複製適當的 URL。

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

在本節中，您會將 Alibaba Cloud Service (角色型 SSO) 的存取權授與 B.Simon，讓其能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Alibaba Cloud Service (角色型 SSO)]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。
1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。
1. 在 [使用者和群組]  索引標籤上，選取使用者清單中的 u2，然後按一下 [選取]  。 然後，按一下 [指派]  。

    ![指派 Azure AD 測試使用者1](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. 檢視指派的角色並測試 Alibaba Cloud Service (角色型 SSO)。

    ![指派 Azure AD 測試使用者2](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >在指派使用者 (u2) 後，所建立的角色會自動連結至使用者。 如果您已建立多個角色，則必須視需要將適當的角色連結至使用者。 如果您想要將角色型 SSO 從 Azure AD 實作到多個 Alibaba 雲端帳戶，請重複上述步驟。

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>在 Alibaba Cloud Service 中設定角色型單一登入

1. 使用 Account1 登入 Alibaba Cloud [RAM 主控台](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9)。

2. 在左側瀏覽窗格中，選取 [SSO]  。

3. 在 [角色型 SSO]  索引標籤上，按一下 [建立 IdP]  。

4. 在顯示的頁面上，於 [IdP 名稱] 欄位中輸入 `AAD`，於 [附註]  欄位中輸入描述，按一下 [上傳]  來上傳您之前下載的同盟中繼資料檔案，然後按一下 [確定]  。

5. 成功建立 IdP 後，按一下 [建立 RAM 角色]  。

6. 在 [RAM 角色名稱]  欄位中輸入 `AADrole`，從 [選取 IdP]  下拉式清單中選取 `AAD`，然後按一下 [確定]。

    >[!NOTE]
    >您可以視需要對角色授與權限。 在建立 IdP 和對應的角色之後，建議您儲存 IdP 和角色的 ARN 以供後續使用。 您可以在 [IdP 資訊] 頁面和 [角色資訊] 頁面上取得 ARN。

7. 將 Alibaba Cloud RAM 角色 (AADrole) 與 Azure AD 使用者 (u2) 建立關聯：若要將 RAM 角色與 Azure AD 使用者建立關聯，您必須依照下列步驟在 Azure AD 中建立角色：

    a. 登入 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)。

    b. 按一下 [修改權限]  以取得用於建立角色的必要權限。

    ![圖表設定1](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. 從清單中選取下列權限，然後按一下 [修改權限]  ，如下圖所示。

    ![圖表設定2](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >授與權限之後，再次登入 Graph 總管。

    d. 在 [Graph 總管] 頁面上，選取第一個下拉式清單中的 [GET]  和第二個下拉式清單中的 [beta]  。 然後在下拉式清單旁邊的欄位中輸入 `https://graph.microsoft.com/beta/servicePrincipals`，並按一下 [執行查詢]  。

    ![圖表設定3](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >如果您要使用多個目錄，則可以在查詢的欄位中輸入 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`。

    e. 在 [回應預覽]  區段中，從 [服務主體] 擷取 appRoles 屬性以供後續使用。

    ![圖表設定4](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >您可以在查詢的欄位中輸入 `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` 來找到 appRoles 屬性。 請注意，`objectID` 是您從 Azure AD 的 [屬性]  頁面所複製的物件識別碼。

    f. 返回 [Graph 總管]，將方法從 **GET** 變更為 **PATCH**，將下列內容貼到 [要求本文]  區段，然後按一下 [執行查詢]  ：
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > `value` 是您在 RAM 主控台中所建立 IdP 和角色的 ARN。 在這裡，您可以視需要新增多個角色。 Azure AD 會在 SAML 回應中以宣告值的形式傳送這些角色的值。 但是，您只能在修補作業的 `msiam_access` 部分之後新增角色。 若要讓建立程序變得順利，建議您使用識別碼產生器 (例如 GUID 產生器) 來即時產生識別碼。

    g. [服務主體] 在使用必要角色進行修補之後，請遵循教學課程中 **指派 Azure AD 測試使用者** 一節的步驟將角色連結至 Azure AD 使用者 (u2)。

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>設定 Alibaba Cloud Service (角色型 SSO) 的 SSO

若要在 **Alibaba Cloud Service (角色型 SSO)** 端設定單一登入，您必須將從 Azure 入口網站下載的 [同盟中繼資料 XML]  和所複製的適當 URL 傳送給 [Alibaba Cloud Service (角色型 SSO) 支援小組](https://www.aliyun.com/service/)。 他們會進行此設定，讓兩端的 SAML SSO 連線都設定正確。

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>建立 Alibaba Cloud Service (角色型 SSO) 測試使用者

在本節中，您會在 Alibaba Cloud Service (角色型 SSO) 中建立名為 Britta Simon 的使用者。 請與 [Alibaba Cloud Service (角色型 SSO) 支援小組](https://www.aliyun.com/service/)合作，在 Alibaba Cloud Service (角色型 SSO) 平台中新增使用者。 您必須先建立和啟動使用者，然後才能使用單一登入。

## <a name="test-sso"></a>測試 SSO 

完成上述設定後，請遵循下列步驟來測試 Alibaba Cloud Service (角色型 SSO)：

1. 在 Azure 入口網站中，移至 [Alibaba Cloud Service (角色型 SSO)]  頁面、選取 [單一登入]  ，然後按一下 [測試]  。

    ![測試設定1](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. 按一下 [以目前使用者身分登入]  。

    ![測試設定2](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. 在 [帳戶選取] 頁面中，選取 [u2]。

    ![測試設定3](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. 隨即會顯示下列頁面，指出角色型 SSO 已成功。

    ![測試設定4](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="next-steps"></a>後續步驟

設定 Alibaba Cloud Service (角色型 SSO) 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](/cloud-app-security/proxy-deployment-any-app)。