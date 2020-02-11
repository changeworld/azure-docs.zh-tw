---
title: 從 Azure Logic Apps 新增和呼叫 Azure Functions
description: 從中的自動化工作和工作流程，在您的 Azure Functions 中呼叫並執行自訂程式碼 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 68975f21ab810398da969384db4d3bddd22f1bd9
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116914"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>從 Azure Logic Apps 呼叫 Azure 函式

當您想要執行程式碼以在邏輯應用程式中執行特定作業時，您可以使用[Azure Functions](../azure-functions/functions-overview.md)來建立自己的函式。 此服務可協助您建立 node.js、 C#和F#函式，因此您不需要建立完整的應用程式或基礎結構來執行程式碼。 您也可以[從 Azure 函式內呼叫邏輯應用程式](#call-logic-app)。 Azure Functions 可提供在雲端進行的無伺服器運算，適合用來執行以下舉例的各種工作：

* 使用 Node.js 或 C# 中的函式來擴充邏輯應用程式的行為。
* 在邏輯應用程式工作流程中執行計算。
* 在邏輯應用程式中套用進階格式設定或計算欄位。

若要在不建立 Azure 函式的情況下執行程式碼片段，請瞭解如何[新增和執行內嵌程式碼](../logic-apps/logic-apps-add-run-inline-code.md)。

