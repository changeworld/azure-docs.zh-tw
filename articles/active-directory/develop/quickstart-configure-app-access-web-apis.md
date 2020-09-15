---
title: 快速入門：設定應用程式以存取 Web API | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入門中，您會設定應用程式註冊代表 Microsoft 身分識別平台中的 Web API，以啟用用戶端應用程式的範圍資源存取權 (權限)。
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
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: fc2f3202ac88e3ee6c24db21dd9072a13a8deef9
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442249"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>快速入門：設定用戶端應用程式以存取 Web API

在本快速入門中，您會提供向 Microsoft 身分識別平台註冊的用戶端應用程式，並具備範圍限定的權限，可存取您自己的 Web API。 您也會提供 Microsoft Graph 的用戶端應用程式存取權。

藉由在用戶端應用程式的註冊中指定 Web API 的範圍，用戶端應用程式可以從 Microsoft 身分識別平台取得包含這些範圍的存取權杖。 接著 Web API 可以在程式碼中根據存取權杖中找到的範圍，提供其資源的權限存取權。

## <a name="prerequisites"></a>先決條件

* 包含作用中訂用帳戶的 Azure 帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 完成[快速入門：註冊應用程式](quickstart-register-app.md)
* 完成[快速入門：設定應用程式以公開 Web API](quickstart-configure-app-expose-web-apis.md)

## <a name="add-permissions-to-access-your-web-api"></a>新增用來存取 Web API 的權限

