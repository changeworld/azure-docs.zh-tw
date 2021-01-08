---
title: 新增應用程式角色並自權杖中取得 | Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何將應用程式角色新增至 Azure Active Directory 中註冊的應用程式、將使用者和群組指派給這些角色，並在權杖中的「角色」宣告中接收這些角色。
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: b2ac90334ade52d68c775d9db5a84545774f3844
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013697"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>如何：將應用程式角色新增至您的應用程式，並在權杖中接收這些角色

角色型存取控制 (RBAC) 是一個熱門的機制，可在應用程式中強制授權。 使用 RBAC 時，系統管理員會將權限授與給角色，而非授與個別使用者或群組。 系統管理員可以接著將角色指派給不同的使用者和群組，控制誰可以存取哪些內容和功能。

使用 RBAC 搭配應用程式角色和角色宣告，開發人員可以更輕鬆地在其應用程式中安全地強制執行授權。

另一種方法是使用 Azure AD 群組和群組宣告，如 GitHub 上的 [active directory-aspnetcore-webapp-openidconnect-v2](https://aka.ms/groupssample) 程式碼範例所示。 Azure AD 群組和應用程式角色都不是互斥的;可以用來串聯，以提供更細微的存取控制。

## <a name="declare-roles-for-an-application"></a>宣告應用程式的角色

您可以使用 [Azure 入口網站](https://portal.azure.com)來定義應用程式角色。 應用程式角色通常定義于代表服務、應用程式或 API 的應用程式註冊。 當使用者登入應用程式時，Azure AD `roles` 會針對每個角色發出宣告，使用者或服務主體已個別授與使用者和群組成員資格。 這可以用來執行以宣告為基礎的授權。 應用程式角色可以指派給 [使用者或使用者群組](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app)。 您也可以將應用程式角色指派給另一個應用程式的服務主體，或指派給 [受控識別的服務主體](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md)。

> [!IMPORTANT]
> 目前，如果您將服務主體新增至群組，然後將應用程式角色指派給該群組，Azure AD 不會將宣告新增 `roles` 至它所發出的權杖。

使用 Azure 入口網站來宣告應用程式角色的方法有兩種：

* [應用程式角色 UI](#app-roles-ui--preview) |預覽
* [應用程式資訊清單編輯器](#app-manifest-editor)

您新增的角色數目會計入 Azure Active Directory 所強制執行的應用程式資訊清單限制。 如需這些限制的詳細資訊，請參閱[Azure Active Directory 應用程式資訊清單參考](reference-app-manifest.md)的[資訊清單限制](./reference-app-manifest.md#manifest-limits)一節。

### <a name="app-roles-ui--preview"></a>應用程式角色 UI |預覽

> [!IMPORTANT]
> 應用程式角色入口網站 UI 功能 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

若要使用 Azure 入口網站的使用者介面來建立應用程式角色：

1. 登入<a href="https://portal.azure.com/" target="_blank">Azure 入口網站 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。
1. 在頂端功能表中選取 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您要新增應用程式角色的應用程式註冊 Azure Active Directory 租使用者。
1. 搜尋並選取 [Azure Active Directory]  。
1. 在 [ **管理**] 底下，選取 [ **應用程式註冊**]，然後選取您想要在其中定義應用程式角色的應用程式。
1. 選取 **應用程式角色 |預覽**]，然後選取 [ **建立應用程式角色**]。

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Azure 入口網站中的應用程式註冊的 [應用程式角色] 窗格":::
1. 在 [ **建立應用程式角色** ] 窗格中，輸入角色的設定。 影像後面的資料表會描述每個設定及其參數。

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="應用程式註冊的應用程式角色會在 Azure 入口網站中建立內容窗格":::

    | 欄位 | 描述 | 範例 |
    |-------|-------------|---------|
    | **顯示名稱** | 顯示在管理員同意和應用程式指派體驗中的應用程式角色名稱。 此值可包含空格。 | `Survey Writer` |
    | **允許的成員類型** | 指定是否可將此應用程式角色指派給使用者、應用程式或兩者。<br/><br/>如果有 `applications` ，應用程式角色會顯示為應用程式註冊的 [ **管理** ] 區段中的 [應用程式許可權] > **API 許可權 > 新增 > 我的 Api 的許可權 > 選擇 API > 應用程式許可權**。 | `Users/Groups` |
    | **ReplTest1** | 指定應用程式在權杖中應預期的角色宣告值。 此值應與應用程式程式碼中所參考的字串完全相符。 值不能包含空格。 | `Survey.Create` |
    | **說明** | 更詳細描述在系統管理員應用程式指派和同意體驗期間所顯示的應用程式角色。 | `Writers can create surveys.` |
    | **要啟用此應用程式角色嗎？** | 指定是否啟用應用程式角色。 若要刪除應用程式角色，請取消選取此核取方塊，並在嘗試刪除操作之前套用變更。 | *已核取* |

1. 選取 [套用] 以儲存變更。

### <a name="app-manifest-editor"></a>應用程式資訊清單編輯器

若要直接編輯資訊清單來新增角色：

1. 登入<a href="https://portal.azure.com/" target="_blank">Azure 入口網站 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。
1. 在頂端功能表中選取 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您要新增應用程式角色的應用程式註冊 Azure Active Directory 租使用者。
1. 搜尋並選取 [Azure Active Directory]  。
1. 在 [ **管理**] 底下，選取 [ **應用程式註冊**]，然後選取您想要在其中定義應用程式角色的應用程式。
1. 同樣地，在 [ **管理**] 下選取 [ **資訊清單**]。
1. 尋找 `appRoles` 設定並新增應用程式角色，以編輯應用程式資訊清單。 您可以定義以 `users` 、或（或）為目標的應用程式角色 `applications` 。 下列 JSON 程式碼片段顯示這兩者的範例。
1. 儲存資訊清單。

資訊清單中的每個應用程式角色定義都必須有其值的唯一 GUID `id` 。

每個應用程式角色定義的 `value` 屬性都應完全符合應用程式中程式碼所使用的字串。 屬性 `value` 不包含空格。 如果包含空格，您就會在儲存資訊清單時收到錯誤。

#### <a name="example-user-app-role"></a>範例：使用者應用程式角色

此範例會定義名為的應用程式角色 `Writer` ，您可以將其指派給 `User` ：

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>範例：應用程式應用程式角色

如果有 `applications` ，應用程式角色會顯示為應用程式註冊的 [ **管理** ] 區段中的 [應用程式許可權] > **API 許可權 > 新增 > 我的 Api 的許可權 > 選擇 API > 應用程式許可權**。

此範例顯示以下列為目標的應用程式角色 `Application` ：

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>將使用者和群組指派給角色

當您在應用程式中新增應用程式角色之後，就可以將使用者和群組指派給角色。 透過入口網站的 UI，或以程式設計方式使用 [Microsoft Graph](/graph/api/user-post-approleassignments)，即可將使用者和群組指派給角色。 當指派給各種應用程式角色的使用者登入應用程式時，他們的權杖將會在宣告中有指派的角色 `roles` 。

若要使用 Azure 入口網站將使用者和群組指派給角色：

1. 登入<a href="https://portal.azure.com/" target="_blank">Azure 入口網站 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。
1. 在 **Azure Active Directory** 中，選取左側導覽功能表中的 [ **企業應用程式** ]。
1. 選取 [所有應用程式]，以檢視所有應用程式的清單。 如果您的應用程式未出現在清單中，請使用 [ **所有應用程式** ] 清單頂端的篩選準則來限制清單，或向下移動清單以找出您的應用程式。
1. 選取您想要在其中將使用者或安全性群組指派給角色的應用程式。
1. 在 [管理] 下選取 [使用者和群組]。
1. 選取 [ **新增使用者** ] 以開啟 [ **新增指派** ] 窗格。
1. 選取 [新增指派] 窗格中的 [使用者和群組] 選取器。 使用者和安全性群組的清單隨即顯示。 您可以搜尋特定的使用者或群組，也可以選取出現在清單中的多個使用者和群組。
1. 選取 [使用者和群組] 之後，請選取 [ **選取** ] 按鈕以繼續。
1. 選取 [**新增指派**] 窗格中的 [**選取角色**]。 系統會顯示您為應用程式定義的所有角色。
1. 選擇角色，然後選取 [ **選取** ] 按鈕。
1. 選取 [ **指派** ] 按鈕，完成將使用者和群組指派給應用程式的作業。

確認您新增的使用者和群組出現在 [ **使用者和群組** ] 清單中。

## <a name="assign-app-roles-to-applications"></a>將應用程式角色指派給應用程式

當您在應用程式中新增應用程式角色之後，您可以使用 Azure 入口網站，或使用 [Microsoft Graph](/graph/api/user-post-approleassignments)以程式設計方式將應用程式角色指派給用戶端應用程式。

當您將應用程式角色指派給應用程式時，您會建立 *應用程式許可權*。 應用程式許可權通常會由 daemon 應用程式或後端服務所使用，而這些服務需要自行驗證及進行授權的 API 呼叫，而不需要使用者互動。

使用 Azure 入口網站將應用程式角色指派給應用程式：

1. 登入<a href="https://portal.azure.com/" target="_blank">Azure 入口網站 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>。
1. 在 **Azure Active Directory** 中，選取左側導覽功能表中的 [ **應用程式註冊** ]。
1. 選取 [所有應用程式]，以檢視所有應用程式的清單。 如果您的應用程式未出現在清單中，請使用 [ **所有應用程式** ] 清單頂端的篩選準則來限制清單，或向下移動清單以找出您的應用程式。
1. 選取您要指派應用程式角色的應用程式。
1. 選取 [API 權限]  >  [新增權限]。
1. 選取 [ **我的 api** ] 索引標籤，然後選取您已為其定義應用程式角色的應用程式。
1. 選取 [應用程式權限]。
1. 選取您要指派)  (的角色。
1. 選取 [ **新增許可權** ] 按鈕，即可完成新增角色 (s) 。

新增的角色應該會出現在您的應用程式註冊的 [ **API 許可權** ] 窗格中。

#### <a name="grant-admin-consent"></a>授與管理員同意

因為這些是 *應用程式許可權*，而不是委派許可權，所以系統管理員必須授與同意，才能使用指派給應用程式的應用程式角色。

1. 在 [應用程式註冊的 **API 許可權**] 窗格中，選取 **[ \<tenant name\> 授與管理員同意**]。
1. 當系統提示您為要求的許可權授與同意時，請選取 **[是]** 。

[**狀態**] 資料行應該會反映已 **\<tenant name\> 授** 與的同意。

## <a name="use-app-roles-in-your-web-api"></a>在您的 web API 中使用應用程式角色

定義應用程式角色並將其指派給使用者、群組或應用程式之後，下一個步驟是將程式碼新增至 web API，以在呼叫 API 時檢查這些角色。 也就是說，當用戶端應用程式要求您已決定需要授權的 API 作業時，您的 API 程式碼必須驗證範圍是否在用戶端應用程式呼叫中提供的存取權杖中。

若要瞭解如何將授權新增至您的 web API，請參閱 [受保護的 WEB api：驗證範圍和應用程式角色](scenario-protected-web-api-verification-scope-app-roles.md)。

## <a name="app-roles-vs-groups"></a>應用程式角色與群組

雖然您可以使用應用程式角色或群組來進行授權，但它們之間的主要差異可能會影響您決定要用於案例的專案。

| 應用程式角色                                                                          | 群組                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| 它們是應用程式特有的，而且是在應用程式註冊中定義。 它們會隨著應用程式移動。 | 它們並不是應用程式特有的，而是 Azure AD 的租使用者。 |
| 當應用程式的應用程式註冊移除時，會移除應用程式角色。                      | 即使已移除應用程式，群組仍會保持不變。            |
| 在宣告中提供 `roles` 。                                                     | 在宣告中提供 `groups` 。                                 |

開發人員可以使用應用程式角色來控制使用者是否可以登入應用程式，或應用程式是否可以取得 web API 的存取權杖。 若要將此安全性控制延伸至群組，開發人員和系統管理員也可以將安全性群組指派給應用程式角色。

當開發人員想要在應用程式本身中描述並控制授權的參數時，開發人員偏好使用應用程式角色。 例如，使用群組進行授權的應用程式將在下一個租使用者中中斷，因為群組識別碼和名稱可能會不同。 使用應用程式角色的應用程式保持安全。 事實上，將群組指派給應用程式角色很受 SaaS 應用程式的使用，原因也相同。

## <a name="next-steps"></a>後續步驟

深入瞭解具有下列資源的應用程式角色。

* GitHub 上的程式碼範例
  * [使用群組和群組宣告將授權新增至 ASP.NET Core web 應用程式](https://aka.ms/groupssample)
  * [ (SPA) 呼叫 .NET Core web API，並使用應用程式角色和安全性群組的單一頁面應用程式](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md)
* 參考文件
  * [Azure AD 應用程式資訊清單](./reference-app-manifest.md)
  * [Azure AD 存取權杖](access-tokens.md)
  * [Azure AD 識別碼權杖](id-tokens.md)
  * [為您的應用程式提供選擇性宣告](active-directory-optional-claims.md)
* 影片： [在您的應用程式中使用 Microsoft 身分識別平臺來執行授權](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15) 
