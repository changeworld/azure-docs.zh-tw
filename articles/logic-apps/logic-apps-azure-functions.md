---
title: 從 Azure Logic Apps 新增和呼叫 Azure Functions
description: 在 Azure Logic Apps 從自動化工作和工作流程呼叫並執行 Azure Functions 中的自訂程式碼
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 6240b0813132f4a14dbe94b870774ebe7a0663aa
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714571"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>從 Azure Logic Apps 呼叫 Azure 函式

當您想要執行程式碼，以在邏輯應用程式中執行特定的作業時，您可以使用 [Azure Functions](../azure-functions/functions-overview.md) 建立自己的函式。 此服務可協助您建立 Node.js、C# 和 F# 和函式，而不需要組建完整的應用程式或基礎結構來執行程式碼。 您也可以[從 Azure 函式內呼叫邏輯應用程式](#call-logic-app)。 Azure Functions 可提供在雲端進行的無伺服器運算，適合用來執行以下舉例的各種工作：

* 使用 Node.js 或 C# 中的函式來擴充邏輯應用程式的行為。
* 在邏輯應用程式工作流程中執行計算。
* 在邏輯應用程式中套用進階格式設定或計算欄位。

若要在不建立 Azure 函式的情況下執行程式碼片段，請了解如何[新增和執行內嵌程式碼](../logic-apps/logic-apps-add-run-inline-code.md)。