在第一個案例中，您會將用戶端應用程式存取權授與您自己的 Web API，這兩者都應該註冊為必要條件的一部分。 如果尚未同時註冊用戶端應用程式和 Web API，請完成兩個[必要條件](#prerequisites)文章中的步驟。

此圖顯示兩個應用程式註冊彼此之間的關係。 在本節中，您會將權裉新增至用戶端應用程式的註冊。

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="折線圖，顯示右側有公開範圍的 Web API，以及左側用戶端應用程式，其中已選取這些範圍作為權限" border="false":::

一旦您註冊了用戶端應用程式和 Web API，並藉由建立範圍來公開 API，您可以遵循下列步驟來設定用戶端對 API 的權限：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您有多個租用戶，請使用頂端功能表中的**目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: 來選取包含您用戶端應用程式的租用戶。
1. 依序選取 [Azure Active Directory] > [應用程式註冊]，接著選取您的用戶端應用程式 (「不是」您的 Web API)。
1. 選取 [API 權限] > [新增權限] > [我的 API]。
1. 選取您在必要條件中註冊的 Web API。

    預設會選取 [委派的權限]。 委派的權限適用於以登入使用者身分存取 Web API 的用戶端應用程式，且其存取權應該限制為您在下一個步驟中選取的權限。 為此範例選取 [委派的權限]。

    **應用程式權限**適用於需要自行存取 Web API 的服務或精靈類型應用程式，而無需使用者介入來進行登入或同意。 除非您已定義 Web API 的應用程式角色，否則會停用此選項。
1. 在**選取權限**下，展開您為 Web API 定義之範圍的資源，然後選取用戶端應用程式應該代表登入使用者的權限。

    如果您使用了先前快速入門中所指定的範例範圍名稱，應該會看到 **Employees.Read.All** 和 **Employees.Write.All**。
    選取 [Employees.Read.All] 或在完成必要條件時可能已建立的其他權限。
1. 選取 [新增權限] 來完成程序。

將權限新增至您的 API 後，您應該會在**設定的權限**之下看到選取的權限。 下圖顯示範例 Employees.Read.All 委派的權限已新增至用戶端應用程式的註冊。

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="Azure 入口網站中的設定的權限窗格，其中顯示新增的權限":::

您也可能會注意到 Microsoft Graph API 的 User.Read 權限。 當您在 Azure 入口網站中註冊應用程式時，系統會自動新增此權限。

## <a name="add-permissions-to-access-microsoft-graph"></a>新增用來存取 Microsoft Graph 的權限

除了會代表登入的使用者存取您自己的 Web API，您的應用程式可能也需要存取或修改儲存在 Microsoft Graph 中的使用者 (或其他) 資料。 或者，您可能會需要以本身身分存取 Microsoft Graph 的服務或精靈應用程式，並在不需要任何使用者互動的情況下執行作業。

### <a name="delegated-permission-to-microsoft-graph"></a>Microsoft Graph 的委派權限

將委派的權限設定為 Microsoft Graph，讓您的用戶端應用程式代表登入的使用者執行作業，例如讀取其電子郵件或修改其設定檔。 根據預設，用戶端應用程式的使用者登入時，系統會詢問用戶端應用程式的使用者以同意您為其設定的委派權限。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您有多個租用戶，請使用頂端功能表中的**目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: 來選取包含您用戶端應用程式的租用戶。
1. 依序選取 [Azure Active Directory] > [應用程式註冊]，接著選取您的用戶端應用程式。
1. 選取 [API 權限] > [新增權限] > [Microsoft Graph]
1. 選取 [委派的權限]。 Microsoft Graph 會公開許多權限，其中最常使用的會顯示在清單頂端。
1. 在**選取權限**底下，選取下列權限：

    | 權限       | 說明                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | 檢視使用者的電子郵件地址                           |
    | `offline_access` | 維護您授與的資料存取權 |
    | `openid`         | 將使用者登入                                       |
    | `profile`        | 檢視所有使用者的基本個人資料                           |
1. 選取 [新增權限] 來完成程序。

每當設定權限時，系統會在您的應用程式使用者登入時要求他們同意，以允許您的應用程式代表他們存取資源 API。

身為管理員，您也可以代表「所有」使用者授與同意，如此系統就不會提示使用者執行此動作。 本文章的[詳細說明 API 權限和管理員同意](#more-on-api-permissions-and-admin-consent)一節會討論管理員同意。

### <a name="application-permission-to-microsoft-graph"></a>Microsoft Graph 的應用程式權限

設定應用程式的應用程式權限，以在不需要使用者互動或同意的情況下，自行進行驗證。 應用程式權限通常是由背景服務或以「無周邊」方式存取 API 的精靈，以及存取另一個 (下游) API 的 Web API 所使用。

在下列步驟中，您會將權限授與 Microsoft Graph 的 Files.Read.All 權限作為範例。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 如果您有多個租用戶，請使用頂端功能表中的**目錄 + 訂用帳戶** 篩選條件 :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: 來選取包含您用戶端應用程式的租用戶。
1. 依序選取 [Azure Active Directory] > [應用程式註冊]，接著選取您的用戶端應用程式。
1. 選取 [API 權限] > [新增權限] > [Microsoft Graph] > [應用程式權限]。
1. Microsoft Graph 所公開的所有權限都會顯示在**選取權限**之下。
1. 選取您想要授與應用程式的權限。 例如，您可能有一個可掃描組織中檔案的精靈應用程式，會針對特定檔案類型或名稱發出警示。

    在**選取權限**底下，展開**檔案**，然後選取 Files.Read.All 權限。
1. 選取 [新增權限]。

某些權限 (例如 Microsoft Graph 的 Files.Read.All 權限) 需要管理員同意。 若要授與管理員同意，請選取 [授與管理員同意] 按鈕，稍後我們會在 [管理員同意按鈕](#admin-consent-button)一節中討論。

### <a name="configure-client-credentials"></a>設定用戶端認證

使用應用程式權限的應用程式會使用自己的認證以自己的身分進行驗證，而不需要任何使用者互動。 您必須先設定用戶端應用程式的認證，您的應用程式 (或 API) 才能使用應用程式權限來存取 Microsoft Graph、您自己的 Web API 或任何其他 API。

如需有關設定應用程式認證的詳細資訊，請參閱[新增認證](quickstart-register-app.md#add-credentials)一節，位於[快速入門：向 Microsoft 身分識別平台註冊應用程式](quickstart-register-app.md)。

## <a name="more-on-api-permissions-and-admin-consent"></a>深入了解 API 權限和管理員同意

應用程式註冊的**API 權限**窗格包含[設定的權限](#configured-permissions)資料表，也可能包含[授與其他權限](#other-permissions-granted)的資料表 。 下列各節將說明資料表和[管理員同意按鈕](#admin-consent-button)。

### <a name="configured-permissions"></a>已設定權限

**API 權限**窗格上的**設定的權限**資料表會顯示您的應用程式針對基本作業所需的權限清單 - 「需要的資源存取」 (RRA) 清單。 使用者或其管理員必須先同意這些權限，才能使用您的應用程式。 您可在稍後於執行階段要求其他選用的權限 (使用動態同意)。

這是使用者同意您應用程式所需的最低權限清單。 您可能會有更多權限，但這些是一定必要的。 為確保安全性，並協助使用者和管理員更熟悉您的應用程式，請不要詢問任何您不需要的資訊。

您可以使用上述步驟或從[授與的其他權限](#other-permissions-granted)，來新增或移除此資料表中所顯示的權限 (會在下一節中說明)。 身為管理員，您可以授與管理員同意資料表中所顯示的完整 API 權限集合，並撤銷個別權限的同意。

### <a name="other-permissions-granted"></a>已授與的其他權限

您也可能會在 **API 權限**窗格上，看到名為**針對 {your tenant} 授與的其他權限**資料表。 **授與 {your tenant} 的其他權限**資料表顯示已授與租用戶，但尚未在應用程式物件上明確設定的權限。 這些是以動態方式要求和同意的權限。 只有在至少有一個適用的權限時，才會顯示此區段。

您可以將此資料表中所顯示的一組完整的 API 權限或個別權限新增至**已設定權限**資料表。 身為管理員，您也可以在本節中撤銷 API 或個別權限的管理員同意。

### <a name="admin-consent-button"></a>系統管理員同意按鈕

**授與 {your tenant} 管理員同意**按鈕可讓系統管理員對於應用程式上設定的權限授與系統管理員同意。 選取該按鈕時，會顯示一個對話方塊要求您確認同意動作。

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="在 Azure 入口網站的設定的權限窗格中反白顯示授與管理員同意按鈕":::

授與同意之後，需要管理員同意的權限就會顯示為同意授權：

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="在 Azure 入口網站中設定權限資料表，以顯示授與 Files.Read.All 權限的管理員同意":::

如果您不是系統管理員，或未針對應用程式設定權限，則會「停用」**授與管理員同意**按鈕。 如果您有已授與但未設定的權限，系統管理員同意按鈕會提示您處理這些權限。 您可以將其新增至已設定權限，或將其移除。

## <a name="next-steps"></a>後續步驟

前進到系列中的下一個快速入門，以了解如何設定哪些帳戶類型可以存取您的應用程式。 例如，您可以限制只有組織中的使用者 (單一租用戶) 能夠存取，或是允許其他 Azure AD 租用戶 (多租用戶) 中的使用者和具有個人 Microsoft 帳戶 (MSA) 的使用者進行存取。

> [!div class="nextstepaction"]
> [修改應用程式所支援的帳戶](quickstart-modify-supported-accounts.md)