---
title: 快速入門：註冊並公開 Web API | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，您會向 Microsoft 身分識別平台註冊 Web API 並設定其範圍，將其公開給用戶端，以取得 API 資源的權限型存取權。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperfq1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 72d66bd4c738ed60bbaefc123daae90ecc0db163
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89442123"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>快速入門：設定應用程式以公開 Web API

在本快速入門中，您會向 Microsoft 身分識別平台註冊 Web API，並藉由新增範例範圍將其公開給用戶端應用程式。 藉由註冊您的 Web API 並透過範圍公開，您可以針對存取您 API 的授權使用者和用戶端應用程式，提供其資源的權限存取權。

## <a name="prerequisites"></a>先決條件

* 包含作用中訂用帳戶的 Azure 帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 完成[快速入門：設定租用戶](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>註冊 Web API

若要為 Web API 中的資源提供有範圍的存取權，您必須先向 Microsoft 身分識別平台註冊 API。

1. 執行**註冊應用程式**一節中的步驟，位於[快速入門：使用 Microsoft 身分識別平台來註冊應用程式](quickstart-register-app.md)。
1. 略過**新增重新導向 URI**並**設定平台設定**區段。 因為沒有任何使用者以互動方式登入，所以您不需要設定 Web API 的重新導向 URI。
1. 目前請略過**新增認證**區段。 只有當您的 API 存取下游 API 時，才需要自己的認證，這是本文未涵蓋的案例。

註冊 Web API 之後，您可以新增 API 程式碼用來為 API 的取用者提供細微權限的範圍。

## <a name="add-a-scope"></a>新增範圍

用戶端應用程式中的程式碼會要求權限，以執行 Web API 所定義的作業，方法是將存取權杖連同其要求傳遞至受保護的資源 (Web API)。 只有當所收到的存取權杖包含作業所需的範圍時，您的 Web API 才會執行要求的作業。

首先，請遵循下列步驟來建立名為 `Employees.Read.All` 的範例範圍：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您有多個租用戶，請使用頂端功能表中的**目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false"::: 來選取包含您用戶端應用程式的租用戶。
1. 依序選取 [Azure Active Directory] > [應用程式註冊]，接著選取 API 的應用程式註冊。
1. 選取 [公開 API] > [增範圍]。

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="應用程式註冊在 Azure 入口網站中公開 API 窗格":::

1. 如果您尚未設定**應用程式識別碼 URI**，系統會提示您加以設定。

   應用程式識別碼 URI 會作為您將在 API 程式碼中參考之範圍的前置詞，而且必須是全域唯一的值。 您可以使用所提供的預設值 (格式為 `api://<application-client-id>`)，或指定更容易閱讀的 URI，例如 `https://contoso.com/api`。

1. 接下來，在 [新增範圍] 窗格中指定範圍的屬性。 在此逐步解說中，您可以使用範例值或指定您自己的值。

    | 欄位 | 說明 | 範例 |
    |-------|-------------|---------|
    | **範圍名稱** | 範圍名稱。 通常的範圍命名慣例為 `resource.operation.constraint`。 | `Employees.Read.All` |
    | **誰可以同意** | 此範圍是否可由使用者同意，還是必須由管理員同意。 選取 [僅限系統管理員]  以要求較高權限。 | **管理員和使用者** |
    | **管理員同意顯示名稱** | 只有管理員才會看到範圍用途的簡短說明。 | `Read-only access to Employee records` |
    | **管理員同意描述** | 只有管理員才會看到範圍所授與之權限的更詳細描述。 | `Allow the application to have read-only access to all Employee data.` |
    | **使用者同意顯示名稱** | 範圍用途的簡短描述。 只有當您將**可同意的人員**設定為**管理員和使用者**時，才會向使用者顯示。 | `Read-only access to your Employee records` |
    | **使用者同意描述** | 範圍所授與之權限的更詳細描述。 只有當您將**可同意的人員**設定為**管理員和使用者**時，才會向使用者顯示。 | `Allow the application to have read-only access to your Employee data.` |

1. 將**狀態**設定為**啟用**，然後選取 [新增範圍]。

1. (選用) 若要隱藏提示您的應用程式使用者同意您已定義的範圍，可以「預先授權」用戶端應用程式存取您的 Web API。 系統「只會」預先授權信任的用戶端應用程式，因為您的使用者不會有機會拒絕同意。
    1. 在**授權的用戶端應用程式**底下，選取 [新增用戶端應用程式]
    1. 輸入您要預先授權的用戶端應用程式的**應用程式 (用戶端)識別碼**。 例如，您先前註冊的 Web 應用程式。
    1. 在 [授權範圍] 底下，選取您想要隱藏同意提示的範圍，然後選取 [新增應用程式]。

    如果您遵循此選用步驟，用戶端應用程式現在是預先授權的用戶端應用程式 (PCA)，系統不會在使用者登入時顯示同意的提示。

## <a name="add-a-scope-requiring-admin-consent"></a>新增需要管理員同意的範圍

接下來，新增另一個名為 `Employees.Write.All` 的範例範圍，只有管理員可以同意。 需要管理員同意的範圍通常會用來提供較高權限作業的存取權，且通常會由執行為後端服務的用戶端應用程式，或不以使用者互動方式登入的精靈使用。

若要新增 `Employees.Write.All` 範例範圍，請依照[新增範圍](#add-a-scope)一節中的步驟，並在**新增範圍**窗格中指定這些值：

| 欄位                          | 範例值                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **範圍名稱**                 | `Employees.Write.All`                                              |
| **誰可以同意**            | **僅限管理員**                                                    |
| **管理員同意顯示名稱** | `Write access to Employee records`                                 |
| **管理員同意描述**  | `Allow the application to have write access to all Employee data.` |
| **使用者同意顯示名稱**  | 無 (保留空白)                                               |
| **使用者同意描述**   | 無 (保留空白)                                               |

## <a name="verify-the-exposed-scopes"></a>驗證公開的範圍

如果您成功新增前幾節中所述的兩個範例範圍，則這些範圍會出現在 Web API 應用程式註冊的 **公開 API** 窗格中，類似於此圖：

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="應用程式註冊在 Azure 入口網站中公開 API 窗格":::

如圖片中所示，範圍的完整字串是 Web API 的 **應用程式識別碼 URI** 和範圍之**範圍名稱**的串連。

例如，如果您 Web API 的應用程式識別碼 URI 是 `https://contoso.com/api`，而範圍名稱是 `Employees.Read.All`，則完整範圍是：

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>使用公開的範圍

在此系列的下一篇文章中，您可以設定用戶端應用程式的註冊以存取您的 Web API，以及遵循本文所定義的範圍。

一旦用戶端應用程式註冊授與存取您 Web API 的權限，用戶端就可以由 Microsoft 身分識別平台發出 OAuth 2.0 存取權杖。 當用戶端呼叫 Web API 時，其會呈現存取權杖，其範圍 (`scp`) 宣告已設定為您在用戶端應用程式註冊中指定的權限。

稍後您可以視需要公開其他範圍。 假設您的 Web API 可以公開多個與數個作業相關聯的範圍。 在執行階段，您的資源可藉由評估所收到之 OAuth 2.0 存取權杖中的範圍 (`scp`) 宣告，來控制 Web API 的存取。

## <a name="next-steps"></a>後續步驟

您現已藉由設定 Web API 的範圍來將其公開，接下來請使用可存取這些範圍的權限來設定用戶端應用程式的註冊。

> [!div class="nextstepaction"]
> [設定 Web API 存取的應用程式註冊](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
