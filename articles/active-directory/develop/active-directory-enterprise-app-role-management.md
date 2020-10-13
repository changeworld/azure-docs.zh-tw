---
title: 設定企業 Azure AD 應用程式的角色宣告 |蔚藍
titleSuffix: Microsoft identity platform
description: 了解如何針對 Azure Active Directory 中的企業應用程式設定 SAML 權杖中發出的角色宣告
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: 8ec87a8d78f73af48b662c5971dfe1803717f88a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704543"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>操作說明：針對企業應用程式，設定 SAML 權杖中發出的角色宣告

藉由使用 Azure Active Directory (Azure AD)，您可以針對在授權應用程式之後所收到回應權杖中的角色宣告，自訂其宣告類型。

## <a name="prerequisites"></a>必要條件

- 具有目錄設定的 Azure AD 訂用帳戶。
- 已啟用單一登入 (SSO) 的訂用帳戶。 您必須設定與您應用程式搭配運作的 SSO。

## <a name="when-to-use-this-feature"></a>使用此功能的時機

如果您的應用程式預期在 SAML 回應中傳遞自訂角色，您就需要使用此功能。 您可以依需求建立多個要從 Azure AD 傳回給應用程式的角色。

## <a name="create-roles-for-an-application"></a>建立應用程式的角色

