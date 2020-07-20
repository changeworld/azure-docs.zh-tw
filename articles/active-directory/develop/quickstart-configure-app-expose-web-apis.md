---
title: 設定應用程式以公開 Web API - Microsoft 身分識別平台 | Azure
description: 了解如何設定應用程式來公開新的權限/範圍和角色，以便讓應用程式可供用戶端應用程式使用。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/14/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: e005ba9c5458849863bd4668ffde1e0f6fb4bf91
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76704216"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis"></a>快速入門：設定應用程式以公開 Web API

您可以開發 Web API，並藉由公開[權限/範圍](developer-glossary.md#scopes)和[角色](developer-glossary.md#roles)，使其可供用戶端應用程式使用。 正確設定的 web API 即可供使用，就像其他 Microsoft web API 一樣，包括 Graph API 和 Office 365 API。

在本快速入門中，您將了解如何設定應用程式來公開新的範圍，以便將應用程式提供給用戶端應用程式使用。

## <a name="prerequisites"></a>Prerequisites

開始之前，請先確定您已完成以下先決條件：

* 了解所支援的[權限和同意](v2-permissions-and-consent.md)，在建置其他使用者或應用程式需要使用的應用程式時，這些是務必了解的重要資訊。
* 擁有已有應用程式在其中註冊的租用戶。
  * 如果您尚未註冊應用程式，請[了解如何使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)。

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>登入 Azure 入口網站，然後選取應用程式

請先遵循下列步驟，然後才能設定應用程式：

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您的帳戶可讓您存取多個租用戶，請在右上角選取帳戶，然後將您的入口網站工作階段設定為想要的 Azure AD 租用戶。
1. 在左側導覽窗格中，選取 [Azure Active Directory]  服務，然後選取 [應用程式註冊]  。
1. 尋找並選取您要設定的應用程式。 在選取應用程式後，您會看到應用程式的 [概觀]  或主要註冊頁面。
1. 選擇您想要使用哪一個方法 (UI 還是應用程式資訊清單) 來公開新的範圍：
    * [透過 UI 公開新的範圍](#expose-a-new-scope-through-the-ui)
    * [透過應用程式資訊清單公開新的範圍或角色](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>透過 UI 公開新的範圍

[![說明如何使用 UI 公開 API](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

若要透過 UI 公開新的範圍：

1. 從應用程式的 [概觀]  頁面，選取 [公開 API]  區段。

1. 選取 [新增範圍]  。

1. 如果您還未設定 [應用程式識別碼 URI]  ，您會看到提示要求您輸入。 輸入應用程式識別碼 URI 或使用所提供的 URI，然後選取 [儲存並繼續]  。

1. 當 [新增範圍]  頁面出現時，輸入範圍的資訊：

    | 欄位 | 描述 |
    |-------|-------------|
    | **範圍名稱** | 為範圍輸入有意義的名稱。<br><br>例如： `Employees.Read.All` 。 |
    | **誰可以同意** | 選取此範圍可由使用者同意，還是必須由管理員同意。 選取 [僅限系統管理員]  以要求較高權限。 |
    | **管理員同意顯示名稱** | 為範圍輸入有意義的描述，以便讓管理員看到。<br><br>例如， `Read-only access to Employee records` |
    | **管理員同意描述** | 為範圍輸入有意義的描述，以便讓管理員看到。<br><br>例如， `Allow the application to have read-only access to all Employee data.` |

    如果使用者可以同意範圍，也請新增下列欄位的值：

    | 欄位 | 描述 |
    |-------|-------------|
    | **使用者同意顯示名稱** | 為範圍輸入有意義的名稱，以便讓使用者看到。<br><br>例如， `Read-only access to your Employee records` |
    | **使用者同意描述** | 為範圍輸入有意義的描述，以便讓使用者看到。<br><br>例如， `Allow the application to have read-only access to your Employee data.` |

1. 完成時，請設定 [狀態]  並選取 [新增範圍]  。

1. 遵循相關步驟來[確認已向其他應用程式公開 Web API](#verify-the-web-api-is-exposed-to-other-applications)。

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>透過應用程式資訊清單公開新的範圍或角色

[![使用資訊清單中的 oauth2Permissions 集合來公開新的範圍](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

若要透過應用程式資訊清單公開新的範圍：

1. 從應用程式的 [概觀]  頁面，選取 [資訊清單]  區段。 Web 式的資訊清單編輯器隨即開啟，以供您在入口網站內**編輯**資訊清單。 或者，您也可以選取 [下載]  並在本機編輯資訊清單，然後使用 [上傳]  以將其重新套用到您的應用程式。
    
    下列範例說明如何藉由將下列 JSON 元素新增至 `oauth2Permissions` 集合，從而在資源/API 上公開稱為 `Employees.Read.All` 的新範圍。

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

   > [!NOTE]
   > `id` 的值必須以程式設計的方式產生，或使用 GUID 產生工具 (例如 [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx)) 產生。 `id` 代表由 Web API 所公開之範圍的唯一識別碼。 一旦為用戶端設定了適當的 Web API 存取權限，Azure AD 便會對用戶端發出 OAuth 2.0 存取權杖。 用戶端在呼叫 Web API 時會出示存取權杖，此權杖的範圍 (scp) 宣告已設定為其應用程式註冊中所要求的權限。
   >
   > 稍後您可以視需要公開其他範圍。 請考慮您的 Web API 可能會公開多個與各種不同功能相關聯的範圍。 在執行階段，您的資源可藉由評估所收到之 OAuth 2.0 存取權杖中的範圍 (`scp`) 宣告，來控制 Web API 的存取。

1. 完成時，按一下 [儲存]  。 您的 Web API 現在已設定為可供目錄中的其他應用程式使用。
1. 遵循相關步驟來[確認已向其他應用程式公開 Web API](#verify-the-web-api-is-exposed-to-other-applications)。

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>確認已向其他應用程式公開 Web API

1. 返回 Azure AD 租用戶，選取 [應用程式註冊]  ，然後尋找並選取您想要設定的用戶端應用程式。
1. 重複進行[設定用戶端應用程式以存取 Web API](quickstart-configure-app-access-web-apis.md) 中所述的步驟。
1. 當您進行到 [選取 API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis
) 步驟時，選取您的資源。 您應該會看到可供用戶端權限要求使用的新範圍。

## <a name="more-on-the-application-manifest"></a>應用程式資訊清單的詳細資料

應用程式資訊清單可作為更新應用程式實體的一種機制，其可定義 Azure AD 應用程式身分識別設定的所有屬性。 如需應用程式實體和其結構描述的詳細資訊，請參閱[圖形 API 應用程式實體文件](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)。 該文章包含可用來指定您 API 權限之應用程式實體成員的完整參考資訊，包括：  

* appRoles 成員，此成員為 [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) 實體的集合，可用來定義 Web API 的[應用程式權限](developer-glossary.md#permissions)。
* oauth2Permissions 成員，此成員為 [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) 實體的集合，可用來定義 Web API 的[委派的權限](developer-glossary.md#permissions)。

如需應用程式資訊清單一般概念的詳細資訊，請參閱[了解 Azure Active Directory 應用程式資訊清單](reference-app-manifest.md)。

## <a name="next-steps"></a>後續步驟

了解與應用程式相關的下列其他應用程式管理快速入門：

* [使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)
* [設定用戶端應用程式以存取 Web API](quickstart-configure-app-access-web-apis.md)
* [修改應用程式所支援的帳戶](quickstart-modify-supported-accounts.md)
* [移除使用 Microsoft 身分識別平台所註冊的應用程式](quickstart-remove-app.md)

若要了解代表已註冊的應用程式和它們之間關係的兩個 Azure AD 物件，請參閱[應用程式物件和服務主體物件](app-objects-and-service-principals.md)。

若要了解使用 Azure Active Directory 開發應用程式時應使用的商標指導方針，請參閱[應用程式的商標指導方針](howto-add-branding-in-azure-ad-apps.md)。
