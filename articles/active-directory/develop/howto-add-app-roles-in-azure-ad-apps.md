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
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706008"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>如何：在您的應用程式中新增應用程式角色，並且在權杖中接收這些角色

角色型存取控制 (RBAC) 是一個熱門的機制，可在應用程式中強制授權。 使用 RBAC 時，系統管理員會將權限授與給角色，而非授與個別使用者或群組。 系統管理員可以接著將角色指派給不同的使用者和群組，控制誰可以存取哪些內容和功能。

使用 RBAC 搭配應用程式角色和角色宣告，開發人員可以在其應用程式中安全地強制執行授權，而不需投入更多時間。

另一種方法是使用 Azure AD 群組和群組宣告，如 GitHub 上的 [WebApp->webapp-groupclaims-dotnet-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) 程式碼範例所示。 Azure AD 群組和應用程式角色都不是互斥的;您可以使用它們來提供更細微的存取控制。

## <a name="declare-roles-for-an-application"></a>宣告應用程式的角色

應用程式角色是在 [Azure 入口網站](https://portal.azure.com)中定義。  當使用者登入應用程式時，Azure AD 會針對已個別授與給使用者和自其群組成員資格授與的每個角色發出 `roles` 宣告。  透過入口網站的 UI，或以程式設計方式使用 [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview)，即可將使用者和群組指派給角色。

若要建立應用程式角色：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您要新增應用程式角色的應用程式註冊 Azure Active Directory 租使用者。
1. 搜尋並選取 [Azure Active Directory]  。
1. 在 [ **管理**] 底下，選取 [ **應用程式註冊**]，然後選取您想要在其中定義應用程式角色的應用程式。
1. 選取 **應用程式角色 |預覽**]，然後選取 [ **建立應用程式角色**]。

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Azure 入口網站中的應用程式註冊的 [應用程式角色] 窗格":::
1. 在 [ **建立應用程式角色** ] 窗格中，輸入角色的設定。 影像後面的資料表會描述每個設定及其參數。

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="應用程式註冊的應用程式角色會在 Azure 入口網站中建立內容窗格":::

    | 欄位 | 描述 | 範例 |
    |-------|-------------|---------|
    | **顯示名稱** | 顯示在管理員同意和應用程式指派體驗中的應用程式角色名稱。 此值可包含空格。  | `Survey Writer` |
    | **允許的成員類型** | 指定是否可將此應用程式角色指派給使用者、應用程式或兩者。 | `Users/Groups` |
    | **值** | 指定應用程式在權杖中應預期的角色宣告值。 此值應與應用程式程式碼中所參考的字串完全相符。 值不能包含空格。 | `Survey.Create` |
    | **說明** | 更詳細描述在系統管理員應用程式指派和同意體驗期間所顯示的應用程式角色。 | `Writers can create surveys.` |
    | **要啟用此應用程式角色嗎？** | 應用程式角色是否已啟用。 若要刪除應用程式角色，請取消選取此核取方塊，並在嘗試刪除操作之前套用變更。 | *已檢查* |

1. 選取 [套用] 以儲存變更。

> [!NOTE]
> 您新增的角色數目會計入針對應用程式資訊清單定義的限制。 如需這些限制的詳細資訊，請參閱[Azure Active Directory 應用程式資訊清單參考](reference-app-manifest.md)的[資訊清單限制](./reference-app-manifest.md#manifest-limits)一節。

## <a name="assign-users-and-groups-to-roles"></a>將使用者和群組指派給角色

當您在應用程式中新增應用程式角色之後，就可以將使用者和群組指派給角色。

1. 在 Azure 入口網站的 **Azure Active Directory** 中，選取左側導覽功能表中的 [ **企業應用程式** ]。
1. 選取 [所有應用程式]，以檢視所有應用程式的清單。

     如果您的應用程式未出現在清單中，請使用 [ **所有應用程式** ] 清單頂端的篩選準則來限制清單，或向下移動清單以找出您的應用程式。

1. 選取您想要在其中將使用者或安全性群組指派給角色的應用程式。
1. 在 [管理]**** 下選取 [使用者和群組]****。
1. 選取 [ **新增使用者** ] 以開啟 [ **新增指派** ] 窗格。
1. 選取 [新增指派] 窗格中的 [使用者和群組] 選取器。

     使用者和安全性群組的清單隨即顯示。 您可以搜尋特定的使用者或群組，也可以選取出現在清單中的多個使用者和群組。

1. 選取 [使用者和群組] 之後，請選取 [ **選取** ] 按鈕以繼續。
1. 選取 [**新增指派**] 窗格中的 [**選取角色**]。 系統會顯示您為應用程式定義的所有角色。
1. 選擇角色，然後選取 [ **選取** ] 按鈕。
1. 選取 [ **指派** ] 按鈕，完成將使用者和群組指派給應用程式的作業。
1. 確認您新增的使用者和群組出現在 [ **使用者和群組** ] 清單中。

## <a name="receive-roles-in-tokens"></a>接收權杖中的角色

當指派給各種應用程式角色的使用者登入應用程式時，他們的權杖將會在宣告中有指派的角色 `roles` 。

## <a name="web-api-application-permissions"></a>Web API 應用程式許可權

您可以定義以 **使用者/群組**、 **應用程式**或 **兩者**為目標的應用程式角色。 當您選取 [ **應用程式** ] 或 [ **兩者**] 時，應用程式角色會顯示為用戶端應用程式 **API 許可權**中的應用程式許可權。

若要在定義以 **應用程式** 為目標的角色或 **兩者**之後，選取用戶端應用程式註冊中的應用程式許可權：

1. 在 Azure 入口網站的 **Azure Active Directory** 中，選取 [ **應用程式註冊**]，然後選取用戶端應用程式的註冊。
1. 在 [管理] 之下選取 [API 權限]。
1. 選取 [**新增我的 api 的許可權**]  >  ** **。
1. 選取您要新增應用程式角色的應用程式，然後選取 [ **應用程式許可權**]。

## <a name="next-steps"></a>後續步驟

深入瞭解具有下列資源的應用程式角色：

- 程式碼範例： [使用應用程式角色 & 角色宣告加入 ASP.NET Core web 應用程式 (GitHub 的授權](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)) 
- 影片： [在您的應用程式中使用 Microsoft 身分識別平臺來執行授權 ](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15) 
- [Azure Active Directory 應用程式資訊清單](./reference-app-manifest.md)
- [Azure AD 存取權杖](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