1. 在 [Azure 入口網站](https://portal.azure.com)的左側窗格中，選取 [Azure Active Directory]**** 圖示。

    ![Azure Active Directory 圖示][1]

2. 選取 [企業應用程式]。 接著，選取 [所有應用程式]****。

    ![企業應用程式窗格][2]

3. 若要新增新的應用程式，請選取對話方塊頂端的 [新增應用程式]**** 按鈕。

    ![[新增應用程式] 按鈕][3]

4. 在搜尋方塊中輸入應用程式的名稱，然後從結果面板中選取您的應用程式。 選取 [新增]**** 按鈕以新增應用程式。

    ![結果清單中的應用程式](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. 新增應用程式之後，移至 [屬性]**** 頁面，然後複製 [物件識別碼]。

    ![屬性頁面](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. 在另一個視窗中開啟 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) ，然後執行下列步驟：

    a. 使用您租用戶的全域管理員或共同管理員認證來登入 [Graph 總管] 網站。

    b. 您必須有足夠的權限才能建立角色。 選取 [修改權限]**** 來取得權限。

      ![[修改權限] 按鈕](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. 從清單中選取下列權限 (如果您還沒有這些權限)，然後選取 [修改權限]****。

      ![權限清單和 [修改權限] 按鈕](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > 「雲端應用程式系統管理員」和「應用程式系統管理員」角色不適用於此案例，因為我們需要「全域系統管理員」權限來進行目錄讀取和寫入。

    d. 接受此同意。 您便會再次登入系統。

    e. 將版本變更為 [搶鮮版 (Beta)]****，然後使用下列查詢從租用戶中擷取服務主體清單：

     `https://graph.microsoft.com/beta/servicePrincipals`

      如果您使用多個目錄，則請依照此模式： `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![[Graph 總管] 對話方塊，具有用於擷取服務主體的查詢](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > 我們正在進行升級 API 的程序，因此客戶可能會看到服務中有些作業出現中斷。

    f. 從所擷取的服務主體清單中，取得您需要修改的服務主體。 您也可以使用 Ctrl+F，從所有列出的服務主體中搜尋應用程式。 搜尋您從 [屬性]**** 頁面複製的物件識別碼，然後使用下列查詢來移至該服務主體：

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![用來取得所需修改服務主體的查詢](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. 從服務主體物件中解壓縮 **appRoles** 屬性。

      ![appRoles 屬性的詳細資料](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > 如果您使用自訂應用程式 (而非 Azure Marketplace 應用程式)，您會看到兩個預設角色：使用者和 msiam_access。 如果是 Marketplace 應用程式，則 msiam_access 會是唯一的預設角色。 您不需要在預設角色中進行任何變更。

    h. 為應用程式產生新角色。

      下列 JSON 是 **appRoles** 物件的範例。 請建立類似的物件，以新增應用程式所需的角色。

      ```
      {
         "appRoles": [
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "msiam_access",
              "displayName": "msiam_access",
              "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
              "isEnabled": true,
              "origin": "Application",
              "value": null
          },
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "Administrators Only",
              "displayName": "Admin",
              "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
              "isEnabled": true,
              "origin": "ServicePrincipal",
              "value": "Administrator"
          }
      ]
      }
      ```

      > [!Note]
      > 您只能在修補作業的 msiam_access 之後新增角色。 此外，您可以新增組織所需數目的角色。 Azure AD 會在 SAML 回應中以宣告值的形式傳送這些角色的值。 若要針對新角色的識別碼產生 GUID 值，請使用[這](https://www.guidgenerator.com/)一類的 Web 工具

    i. 返回 Graph 總管，並將方法從 [GET]**** 變更為 [PATCH]****。 藉由更新 **appRoles** 屬性 (例如前述範例所示屬性)，將服務主體物件修補成具有所需的角色。 選取 [執行查詢]**** 以執行修補作業。 若出現成功訊息，表示角色已建立。

      ![具有成功訊息的修補作業](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. 將服務主體修補成具有更多角色之後，您可以將使用者指派給各自的角色。 您可以移至入口網站並瀏覽至應用程式，來指派使用者。 選取 [ **使用者和群組** ] 索引標籤。此索引標籤會列出已指派給應用程式的所有使用者和群組。 您可以對新角色新增使用者。 您也可以選取現有使用者，然後選取 [編輯]**** 以變更角色。

    ![[使用者和群組] 索引標籤](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    若要將角色指派給任何使用者，請選取新角色，然後選取頁面底部的 [指派]**** 按鈕。

    ![[編輯指派] 窗格和 [選取角色] 窗格](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > 您必須在 Azure 入口網站中重新整理工作階段，才能看到新角色。

8. 更新 [屬性]**** 資料表以定義自訂的角色宣告對應。

9. 在 [使用者屬性] 對話方塊的 [使用者宣告] 區段中，執行下列步驟以設定 SAML 權杖屬性，如下表所示：

    | 屬性名稱 | 屬性值 |
    | -------------- | ----------------|
    | 角色名稱  | user.assignedroles |

    >[!NOTE]
    >如果角色宣告值為 null，則 Azure AD 不會在權杖中傳送此值，而這是根據設計的預設值。

    a. 按一下 [ **編輯** ] 圖示，以開啟 [ **使用者屬性 & 宣告** ] 對話方塊。

      ![反白顯示用來開啟 [使用者屬性 & 宣告] 對話方塊的編輯圖示的螢幕擷取畫面。](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. 在 [ **管理使用者宣告** ] 對話方塊中，按一下 [ **新增**宣告] 來新增 [SAML 權杖] 屬性。

      ![[新增屬性] 按鈕](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![[新增屬性] 窗格](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. 在 [名稱]**** 方塊中，視需要輸入屬性名稱。 此範例使用 [角色名稱]**** 作為宣告名稱。

    d. 讓 [命名空間]**** 方塊保持空白。

    e. 在 [來源屬性]  清單中，輸入該資料列所顯示的屬性值。

    f. 選取 [儲存]  。

10. 若要在識別提供者所起始的單一登入中測試應用程式，請登入[存取面板](https://myapps.microsoft.com)，並選取應用程式圖格。 在 SAML 權杖中，您應該會看到具有所指定宣告名稱之使用者的所有指派角色。

## <a name="update-an-existing-role"></a>更新現有角色

若要更新現有角色，請執行下列步驟：

1. 開啟 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)。

2. 使用您租用戶的全域管理員或共同管理員認證來登入 [Graph 總管] 網站。

3. 將版本變更為 [搶鮮版 (Beta)]****，然後使用下列查詢從租用戶中擷取服務主體清單：

    `https://graph.microsoft.com/beta/servicePrincipals`

    如果您使用多個目錄，則請依照此模式： `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![[Graph 總管] 對話方塊，具有用於擷取服務主體的查詢](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. 從所擷取的服務主體清單中，取得您需要修改的服務主體。 您也可以使用 Ctrl+F，從所有列出的服務主體中搜尋應用程式。 搜尋您從 [屬性]**** 頁面複製的物件識別碼，然後使用下列查詢來移至該服務主體：

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![用來取得所需修改服務主體的查詢](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. 從服務主體物件中解壓縮 **appRoles** 屬性。

    ![appRoles 屬性的詳細資料](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. 若要更新現有角色，請使用下列步驟。

    !["PATCH" 的要求本文，並醒目提示 "description" 和 "displayname"](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. 將方法從 **GET** 變更為 **PATCH**。

    b. 複製現有角色，然後貼到 [要求本文]**** 底下。

    c. 視需要藉由更新角色描述、角色值或角色顯示名稱，來更新角色的值。

    d. 更新所有必要角色之後，選取 [執行查詢]****。

## <a name="delete-an-existing-role"></a>刪除現有角色

若要刪除現有角色，請執行下列步驟：

1. 在另一個視窗中開啟 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 。

2. 使用您租用戶的全域管理員或共同管理員認證來登入 [Graph 總管] 網站。

3. 將版本變更為 [搶鮮版 (Beta)]****，然後使用下列查詢從租用戶中擷取服務主體清單：

    `https://graph.microsoft.com/beta/servicePrincipals`

    如果您使用多個目錄，則請依照此模式： `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![[Graph 總管] 對話方塊，具有用於擷取服務主體清單的查詢](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. 從所擷取的服務主體清單中，取得您需要修改的服務主體。 您也可以使用 Ctrl+F，從所有列出的服務主體中搜尋應用程式。 搜尋您從 [屬性]**** 頁面複製的物件識別碼，然後使用下列查詢來移至該服務主體：

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![用來取得所需修改服務主體的查詢](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. 從服務主體物件中解壓縮 **appRoles** 屬性。

    ![服務主體物件中 appRoles 屬性的詳細資料](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. 若要刪除現有角色，請使用下列步驟。

    !["PATCH" 的要求本文，且 IsEnabled 設定為 false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. 將方法從 **GET** 變更為 **PATCH**。

    b. 從應用程式複製現有的角色，然後貼到 [要求本文]**** 底下。

    c. 針對您要刪除的角色，將 **IsEnabled** 值設定為 **false**。

    d. 選取 [執行查詢]****。

    > [!NOTE]
    > 請確定您擁有 msiam_access 角色，而且識別碼與所產生角色中的相符。

7. 在停用角色之後，從 **appRoles** 區段中刪除該角色區塊。 繼續保持使用 [PATCH]**** 方法，然後選取 [執行查詢]****。

8. 執行查詢之後，便會刪除該角色。

    > [!NOTE]
    > 必須先停用角色，才可移除該角色。

## <a name="next-steps"></a>後續步驟

如需其他步驟，請參閱[應用程式文件](../saas-apps/tutorial-list.md)。

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
