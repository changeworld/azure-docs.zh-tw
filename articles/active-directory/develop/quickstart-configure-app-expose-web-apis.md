---
title: 快速入門：設定應用程式以公開 Web API | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，您會了解如何設定應用程式來公開新的權限/範圍和角色，以便讓應用程式可供用戶端應用程式使用。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830286"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>快速入門：設定應用程式以公開 Web API

您可以開發 Web API，並藉由公開[權限/範圍](developer-glossary.md#scopes)和[角色](developer-glossary.md#roles)，使其可供用戶端應用程式使用。 正確設定的 web API 即可供使用，就像其他 Microsoft web API 一樣，包括 Graph API 和 Office 365 API。

在本快速入門中，您將了解如何設定應用程式來公開新的範圍，以便將應用程式提供給用戶端應用程式使用。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 完成[快速入門：向 Microsoft 身分識別平台註冊應用程式](quickstart-register-app.md)。

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

1. (選用) 若要隱藏提示您的應用程式使用者同意您已定義的範圍，可以「預先授權」用戶端應用程式存取您的 Web API。 您應該「只」預先授權信任的用戶端應用程式，因為您的使用者不會有機會拒絕同意。
    1. 在**授權的用戶端應用程式**底下，選取 [新增用戶端應用程式]
    1. 輸入您要預先授權的用戶端應用程式的**應用程式 (用戶端)識別碼**。 例如，您先前註冊的 Web 應用程式。
    1. 在 [授權範圍] 底下，選取您想要隱藏同意提示的範圍，然後選取 [新增應用程式]。

    用戶端應用程式現在是預先授權的用戶端應用程式 (PCA)，系統不會在使用者登入時顯示同意的提示。

1. 遵循相關步驟來[確認已向其他應用程式公開 Web API](#verify-the-web-api-is-exposed-to-other-applications)。

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>透過應用程式資訊清單公開新的範圍或角色

應用程式資訊清單可作為用於更新應用程式實體的機制，以定義 Azure AD 應用程式註冊的屬性。

[![使用資訊清單中的 oauth2Permissions 集合來公開新的範圍](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

若要藉由編輯應用程式資訊清單來公開新的範圍：

1. 從應用程式的 [概觀] 頁面，選取 [資訊清單] 區段。 Web 式的資訊清單編輯器隨即開啟，以供您在入口網站內**編輯**資訊清單。 或者，您也可以選取 [下載] 並在本機編輯資訊清單，然後使用 [上傳] 以將其重新套用到您的應用程式。

    下列範例說明如何藉由將下列 JSON 元素新增至 `oauth2Permissions` 集合，從而在資源/API 上公開稱為 `Employees.Read.All` 的新範圍。

    以程式設計方式或使用 [guidgen](https://www.microsoft.com/download/details.aspx?id=55984) 之類的 GUID 產生工具來產生 `id` 值。

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

1. 完成時，按一下 [儲存]。 您的 Web API 現在已設定為可供目錄中的其他應用程式使用。
1. 遵循相關步驟來[確認已向其他應用程式公開 Web API](#verify-the-web-api-is-exposed-to-other-applications)。

如需應用程式實體及其結構描述的詳細資訊，請參閱 Microsoft Graph 的[應用程式][ms-graph-application]資源類型參考文件。

如需應用程式資訊清單的詳細資訊，包括其結構描述參考，請參閱[了解 Azure AD 應用程式資訊清單](reference-app-manifest.md)。

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>確認已向其他應用程式公開 Web API

1. 返回 Azure AD 租用戶，選取 [應用程式註冊]，然後尋找並選取您想要設定的用戶端應用程式。
1. 重複進行[設定用戶端應用程式以存取 Web API](quickstart-configure-app-access-web-apis.md) 中所述的步驟。
1. 當您進行到[選取 API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis) 步驟時，選取您的資源 (Web API 應用程式註冊)。
    * 如果您使用 Azure 入口網站建立了 Web API 應用程式註冊，您的 API 資源會列在**我的 API** 索引標籤中。
    * 如果您允許 Visual Studio 在專案建立期間建立您的 Web API 應用程式註冊，您的 API 資源會列在**我的組織使用的 API** 索引標籤中。

一旦選取了 Web API 資源，您應該會看到可供用戶端權限要求使用的新範圍。

## <a name="using-the-exposed-scopes"></a>使用公開的範圍

一旦為用戶端設定了適當的 Web API 存取權限，Azure AD 便會對用戶端發出 OAuth 2.0 存取權杖。 用戶端在呼叫 Web API 時會出示存取權杖，此權杖的範圍 (`scp`) 宣告已設定為其應用程式註冊中所要求的權限。

稍後您可以視需要公開其他範圍。 請考慮您的 Web API 可能會公開多個與各種不同功能相關聯的範圍。 在執行階段，您的資源可藉由評估所收到之 OAuth 2.0 存取權杖中的範圍 (`scp`) 宣告，來控制 Web API 的存取。

在您的應用程式中，完整範圍值是 Web API 的**應用程式識別碼 URI** (資源) 和**範圍名稱**的串連。

例如，如果您 Web API 的應用程式識別碼 URI 是 `https://contoso.com/api`，而您的範圍名稱是 `Employees.Read.All`，則完整範圍是：

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>後續步驟

您現已藉由設定 Web API 的範圍來將其公開，接下來請使用可存取這些範圍的權限來設定用戶端應用程式的註冊。

> [!div class="nextstepaction"]
> [設定 Web API 存取的應用程式註冊](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