> [!NOTE]
> Logic Apps 和 Azure Functions 之間的整合目前不適用於已啟用的位置。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* Azure 函數應用程式，這是 Azure 函式的容器，以及您的 Azure 函式。 如果您沒有函式應用程式，[請先建立該函式應用程式](../azure-functions/functions-create-first-azure-function.md)。 接著，您可以在 Azure 入口網站的邏輯應用程式之外，或從邏輯應用程式設計工具中的[邏輯應用程式內部](#create-function-designer)建立函式。

* 使用邏輯應用程式時，相同的需求適用于函式應用程式和函式，不論它們是現有或新的：

  * 您的函數應用程式和邏輯應用程式必須使用相同的 Azure 訂用帳戶。

  * 新的函數應用程式必須使用 .NET 或 JavaScript 做為執行時間堆疊。 當您將新的函式新增至現有的函數應用程式時C# ，您可以選取或 JavaScript。

  * 您的函式會使用**HTTP 觸發**程式範本。

    HTTP 觸發程序範本可以接受內容中有來自邏輯應用程式的 `application/json` 類型。 當您將 Azure 函式新增至邏輯應用程式時，邏輯應用程式設計工具會顯示在您的 Azure 訂用帳戶內從這個範本建立的自訂函式。

  * 您的函式不會使用自訂路由，除非您已定義[OpenAPI 定義](../azure-functions/functions-openapi-definition.md)（先前稱為[Swagger](https://swagger.io/)檔案）。

  * 如果您有函式的 OpenAPI 定義，當您使用函式參數時，Logic Apps 設計工具會提供更豐富的體驗。 邏輯應用程式若要尋找和存取具有 OpenAPI 定義的函式，必須先[遵循下列步驟來設定函式應用程式](#function-swagger)。

* 您要在其中新增函式的邏輯應用程式，並包括作為邏輯應用程式中第一個步驟的[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)

  您的邏輯應用程式必須以觸發程式開頭，才可以新增執行函式的動作。 如果您還不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>尋找具有 OpenAPI 描述的函式

當您在 Logic Apps 設計工具中使用函式參數時，如需更豐富的體驗，請針對您的函[式產生 OpenAPI 定義](../azure-functions/functions-openapi-definition.md)（之前稱為[Swagger](https://swagger.io/)檔案）。 若要設定函式應用程式，讓邏輯應用程式可以尋找和使用具有 Swagger 描述的函式，請遵循下列步驟：

1. 請確定您的函數應用程式正在執行。

1. 在您的函式應用程式中，設定[跨原始來源資源分享（CORS）](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) ，以遵循這些步驟來允許所有來源：

   1. 從 [**函數應用程式**] 清單中，選取您的函數應用程式。 在右窗格中，選取 [**平臺功能**] [ > **CORS**]。

      ![選取您的函式應用程式 > [平台功能] > [CORS]](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. 在 [ **CORS**] 底下，新增星號（ **`*`** ）萬用字元，但移除清單中的所有其他來源，然後選取 [**儲存**]。

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

1. 函式會建立 `data` 變數，並將 `body` 物件內的 `request` 物件指派給該變數。 此函式會使用點 (.) 運算子來參考 `body` 物件內的 `request` 物件：

   ```javascript
   var data = request.body;
   ```

1. 此函式現在可以透過 `date` 變數存取 `data` 屬性，並藉由呼叫 `ToDateString()` 函式將該屬性值從 DateTime 類型轉換為 DateString 類型。 此函式也會透過函式回應中的 `body` 屬性傳回結果：

   ```javascript
   body: data.date.ToDateString();
   ```

您已建立 Azure 函式，接下來請遵循如何[將函式新增至邏輯應用程式](#add-function-logic-app)的步驟。

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>在邏輯應用程式內部建立函式

在您可以使用邏輯應用程式設計工具從邏輯應用程式內開始建立 Azure 函式之前，您必須先擁有 Azure 函式應用程式，這是函式的容器。 如果您沒有函式應用程式，請先建立該函式應用程式。 請參閱[在 Azure 入口網站中建立您的第一個函式](../azure-functions/functions-create-first-azure-function.md)。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 若要建立並新增函式，請遵循適用於您案例的步驟：

   * 在邏輯應用程式工作流程的最後一個步驟底下，選取 [**新增步驟**]。

   * 在邏輯應用程式工作流程中的現有步驟之間，將滑鼠移至箭號上，選取加號（+），然後選取 [**新增動作**]。

1. 在搜尋方塊中，輸入「azure functions」作為篩選條件。 從 [動作] 清單中，選取 [**選擇 Azure 函數**] 動作，例如：

   ![尋找 "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 從函式應用程式清單中，選取您的函式應用程式。 在動作清單開啟後，選取此動作：**建立新**的函式

   ![選取函式應用程式](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. 在函式定義編輯器中，定義您的函式：

   1. 在 [函式名稱] 方塊中，為您的函式提供名稱。

   1. 在 [程式**代碼**] 方塊中，將您的程式碼新增至函式範本，包括您想要在函式完成執行後傳回給邏輯應用程式的回應和承載。 當您完成時，選取 [建立]。

   例如，

   ![定義函式](./media/logic-apps-azure-functions/add-code-function-definition.png)

   在範本的程式碼中， *物件`context`* 代表邏輯應用程式透過**要求本文** (位於後面的步驟中) 欄位傳送的訊息。 若要從您的函式內存取 `context` 物件的屬性，請使用此語法：

   `context.body.<property-name>`

   例如，若要參考 `content` 物件內的 `context` 屬性，請使用此語法：

   `context.body.content`

   範本程式碼也包含 `input` 變數，其中儲存著來自 `data` 參數的值，因此函式可以根據該值執行作業。 在 JavaScript 函數中，`data` 變數也是 `context.body` 的捷徑。

   > [!NOTE]
   > 這裡的 `body` 屬性適用於 `context` 物件，與動作輸出中可能也會傳遞給函式的**本文**權杖不同。

1. 在 [要求本文] 方塊中，提供函式的輸入 (必須以 JavaScript 物件標記法 (JSON) 物件進行格式化)。

   此輸入是邏輯應用程式傳送給函式的「內容物件」或訊息。 當您在 [要求本文] 欄位內按一下時，動態內容清單會隨即出現，讓您可以選取前面步驟中的輸出權杖。 這個範例會指定內容承載包含名為 `content` 的屬性，其具有**來自**電子郵件觸發程式的 from token 值。

   ![「要求本文」範例 - 內容物件承載](./media/logic-apps-azure-functions/function-request-body-example.png)

   在此處，內容物件不會轉換為字串，所以物件內容會直接新增至 JSON 承載。 不過，如果內容物件不是傳遞字串、JSON 物件或 JSON 陣列的 JSON 權杖，您就會收到錯誤。 因此，如果此範例改為使用**接收的時間**token，您可以藉由加入雙引號，將內容物件轉換為字串。

   ![將物件轉換為字串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 若要指定其他詳細資料，例如要使用的方法、要求標頭或查詢參數，或驗證，請開啟 [**加入新的參數**] 清單，然後選取您想要的選項。 針對驗證，您的選項會根據您選取的函式而有所不同。 請參閱[啟用 Azure 函式的驗證](#enable-authentication-functions)。

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>在邏輯應用程式中新增現有函式

若要從邏輯應用程式呼叫現有 Azure 函式，您可以在邏輯應用程式設計工具中新增 Azure 函式，例如任何其他動作。

1. 在 [Azure 入口網站](https://portal.azure.com)的邏輯應用程式設計工具中，開啟邏輯應用程式。

1. 在您要新增函式的步驟底下，選取 [**新增步驟**]。

1. 在 [**選擇動作**] 下的 [搜尋] 方塊中，輸入「azure 函式」作為篩選準則。 從 [動作] 清單中，選取 [**選擇 Azure 函數**] 動作。

   ![尋找 "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 從函式應用程式清單中，選取您的函式應用程式。 函式清單出現後，選取您的函式。

   ![選取函式應用程式和 Azure 函式](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   針對具有 API 定義（Swagger 描述）並已設定好[讓邏輯應用程式可以尋找和存取這些](#function-swagger)函式的函式，您可以選取 [ **Swagger 動作**]。

   ![選取您的函數應用程式、「Swagger 動作」和您的 Azure 函式](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. 在 [要求本文] 方塊中，提供函式的輸入 (必須以 JavaScript 物件標記法 (JSON) 物件進行格式化)。

   此輸入是邏輯應用程式傳送給函式的「內容物件」或訊息。 當您按一下 [**要求**本文] 欄位時，動態內容清單隨即出現，讓您可以從先前的步驟中選取輸出的權杖。 這個範例會指定內容承載包含名為 `content` 的屬性，其具有**來自**電子郵件觸發程式的 from token 值。

   ![「要求本文」範例 - 內容物件承載](./media/logic-apps-azure-functions/function-request-body-example.png)

   在此處，內容物件不會轉換為字串，所以物件內容會直接新增至 JSON 承載。 不過，如果內容物件不是傳遞字串、JSON 物件或 JSON 陣列的 JSON 權杖，您就會收到錯誤。 因此，如果此範例改為使用**接收時間**權仗，則您可以在內容物件上加上雙引號，將內容物件轉換為字串：

   ![將物件轉換為字串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 若要指定其他詳細資料，例如要使用的方法、要求標頭、查詢參數或驗證，請開啟 [**加入新的參數**] 清單，然後選取您想要的選項。 針對驗證，您的選項會根據您選取的函式而有所不同。 請參閱[在 Azure 函式中啟用驗證](#enable-authentication-functions)。

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>從 Azure 函式呼叫邏輯應用程式

若要從 Azure 函式內觸發邏輯應用程式，該邏輯應用程式必須以能提供可呼叫端點的觸發程序作為開頭。 例如，您可以使用 **HTTP**、**要求**、**Azure 佇列**或**事件方格**觸發程序來起始邏輯應用程式。 在函式內部，將 HTTP POST 要求傳送給該觸發程序的 URL，並包含您想要讓該邏輯應用程式處理的承載。 如需詳細資訊，請參閱[呼叫、觸發或巢狀邏輯應用程式](../logic-apps/logic-apps-http-endpoint.md)。

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>啟用 Azure 函式的驗證

若要驗證對其他 Azure Active Directory （Azure AD）租使用者中資源的存取權，而不需要登入並提供認證或密碼，您的邏輯應用程式可以使用[受控識別](../active-directory/managed-identities-azure-resources/overview.md)（先前稱為受控服務識別或 MSI）。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。 深入瞭解[支援受控識別以進行 Azure AD 驗證的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

如果您將邏輯應用程式設定為使用系統指派的身分識別，或手動建立的使用者指派身分識別，則邏輯應用程式中的 Azure 函式也可以使用相同的身分識別進行驗證。 如需在邏輯應用程式中對 Azure 函式進行驗證支援的詳細資訊，請參閱[將驗證新增至輸出呼叫](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

若要設定和使用受控識別搭配您的函式，請遵循下列步驟：

1. 在您的邏輯應用程式上啟用受控識別，並設定該身分識別對目標資源的存取權。 請參閱[使用 Azure Logic Apps 中的受控識別來驗證對 Azure 資源的存取](../logic-apps/create-managed-service-identity.md)。

1. 遵循下列步驟，在您的 Azure 函式和函式應用程式中啟用驗證：

   * [在您的函式中設定匿名驗證](#set-authentication-function-app)
   * [在您的函數應用程式中設定 Azure AD 驗證](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>在您的函式中設定匿名驗證

若要在您的 Azure 函式中使用邏輯應用程式的受控識別，您已將函式的驗證層級設為匿名。 否則，您的邏輯應用程式會擲回「BadRequest」錯誤。

1. 在  [Azure 入口網站](https://portal.azure.com)中，尋找並選取您的函數應用程式。 這些步驟會使用 "FabrikamFunctionApp" 作為範例函數應用程式。

1. 在 [函數應用程式] 窗格上，選取 [**平臺功能**]。 在 [**開發工具**] 底下，選取 **[Advanced tools （Kudu）** ]。

   ![開啟 advanced tools for Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. 在 Kudu 網站的標題列上，從 [**偵錯主控台**] 功能表中，選取 [ **CMD**]。

   ![從 [調試] 主控台功能表中，選取 [CMD] 選項](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. 下一個頁面出現之後，從 [資料夾] 清單中，選取 [ **site** > **wwwroot** > *您的函式*]。 這些步驟會使用 "FabrikamAzureFunction" 作為範例函數。

   ![> 您的函式中選取 "site" > "wwwroot"](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. 開啟 `function.json` 檔案進行編輯。

   ![針對 "function. json" 檔案按一下 [編輯]](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. 在 `bindings` 物件中，檢查 `authLevel` 屬性是否存在。 如果屬性存在，請將屬性值設定為 `anonymous`。 否則，請加入該屬性並設定值。

   ![新增 "authLevel" 屬性並設定為 "anonymous"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. 當您完成時，請儲存您的設定，然後繼續進行下一節。

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>設定函數應用程式的 Azure AD 驗證

開始這項工作之前，請先找出這些值，並將它們放在一旁，以供稍後使用：

* 為系統指派的身分識別產生的物件識別碼，代表您的邏輯應用程式

  * 若要產生此物件識別碼，請[啟用邏輯應用程式系統指派的身分識別](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)。

  * 否則，若要尋找此物件識別碼，請在邏輯應用程式設計工具中開啟邏輯應用程式。 在邏輯應用程式功能表的 [**設定**] 下，選取 [身分**識別** > **系統指派**]。

* Azure Active Directory （Azure AD）中租使用者的目錄識別碼

  若要取得租使用者的目錄識別碼，您可以執行[`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) Powershell 命令。 或者，在 Azure 入口網站中，依照下列步驟執行：

  1. 在  [Azure 入口網站](https://portal.azure.com)中，尋找並選取您的函數應用程式。

  1. 尋找並選取您的 Azure AD 租使用者。 這些步驟會使用「Fabrikam」作為範例租使用者。

  1. 在租使用者的功能表中，選取 [**管理**] 底下的 [**屬性**]。

  1. 複製租使用者的目錄識別碼，例如，並儲存該識別碼以供稍後使用。

     ![尋找並複製 Azure AD 租使用者的目錄識別碼](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* 您想要存取之目標資源的資源識別碼

  * 若要尋找這些資源識別碼，請參閱[支援 Azure AD 的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

  > [!IMPORTANT]
  > 此資源識別碼必須完全符合 Azure AD 預期的值，包括任何必要的尾端斜線。

  當您[將函式動作設定為使用系統指派](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)的身分識別時，此資源識別碼也會與您稍後在 [**物件**] 屬性中使用的值相同。

現在您已準備好設定函數應用程式的 Azure AD 驗證。

1. 在  [Azure 入口網站](https://portal.azure.com)中，尋找並選取您的函數應用程式。

1. 在 [函數應用程式] 窗格上，選取 [**平臺功能**]。 在 [**網路**] 底下，選取 [**驗證/授權**]。

   ![查看驗證和授權設定](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. 將**App Service Authentication**設定變更為 [**開啟**]。 從 [**要求未通過驗證時所要採取的動作**] 清單中，選取 [**使用 Azure Active Directory 登入**]。 在 [驗證提供者] 底下，選取 [Azure Active Directory]。

   ![使用 Azure AD 開啟驗證](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. 在 [ **Azure Active Directory 設定**] 窗格中，依照下列步驟執行：

   1. 將 [**管理模式]** 設定為 [ **Advanced**]。

   1. 在 [**用戶端識別碼**] 屬性中，輸入邏輯應用程式系統指派之身分識別的物件識別碼。

   1. 在 [**簽發者 Url** ] 屬性中，輸入 `https://sts.windows.net/` Url，並附加您的 Azure AD 租使用者的目錄識別碼。

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. 在 [**允許的權杖**物件] 屬性中，輸入您想要存取之目標資源的資源識別碼。

      當您[將函式動作設定為使用系統指派](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)的身分識別時，此資源識別碼的值會與您稍後在 [**物件**] 屬性中使用的值相同。

   此時，您的版本看起來類似此範例：

   ![Azure Active Directory 驗證設定](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. 完成後，選取 [確定]。

1. 返回邏輯應用程式設計工具，並遵循[步驟以使用受控識別來驗證存取權](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)。

## <a name="next-steps"></a>後續步驟

* 了解[Logic Apps 連接器](../connectors/apis-list.md)