> [!NOTE]
> Logic Apps 和 Azure Functions 之間的整合目前不支援啟用「位置」。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* Azure 函數應用程式 (Azure 函式的容器) 及您的 Azure 函式。 如果您沒有函式應用程式，[請先建立該函式應用程式](../azure-functions/functions-create-first-azure-function.md)。 然後，您可以使用 Azure 入口網站在邏輯應用程式外建立函式，或在邏輯應用程式設計工具中[從邏輯應用程式內](#create-function-designer)建立函式。

* 使用邏輯應用程式時，無論是現有或新的函數應用程式和函式，也都要符合相同的需求：

  * 您的函數應用程式和邏輯應用程式必須使用相同的 Azure 訂用帳戶。

  * 新的函數應用程式必須使用 .NET 或 JavaScript 作為執行階段堆疊。 當您將新的函式新增至現有的函數應用程式時，您可以選取 C# 或 JavaScript。

  * 您的函式會使用 **HTTP 觸發程序**範本。

    HTTP 觸發程序範本可以接受內容中有來自邏輯應用程式的 `application/json` 類型。 當您將 Azure 函式新增至邏輯應用程式時，邏輯應用程式設計工具會顯示 Azure 訂用帳戶內從這個範本建立的自訂函式。

  * 除非您已定義 [OpenAPI 定義](../azure-functions/functions-openapi-definition.md) (先前稱為 [Swagger 檔案](https://swagger.io/))，否則您的函式不會使用自訂路由。

  * 如果函式有 OpenAPI 定義，當您處理函式參數時，Logic Apps 設計工具可讓您享受更豐富的體驗。 邏輯應用程式若要尋找和存取具有 OpenAPI 定義的函式，必須先[遵循下列步驟來設定函式應用程式](#function-swagger)。

* 您要在其中新增函式的邏輯應用程式，並包括作為邏輯應用程式中第一個步驟的[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)

  邏輯應用程式必須以觸發程序開頭，您才能新增動作來執行函式。 如果您不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps](../logic-apps/logic-apps-overview.md) 和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>尋找具有 OpenAPI 描述的函式

當您在 Logic Apps 設計工具中處理函式參數時，為了享有更豐富的體驗，請為您的函式[產生 OpenAPI 定義](../azure-functions/functions-openapi-definition.md) (先前稱為 [Swagger 檔案](https://swagger.io/))。 若要設定函式應用程式，讓邏輯應用程式可以尋找和使用具有 Swagger 描述的函式，請遵循下列步驟：

1. 請確定函數應用程式正在執行。

1. 在函數應用程式中，請遵循下列步驟來設定[跨原始資源共用 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)，以允許所有來源：

   1. 從 [函數應用程式] 清單中，選取您的函數應用程式。 在右窗格中，選取 [平台功能] > [CORS]。

      ![選取您的函式應用程式 > [平台功能] > [CORS]](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. 在 [CORS] 下，加上星號 ( **`*`** ) 萬用字元，但移除清單中的其他所有來源，然後選取 [儲存]。

      ![將 "CORS* 設為萬用字元 "*"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>存取 HTTP 要求內的屬性值

Webhook 函式可以透過輸入接受 HTTP 要求，並將這些要求傳遞給其他函式。 例如，雖然 Logic Apps 有[會轉換日期時間值的函式](../logic-apps/workflow-definition-language-functions-reference.md)，但這個基本範例 JavaScript 函式會說明如何存取傳遞給函式的要求物件內所含的屬性，並對該屬性值執行作業。 為了存取物件內的屬性，這個範例使用[點 (.) 運算子](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)：

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

以下是此函式內部的運作情形：

1. 函式會建立 `data` 變數，並將 `request` 物件內的 `body` 物件指派給該變數。 此函式會使用點 (.) 運算子來參考 `request` 物件內的 `body` 物件：

   ```javascript
   var data = request.body;
   ```

1. 此函式現在可以透過 `data` 變數存取 `date` 屬性，並藉由呼叫 `ToDateString()` 函式將該屬性值從 DateTime 類型轉換為 DateString 類型。 此函式也會透過函式回應中的 `body` 屬性傳回結果：

   ```javascript
   body: data.date.ToDateString();
   ```

您已建立 Azure 函式，接下來請遵循如何[將函式新增至邏輯應用程式](#add-function-logic-app)的步驟。

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>在邏輯應用程式內部建立函式

您可以在邏輯應用程式設計工具中使用內建的 Azure Functions 動作，直接從邏輯應用程式的工作流程建立 Azure 函式，但此方法僅適用於以 JavaScript 撰寫的 Azure 函式。 若為其他語言，您可以透過 Azure 入口網站中的 Azure Functions 體驗來建立 Azure 函式。 如需詳細資訊，請參閱[在 Azure 入口網站中建立您的第一個函式](../azure-functions/functions-create-first-azure-function.md)。

不過，您必須已有 Azure 函數應用程式 (函式的容器)，才能建立任何 Azure 函數。 如果您沒有函式應用程式，請先建立該函式應用程式。 請參閱[在 Azure 入口網站中建立您的第一個函式](../azure-functions/functions-create-first-azure-function.md)。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 若要建立並新增函式，請遵循適用於您案例的步驟：

   * 在邏輯應用程式工作流程的最後一個步驟中，選取 [新增步驟]。

   * 在邏輯應用程式工作流程的現有步驟之間，將滑鼠移到箭號上，選取加號 (+)，然後選取 [新增動作]。

1. 在搜尋方塊中，輸入「azure functions」作為篩選條件。 從動作清單中，選取 [選擇 Azure 函式] 動作，例如：

   ![尋找 "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 從函式應用程式清單中，選取您的函式應用程式。 在動作清單開啟之後，選取此動作：**建立新函數**

   ![選取函式應用程式](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. 在函式定義編輯器中，定義您的函式：

   1. 在 [函式名稱] 方塊中，為您的函式提供名稱。

   1. 在 [程式碼] 方塊中，將程式碼新增至函式範本，包括當函式執行完成後要傳回給邏輯應用程式的回應和承載。 當您完成時，選取 [建立]。

   例如：

   ![定義函式](./media/logic-apps-azure-functions/add-code-function-definition.png)

   在範本的程式碼中，`context` 物件代表邏輯應用程式透過**要求本文** (位於後面的步驟中) 欄位傳送的訊息。 若要從您的函式內存取 `context` 物件的屬性，請使用此語法：

   `context.body.<property-name>`

   例如，若要參考 `context` 物件內的 `content` 屬性，請使用此語法：

   `context.body.content`

   範本程式碼也包含 `input` 變數，其中儲存著來自 `data` 參數的值，因此函式可以根據該值執行作業。 在 JavaScript 函數中，`data` 變數也是 `context.body` 的捷徑。

   > [!NOTE]
   > 這裡的 `body` 屬性適用於 `context` 物件，與動作輸出中可能也會傳遞給函式的**本文**權杖不同。

1. 在 [要求本文] 方塊中，提供函式的輸入 (必須以 JavaScript 物件標記法 (JSON) 物件進行格式化)。

   此輸入是邏輯應用程式傳送給函式的「內容物件」或訊息。 當您在 [要求本文] 欄位內按一下時，動態內容清單會隨即出現，讓您可以選取前面步驟中的輸出權杖。 此範例指定內容承載包含名為 `content` 的屬性，而此屬性具有電子郵件觸發程序中的**寄件者**權杖值。

   ![「要求本文」範例 - 內容物件承載](./media/logic-apps-azure-functions/function-request-body-example.png)

   在此處，內容物件不會轉換為字串，所以物件內容會直接新增至 JSON 承載。 不過，如果內容物件不是傳遞字串、JSON 物件或 JSON 陣列的 JSON 權杖，您就會收到錯誤。 因此，如果此範例改為使用**接收時間**權仗，則您可以將內容物件加上雙引號，以轉換為字串。

   ![將物件轉換為字串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 若要指定其他詳細資料，例如要使用的方法、要求標頭、查詢參數或驗證，請開啟 [新增參數] 清單，然後選取您想要的選項。 在驗證方面，選項會隨著您選取的函式而有所不同。 請參閱[啟用 Azure 函式的驗證](#enable-authentication-functions)。

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>在邏輯應用程式中新增現有函式

若要從邏輯應用程式呼叫現有 Azure 函式，您可以在邏輯應用程式設計工具中新增 Azure 函式，例如任何其他動作。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在您要新增函式的步驟下方，選取 [新增步驟]。

1. 在 [選擇動作] 下的搜尋方塊中，輸入 "azure functions" 作為篩選條件。 從動作清單中，選取 [選擇 Azure 函式] 動作。

   ![尋找 "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 從函式應用程式清單中，選取您的函式應用程式。 函式清單出現後，選取您的函式。

   ![選取函式應用程式和 Azure 函式](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   如果函式有 API 定義 (Swagger 描述)，而且[設定為可讓邏輯應用程式尋找和存取這些函式](#function-swagger)，您可以選取 [Swagger 動作]。

   ![選取函數應用程式、[Swagger 動作] 和 Azure 函式](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. 在 [要求本文] 方塊中，提供函式的輸入 (必須以 JavaScript 物件標記法 (JSON) 物件進行格式化)。

   此輸入是邏輯應用程式傳送給函式的「內容物件」或訊息。 當您按一下 [要求本文] 欄位時，動態內容清單隨即出現，讓您選取先前步驟中的輸出權杖。 此範例指定內容承載包含名為 `content` 的屬性，而此屬性具有電子郵件觸發程序中的**寄件者**權杖值。

   ![「要求本文」範例 - 內容物件承載](./media/logic-apps-azure-functions/function-request-body-example.png)

   在此處，內容物件不會轉換為字串，所以物件內容會直接新增至 JSON 承載。 不過，如果內容物件不是傳遞字串、JSON 物件或 JSON 陣列的 JSON 權杖，您就會收到錯誤。 因此，如果此範例改為使用**接收時間**權仗，則您可以在內容物件上加上雙引號，將內容物件轉換為字串：

   ![將物件轉換為字串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 若要指定其他詳細資料，例如要使用的方法、要求標頭、查詢參數或驗證，請開啟 [新增參數] 清單，然後選取您想要的選項。 在驗證方面，選項會隨著您選取的函式而有所不同。 請參閱[在 Azure 函式中啟用驗證](#enable-authentication-functions)。

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>從 Azure 函式呼叫邏輯應用程式

若要從 Azure 函式內觸發邏輯應用程式，該邏輯應用程式必須以能提供可呼叫端點的觸發程序作為開頭。 例如，您可以使用 **HTTP**、**要求**、**Azure 佇列**或**事件方格**觸發程序來起始邏輯應用程式。 在函式內部，將 HTTP POST 要求傳送給該觸發程序的 URL，並包含您想要讓該邏輯應用程式處理的承載。 如需詳細資訊，請參閱[呼叫、觸發或巢狀邏輯應用程式](../logic-apps/logic-apps-http-endpoint.md)。

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>啟用 Azure 函式的驗證

若要輕鬆驗證存取 Azure Active Directory (Azure AD) 所保護的其他資源，而不需要登入並提供認證或秘密，您的邏輯應用程式可以使用[受控識別](../active-directory/managed-identities-azure-resources/overview.md) (先前稱為受控服務識別，縮寫為 MSI)。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。 深入了解[支援使用受控識別進行 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

如果您將邏輯應用程式設定為使用系統指派的身分識別，或使用者指派的手動建立身分識別，則邏輯應用程式中的 Azure 函式也可以使用該相同的身分識別進行驗證。 如需邏輯應用程式中的 Azure 函式驗證支援的詳細資訊，請參閱[將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

若要對您的函式設定和使用受控識別，請遵循下列步驟：

1. 在邏輯應用程式上啟用受控識別，並設定該身分識別對目標資源的存取權。 請參閱 [在 Azure Logic Apps 中使用受控識別來驗證對 Azure 資源的存取](../logic-apps/create-managed-service-identity.md)。

1. 遵循下列步驟，在 Azure 函式和函數應用程式中啟用驗證：

   * [在函式中設定匿名驗證](#set-authentication-function-app)
   * [在函數應用程式中設定 Azure AD 驗證](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>在函式中設定匿名驗證

若要在 Azure 函式中使用邏輯應用程式的受控識別，您必須將函式的驗證層級設為匿名。 否則，邏輯應用程式會擲回 "BadRequest" 錯誤。

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取您的函數應用程式。 這些步驟使用 "FabrikamFunctionApp" 作為範例函數應用程式。

1. 在 [函數應用程式] 窗格中，選取 [平台功能]。 在 [開發工具] 下，選取 [進階工具 (Kudu)]。

   ![開啟 Kudu 的進階工具](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. 在 Kudu 網站的標題列上，從 [偵錯主控台] 功能表中選取 [CMD]。

   ![從 [偵錯主控台] 功能表中，選取 [CMD] 選項](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. 下一個頁面出現之後，從資料夾清單中，選取 [site] > [wwwroot] > *your-function*。 這些步驟使用 "FabrikamAzureFunction" 作為範例函式。

   ![選取 [site] > [wwwroot] > 您的函式](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. 開啟 `function.json` 檔案進行編輯。

   ![針對 "function. json" 檔案按一下編輯](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. 在 `bindings` 物件中，檢查 `authLevel` 屬性是否存在。 如果屬性存在，請將屬性值設定為 `anonymous`。 否則，請新增該屬性並設定值。

   ![新增 "authLevel" 屬性並設定為 "anonymous"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. 完成時，請儲存設定，然後繼續到下一節。

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>設定函數應用程式的 Azure AD 驗證

開始這項工作之前，請先找出這些值並放在一旁，供稍後使用：

* 針對系統指派的身分識別而產生的物件識別碼，代表您的邏輯應用程式

  * 若要產生此物件識別碼，請[啟用系統指派給邏輯應用程式的身分識別](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)。

  * 否則，若要尋找此物件識別碼，請在邏輯應用程式設計工具中開啟您的邏輯應用程式。 在邏輯應用程式功能表的 [設定] 下，選取 [身分識別] > [系統指派]。

* Azure Active Directory (Azure AD) 中租用戶的目錄識別碼

  若要取得租用戶的目錄識別碼，您可以執行 [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) Powershell 命令。 或者，在 Azure 入口網站中執行下列步驟：

  1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取您的函數應用程式。

  1. 尋找並選取您的 Azure AD 租用戶。 這些步驟使用 "Fabrikam" 作為範例租戶用。

  1. 在租用戶功能表的 [管理] 下，選取 [屬性]。

  1. 複製租用戶的目錄識別碼 (舉例來說)，並儲存該識別碼供稍後使用。

     ![尋找並複製 Azure AD 租用戶的目錄識別碼](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* 資源識別碼，代表您想要存取的目標資源

  * 若要尋找這些資源識別碼，請檢閱[支援 Azure AD 的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

  > [!IMPORTANT]
  > 資源識別碼必須完全符合 Azure AD 預期的值，包括任何必要的尾端斜線。

  您稍後[將函式動作設定為使用系統指派的身分識別](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)時，此資源識別碼就是您在 [對象] 屬性中使用的同一個值。

現在，您可以開始為函數應用程式設定 Azure AD 驗證。

1. 在 [Azure 入口網站](https://portal.azure.com)中，尋找並選取您的函數應用程式。

1. 在 [函數應用程式] 窗格中，選取 [平台功能]。 在 [網路] 下，選取 [驗證/授權]。

   ![檢視驗證和授權設定](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. 將 [App Service 驗證] 設定改為 [開啟]。 從 [當要求未經驗證時所要採取的動作] 清單中，選取 [使用 Azure Active Directory 登入]。 在 [驗證提供者] 底下，選取 [Azure Active Directory]。

   ![啟用透過 Azure AD 進行驗證](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. 在 [Azure Active Directory 設定] 窗格上，請遵循下列步驟：

   1. 將 [管理模式] 設定為 [進階]。

   1. 在 [用戶端識別碼] 屬性中，輸入物件識別碼，代表系統指派給邏輯應用程式的身分識別。

   1. 在 [簽發者 URL] 屬性中，輸入 `https://sts.windows.net/` URL，並附加 Azure AD 租用戶的目錄識別碼。

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. 在 [允許的權杖對象] 屬性中，輸入資源識別碼，代表您想要存取的目標資源。

      您稍後[將函式動作設定為使用系統指派的身分識別](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)時，此資源識別碼就是您在 [對象] 屬性中使用的同一個值。

   此時，您的版本看起來類似此範例：

   ![Azure Active Directory 驗證設定](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. 完成後，選取 [確定]。

1. 返回邏輯應用程式設計工具，並遵循[使用受控識別來驗證存取權](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)的步驟。

## <a name="next-steps"></a>後續步驟

* 了解[Logic Apps 連接器](../connectors/apis-list.md)
