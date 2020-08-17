---
title: 教學課程：Azure Active Directory 與 Amazon Web Services (AWS) 整合以連接多個帳戶 | Microsoft Docs
description: 瞭解如何設定 Azure AD 與 Amazon Web Services (AWS)  (舊版教學課程) 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6857fba86aebbcfd7b20326e4a4a268467e4fc3
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272462"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>教學課程： Azure Active Directory 與 Amazon Web Services (AWS)  (舊版教學課程) 

在本教學課程中，您將瞭解如何整合 Azure Active Directory (Azure AD) 與 Amazon Web Services (AWS)  (舊版教學課程) 。

Amazon Web Services (AWS) 與 Azure AD 整合提供下列優點：

- 您可以在 Azure AD 中控制可存取 Amazon Web Services (AWS) 的人員。
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Amazon Web Services (AWS) (單一登入)。
- 您可以在 Azure 入口網站中集中管理您的帳戶。

如果您想要瞭解有關 SaaS 應用程式與 Azure AD 整合的更多詳細資料，請參閱 [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)。

![結果清單中的 Amazon Web Services (AWS)](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> 請注意，將一個 AWS 應用程式連線到您所有的 AWS 帳戶並不是我們建議的方法。 取而代之的是，建議您使用[這個](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)方法在 Azure AD 中將多個 AWS 帳戶執行個體設定為多個 AWS 應用程式的執行個體。 只有當您有少數 AWS 帳戶和角色時，才應該使用這種方法，此模型無法調整，因為這些帳戶內的 AWS 帳戶和角色會成長。 這種方法不會使用 Azure AD 使用者布建來使用 AWS 角色匯入功能，因此您必須手動新增/更新/刪除角色。 如需此方法的其他限制，請參閱下列詳細資料。

**請注意，我們不建議使用此方法，原因如下：**

* 您必須使用 Microsoft Graph Explorer 方法來修補應用程式的所有角色。 不建議使用資訊清單檔的方法。

* 我們曾見過客戶報告在新增 ~1200 個單一 AWS 應用程式的應用程式角色之後，應用程式上任何作業會開始擲回關於大小的錯誤。 應用程式物件的大小沒有固定限制。

* 您必須在任何帳戶中新增角色時手動更新角色，不幸地這是取代方法而非附加。 此外，如果您的帳戶有成長，那麼這也會變成帳戶和角色的 n x n 關聯性。

* 所有的 AWS 帳戶都將使用相同的同盟中繼資料 XML 檔案，且在憑證變換時，您必須驅動此大規模演練，同時更新所有 AWS 帳戶上的憑證

## <a name="prerequisites"></a>先決條件

若要設定 Azure AD 與 Amazon Web Services (AWS) 整合，您需要下列項目：

* Azure AD 訂用帳戶。 如果您沒有 Azure AD 環境，您可以在[這裡](https://azure.microsoft.com/pricing/free-trial/)取得一個月的試用帳戶
* 已啟用 Amazon Web Services (AWS) 單一登入的訂用帳戶

> [!NOTE]
> 若要測試本教學課程中的步驟，我們不建議使用生產環境。

若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則請勿使用生產環境。
- 如果您沒有 Azure AD 試用環境，您可以[取得一個月試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD 單一登入。

* Amazon Web Services (AWS) 支援由 **SP 和 IDP** 起始的 SSO
* 設定 Amazon Web Services (AWS) 您可以強制執行會話控制項，以即時防止組織的敏感性資料遭到外泄和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>從資源庫新增 Amazon Web Services (AWS)

若要設定 Amazon Web Services (AWS) 與 Azure AD 整合，您需要從資源庫將 Amazon Web Services (AWS) 新增到受控 SaaS App 清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]。
1. 在 [從資源庫新增] 區段的搜尋方塊中輸入 **Amazon Web Services (AWS)** 。
1. 從結果面板選取 [Amazon Web Services (AWS)]，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

1. 新增應用程式之後，請移至 [ **屬性** ] 頁面，並複製 **物件識別碼**。

    ![結果清單中的 Amazon Web Services (AWS)](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO

在本節中，您會以名為 "Britta Simon" 的測試使用者為基礎，設定及測試與 Amazon Web Services (AWS) 搭配運作的 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Amazon Web Services (AWS) 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Amazon Web Services (AWS) 中的相關使用者之間建立連結關聯性。

在 Amazon Web Services (AWS) 中，將 Azure AD 中**使用者名稱**的值指派為 **Username** 的值，以建立連結關聯性。

若要使用 Amazon Web Services (AWS) 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configure-azure-ad-single-sign-on)** - 讓您的使用者能夠使用此功能。
2. **[設定 Amazon Web Services (AWS) 單一登入](#configure-amazon-web-services-aws-single-sign-on)** - 在應用程式端設定單一登入設定。
3. **[測試單一登入](#test-single-sign-on)** ，驗證組態是否能運作。

### <a name="configure-azure-ad-single-sign-on"></a>設定 Azure AD 單一登入

在本節中，您會在 Azure 入口網站中啟用 Azure AD 單一登入，然後在您的 Amazon Web Services (AWS) 應用程式中設定單一登入。

**若要使用 Amazon Web Services (AWS) 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Amazon Web Services (AWS)]**** 應用程式整合頁面上，選取 [單一登入]****。

    ![設定單一登入連結](common/select-sso.png)

2. 在 [選取單一登入方法]  對話方塊中，選取 [SAML/WS-Fed]  模式以啟用單一登入。

    ![單一登入選取模式](common/select-saml-option.png)

3. 在 [以 SAML 設定單一登入] 頁面上，按一下 [編輯] 圖示以開啟 [基本 SAML 設定] 對話方塊。

    ![編輯基本 SAML 組態](common/edit-urls.png)

4. 在 [ **基本 SAML** 設定] 區段上，使用者不需要執行任何步驟，因為應用程式已預先與 Azure 整合，然後按一下 [ **儲存**]。

5. Amazon Web Services (AWS) 應用程式會預期要有特定格式的 SAML 判斷提示。 設定此應用程式的下列宣告。 您可以在應用程式整合頁面的 [使用者屬性與宣告] 區段中管理這些屬性的值。 在 [ **以 SAML 設定單一登入** ] 頁面上，按一下 [ **編輯** ] 按鈕，以開啟 [ **使用者屬性 & 宣告** ] 對話方塊。

    ![image](common/edit-attribute.png)

6. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，如上圖所示設定 SAML 權杖屬性，然後執行下列步驟：

    | 名稱  | 來源屬性  | 命名空間 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | 角色            | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration             | 「提供 900 秒 (15 分鐘) 到 43200 秒 (12 小時) 之間的值」 |  `https://aws.amazon.com/SAML/Attributes` |

    a. 按一下 [新增宣告] 以開啟 [管理使用者宣告] 對話方塊。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. 在 [名稱] 文字方塊中，輸入該資料列所顯示的屬性名稱。

    c. 在 [命名空間]**** 文字方塊中，輸入該資料列顯示的命名空間值。

    d. 選取 [來源] 作為 [屬性]。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 按一下 [確定]。

    g. 按一下 [檔案] 。

7. 在 [以 SAML 設定單一登入]**** 頁面上的 [SAML 簽署憑證]**** 區段中，按一下 [下載]**** 以下載**同盟中繼資料 XML** 並將其儲存在電腦上。

    ![憑證下載連結](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>設定 Amazon Web Services (AWS) 單一登入

1. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 Amazon Web Services (AWS) 公司網站。

1. 按一下 [AWS Home] \(AWS 首頁\)****。

    ![設定單一登入首頁][11]

1. 按一下 [身分 **識別與存取管理**]。

    ![設定單一登入身分識別][12]

1. 按一下 [識別提供者]****，然後按一下 [建立提供者]****。

    ![設定單一登入提供者][13]

1. 在 [設定提供者] **** 對話方塊頁面上，執行下列步驟：

    ![設定單一登入對話方塊][14]

    a. 針對 [提供者類型]****，選取 [SAML]****。

    b. 在 [提供者名稱]**** 文字方塊中，鍵入提供者名稱 (例如：WAAD**)。

    c. 若要上傳從 Azure 入口網站下載的**中繼資料檔案**，請按一下 [選擇檔案]****。

    d. 按一下頁面底部的 [新增] ****。

1. 在 [驗證提供者資訊]**** 對話方塊頁面上，按一下 [建立]****。

    ![設定單一登入驗證][15]

1. 按一下 [Roles] \(角色\)****，然後按一下 [Create role] \(建立角色\)****。

    ![設定單一登入角色][16]

1. 在 [Create role] \(建立角色\) 頁面上，執行下列步驟：  

    ![設定單一登入信任][19]

    a. 在 [Select type of trusted entity] \(選取信任的實體類型\)**** 底下，選取 [SAML 2.0 federation] \(SAML 2.0 同盟\)****。

    b. 在 [Choose a SAML 2.0 Provider] \(選擇 SAML 2.0 提供者\)**** 區段底下，選取您先前建立的 [SAML provider] \(SAML 提供者\)**** (例如：*WAAD*)

    c. 選取 [Allow programmatic and AWS Management Console access] \(允許透過程式設計方式和 AWS 管理主控台存取\)。
  
    d. 按一下 [Next: Permissions] \(下一步：權限\)****。

1. 在搜尋列中搜尋 **系統管理員存取權** ，然後選取 [ **AdministratorAccess** ] 核取方塊，然後按 **[下一步：標記]**。

    ![選取系統管理員存取權](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. 在 [ **將標籤新增 (選用) ** ] 區段中，執行下列步驟：

    ![選取系統管理員存取權](./media/aws-multi-accounts-tutorial/config2.png)

    a. 在 [索引 **鍵** ] 文字方塊中，輸入 Ex： Azureadtest 的索引鍵名稱。

    b. 在 [ ** (選用) ** ] 文字方塊中，使用下列格式輸入機碼值 `accountname-aws-admin` 。 帳戶名稱必須全部小寫。

    c. 按 **[下一步：檢查]**。

1. 在 [檢閱] **** 對話方塊上，執行下列步驟：

    ![設定單一登入檢閱][34]

    a. 在 [ **角色名稱** ] 文字方塊中，以下列模式輸入值 `accountname-aws-admin` 。

    b. 在 [ **角色描述** ] 文字方塊中，輸入您用於角色名稱的相同值。

    c. 按一下 [建立角色]****。

    d. 建立所需數量的角色，並將它們對應至識別提供者。

    > [!NOTE]
    > 同樣地，請建立其餘的其他角色，例如 accountname-財務-系統管理員、accountname-唯讀-使用者、accountname-devops-使用者、accountname-tpm-具有不同原則的使用者。 之後也可以根據每個 AWS 帳戶的需求來變更這些角色原則，但一律最好在 AWS 帳戶中保留每個角色的相同原則。

1. 請記下該 AWS 帳戶的帳戶識別碼，方法是從 EC2 屬性或 IAM 儀表板，如下所示：

    ![選取系統管理員存取權](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. 現在登入 [Azure 入口網站](https://portal.azure.com/) 並流覽至 [ **群組**]。

1. 使用與稍早建立的 IAM 角色相同的名稱建立新群組，並記下這些新群組的 **物件識別碼** 。

    ![選取系統管理員存取權](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. 從目前的 AWS 帳戶登出，然後以您要設定 Azure AD 的單一登入的其他帳戶登入。

1. 在帳戶中建立所有角色後，角色會顯示在這些帳戶的 [角色]**** 清單中。

    ![角色設定](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. 對於跨所有帳戶的各個角色，我們必須擷取所有角色 ARN 和受信任的實體，因為我們需要手動將其與 Azure AD 應用程式對應。

1. 按一下角色以複製 [角色 ARN]**** 和 [信任實體]**** 值。 對於您必須在 Azure AD 中建立的所有角色，您都需要這些值。

    ![角色設定](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. 對所有帳戶中的所有角色執行上述步驟，並以**角色 ARN,信任實體**的格式將其全部儲存在記事本中。

1. 在另一個視窗中開啟 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 。

    a. 使用租使用者的全域管理員/共同管理員認證來登入 Microsoft Graph Explorer 網站。

    b. 您必須有足夠的權限才能建立角色。 按一下 [修改權限]**** 以取得必要的權限。

    ![Microsoft Graph Explorer 對話方塊](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. 從清單中選取下列權限 (如果您還沒有)，然後按一下 [修改權限] 

    ![Microsoft Graph Explorer 對話方塊](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. 此時系統會要求您重新登入並接受此要求。 接受同意之後，您會再次登入 Microsoft Graph Explorer。

    e. 將版本下拉式清單變更為 **Beta**。 若要從您的租用戶擷取所有服務主體，請使用下列查詢：

    `https://graph.microsoft.com/beta/servicePrincipals`

    如果您使用多個目錄，則可以使用下列模式，其中包含您的主要網域：`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Microsoft Graph Explorer 對話方塊](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. 從所擷取的服務主體清單中，取得您需要修改的服務主體。 您也可以使用 Ctrl+F，從所有列出的服務主體中搜尋應用程式。 您可以使用下列查詢，方法是使用您從 Azure AD [屬性] 頁面複製的 **服務主體物件識別碼** ，以取得個別的服務主體。

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Microsoft Graph Explorer 對話方塊](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. 從服務主體物件擷取 appRoles 屬性。

    ![Microsoft Graph Explorer 對話方塊](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. 現在，您必須為您的應用程式產生新角色。 

    i. 以下 JSON 是 appRoles 物件的範例。 請建立類似的物件，以新增您的應用程式所需的角色。

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > 您只能在修補作業的 **msiam_access** 之後新增角色。 此外，您可以根據組織的需求新增任意數目的角色。 Azure AD 會在 SAML 回應中以宣告值的形式傳送這些角色的**值**。

    j. 返回至您的 Microsoft Graph Explorer，並將方法從 **GET** 變更為 **PATCH**。 藉由將 appRoles 屬性更新為前述範例中的類似屬性，將服務主體物件修補成具有所需的角色。 按一下 [執行查詢]**** 以執行修補作業。 此時會出現成功訊息，確認 Amazon Web Services 應用程式的角色已建立。

    ![Microsoft Graph Explorer 對話方塊](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. 以更多角色修補服務主體之後，您即可將使用者/群組指派給各自的角色。 移至入口網站，然後瀏覽至 Amazon Web Services 應用程式，即可完成此作業。 按一下頂端的 [使用者和群組]**** 索引標籤。

1. 建議您為每個 AWS 角色建立新群組，以便在該群組中指派這個角色。 請注意，這是一個群組與一個角色之間的一對一對應。 接著，您可以新增屬於該群組的成員。

1. 群組建立後，請選取群組並指派給應用程式。

    ![設定單一登入的新增](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > 指派群組時，不支援巢狀群組。

1. 若要將角色指派給群組，請選取角色，然後按一下頁面底部的 [指派]**** 按鈕。

    ![設定單一登入的新增](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > 請注意，您必須在 Azure 入口網站中重新整理您的工作階段，才能看到新角色。

### <a name="test-single-sign-on"></a>測試單一登入

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Amazon Web Services (AWS)] 圖格時，應該會進入有選項可供選取角色的 Amazon Web Services (AWS) 應用程式頁面。

![設定單一登入的新增](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

您也可以驗證 SAML 回應，以查看以宣告的形式傳遞的角色。

![設定單一登入的新增](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

如需「存取面板」的詳細資訊，請參閱[存取面板簡介](../active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他資源

* [如何使用 MS Graph Api 設定布建](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)
* [什麼是 Microsoft Cloud App Security 中的工作階段控制項？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
* [如何使用進階可見性和控制項保護 Amazon Web Services (AWS)](https://docs.microsoft.com/cloud-app-security/protect-aws)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/