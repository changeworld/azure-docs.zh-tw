---
title: 從 Azure 邏輯應用添加和調用 Azure 函數
description: 從 Azure 邏輯應用中的自動任務和工作流調用和運行 Azure 函數中的自訂代碼
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 68975f21ab810398da969384db4d3bddd22f1bd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284118"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>從 Azure 邏輯應用調用 Azure 函數

如果要在邏輯應用中運行執行特定作業的代碼，可以使用[Azure 函數創建自己的函數](../azure-functions/functions-overview.md)。 此服務可説明您創建 Node.js、C# 和 F# 函數，因此您不必構建完整的應用或基礎結構來運行代碼。 您也可以[從 Azure 函式內呼叫邏輯應用程式](#call-logic-app)。 Azure Functions 可提供在雲端進行的無伺服器運算，適合用來執行以下舉例的各種工作：

* 使用 Node.js 或 C# 中的函式來擴充邏輯應用程式的行為。
* 在邏輯應用程式工作流程中執行計算。
* 在邏輯應用程式中套用進階格式設定或計算欄位。

要在不創建 Azure 函數的情況下運行程式碼片段，請瞭解如何[添加和運行內聯代碼](../logic-apps/logic-apps-add-run-inline-code.md)。

> [!NOTE]
> 邏輯應用和 Azure 函數之間的集成當前與啟用的插槽不起作用。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* Azure 函數應用（它是 Azure 函數的容器）以及 Azure 函數。 如果您沒有函式應用程式，[請先建立該函式應用程式](../azure-functions/functions-create-first-azure-function.md)。 然後，您可以在 Azure 門戶中的邏輯應用外部或在邏輯應用設計器[中的邏輯應用內部](#create-function-designer)創建函數。

* 使用邏輯應用時，相同的要求適用于函數應用和函數，無論它們是現有還是新應用：

  * 函數應用和邏輯應用必須使用相同的 Azure 訂閱。

  * 新函數應用必須使用 .NET 或 JavaScript 作為運行時堆疊。 將新功能添加到現有函數應用時，可以選擇 C# 或 JavaScript。

  * 您的函數使用**HTTP 觸發器**範本。

    HTTP 觸發程序範本可以接受內容中有來自邏輯應用程式的 `application/json` 類型。 將 Azure 函數添加到邏輯應用時，邏輯應用設計器會顯示 Azure 訂閱中使用此範本創建的自訂函數。

  * 除非定義了[OpenAPI 定義](../azure-functions/functions-openapi-definition.md)（以前稱為[Swagger 檔](https://swagger.io/)），否則函數不使用自訂路由。

  * 如果函數具有 OpenAPI 定義，則邏輯應用設計器在使用函數參數時為您提供更豐富的體驗。 邏輯應用程式若要尋找和存取具有 OpenAPI 定義的函式，必須先[遵循下列步驟來設定函式應用程式](#function-swagger)。

* 您要在其中新增函式的邏輯應用程式，並包括作為邏輯應用程式中第一個步驟的[觸發程序](../logic-apps/logic-apps-overview.md#logic-app-concepts)

  在添加運行函數的操作之前，邏輯應用必須從觸發器開始。 如果您還不熟悉邏輯應用程式，請檢閱[什麼是 Azure Logic Apps？](../logic-apps/logic-apps-overview.md)和[快速入門：建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>查找具有 OpenAPI 說明的函數

為了在邏輯應用設計器中使用函數參數時獲得更豐富的體驗，請為您的函數[生成 OpenAPI 定義](../azure-functions/functions-openapi-definition.md)（以前稱為[Swagger 檔](https://swagger.io/)）。 若要設定函式應用程式，讓邏輯應用程式可以尋找和使用具有 Swagger 描述的函式，請遵循下列步驟：

1. 確保函數應用正在主動運行。

1. 在函數應用中，設置[跨源資源分享 （CORS），](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)以便按照以下步驟允許所有源：

   1. 從 **"功能應用"** 清單中，選擇函數應用。 在右側窗格中，選擇 **"平臺"功能** > **CORS**。

      ![選取您的函式應用程式 > [平台功能] > [CORS]](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. 在**CORS**下，添加星號**`*`**（） 萬用字元，但刪除清單中的所有其他源，然後選擇 **"保存**"。

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

在使用邏輯應用設計器從邏輯應用內部開始創建 Azure 函數之前，必須首先具有 Azure 函數應用，該應用是函數的容器。 如果您沒有函式應用程式，請先建立該函式應用程式。 請參閱[在 Azure 入口網站中建立您的第一個函式](../azure-functions/functions-create-first-azure-function.md)。

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中打開邏輯應用。

1. 若要建立並新增函式，請遵循適用於您案例的步驟：

   * 在邏輯應用工作流的最後一步下，選擇 **"新建步驟**"。

   * 在邏輯應用工作流的現有步驟之間，將滑鼠移到箭頭上，選擇加號 （+） 符號，然後選擇"**添加操作**"。

1. 在搜尋方塊中，輸入「azure functions」作為篩選條件。 從動作清單中選擇 **"選擇 Azure 函數**操作"，例如：

   ![尋找 "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 從函式應用程式清單中，選取您的函式應用程式。 動作清單打開後，選擇此操作：**創建新函數**

   ![選取函式應用程式](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. 在函式定義編輯器中，定義您的函式：

   1. 在 [函式名稱]**** 方塊中，為您的函式提供名稱。

   1. 在 **"代碼"** 框中，將代碼添加到函數範本中，包括函數完成運行後要返回到邏輯應用的回應和有效負載。 當您完成時，選取 [建立]****。

   例如：

   ![定義函式](./media/logic-apps-azure-functions/add-code-function-definition.png)

   在範本的代碼中，*`context`物件*是指邏輯應用在後續步驟中通過 **"請求正文"** 欄位發送的消息。 若要從您的函式內存取 `context` 物件的屬性，請使用此語法：

   `context.body.<property-name>`

   例如，若要參考 `context` 物件內的 `content` 屬性，請使用此語法：

   `context.body.content`

   範本程式碼也包含 `input` 變數，其中儲存著來自 `data` 參數的值，因此函式可以根據該值執行作業。 在 JavaScript 函數中，`data` 變數也是 `context.body` 的捷徑。

   > [!NOTE]
   > 這裡的 `body` 屬性適用於 `context` 物件，與動作輸出中可能也會傳遞給函式的**本文**權杖不同。

1. 在 [要求本文]**** 方塊中，提供函式的輸入 (必須以 JavaScript 物件標記法 (JSON) 物件進行格式化)。

   此輸入是邏輯應用程式傳送給函式的「內容物件」** 或訊息。 當您在 [要求本文]**** 欄位內按一下時，動態內容清單會隨即出現，讓您可以選取前面步驟中的輸出權杖。 此示例指定上下文負載包含名為具有`content`電子郵件觸發器的**From**權杖值的屬性。

   ![「要求本文」範例 - 內容物件承載](./media/logic-apps-azure-functions/function-request-body-example.png)

   在此處，內容物件不會轉換為字串，所以物件內容會直接新增至 JSON 承載。 不過，如果內容物件不是傳遞字串、JSON 物件或 JSON 陣列的 JSON 權杖，您就會收到錯誤。 因此，如果此示例改用 **"接收時間**"權杖，則可以通過添加雙引號將上下文物件轉換為字串。

   ![將物件轉換為字串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 要指定其他詳細資訊（如要使用的方法、請求標頭或查詢參數或身份驗證），請打開 **"添加新參數**清單"，然後選擇所需的選項。 對於身份驗證，您的選項因所選功能而異。 請參閱[啟用 Azure 函數的身份驗證](#enable-authentication-functions)。

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>在邏輯應用程式中新增現有函式

若要從邏輯應用程式呼叫現有 Azure 函式，您可以在邏輯應用程式設計工具中新增 Azure 函式，例如任何其他動作。

1. 在[Azure 門戶](https://portal.azure.com)中，在邏輯應用設計器中打開邏輯應用。

1. 在要添加函數的步驟下，選擇 **"新建步驟**"。

1. 在搜索框中**選擇操作**，在搜索框中輸入"azure 函數"作為篩選器。 從動作清單中選擇 **"選擇 Azure 函數**操作"。

   ![尋找 "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 從函式應用程式清單中，選取您的函式應用程式。 函式清單出現後，選取您的函式。

   ![選取函式應用程式和 Azure 函式](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   對於具有 API 定義（斯瓦格描述）並[設置的函數，以便邏輯應用可以找到和訪問這些函數](#function-swagger)，您可以選擇**Swagger 操作**。

   ![選擇函數應用"搖曳操作"和 Azure 函數](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. 在 [要求本文]**** 方塊中，提供函式的輸入 (必須以 JavaScript 物件標記法 (JSON) 物件進行格式化)。

   此輸入是邏輯應用程式傳送給函式的「內容物件」** 或訊息。 按一下"**請求正文**"欄位中時，將顯示動態內容清單，以便從前面的步驟中為輸出選擇權杖。 此示例指定上下文負載包含名為具有`content`電子郵件觸發器的**From**權杖值的屬性。

   ![「要求本文」範例 - 內容物件承載](./media/logic-apps-azure-functions/function-request-body-example.png)

   在此處，內容物件不會轉換為字串，所以物件內容會直接新增至 JSON 承載。 不過，如果內容物件不是傳遞字串、JSON 物件或 JSON 陣列的 JSON 權杖，您就會收到錯誤。 因此，如果此範例改為使用**接收時間**權仗，則您可以在內容物件上加上雙引號，將內容物件轉換為字串：

   ![將物件轉換為字串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 要指定其他詳細資訊（如要使用的方法、請求標頭、查詢參數或身份驗證），請打開 **"添加新參數**清單"，然後選擇所需的選項。 對於身份驗證，您的選項因所選功能而異。 請參閱[在 Azure 函數中啟用身份驗證](#enable-authentication-functions)。

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>從 Azure 函式呼叫邏輯應用

若要從 Azure 函式內觸發邏輯應用程式，該邏輯應用程式必須以能提供可呼叫端點的觸發程序作為開頭。 例如，您可以使用 **HTTP**、**要求**、**Azure 佇列**或**事件方格**觸發程序來起始邏輯應用程式。 在函式內部，將 HTTP POST 要求傳送給該觸發程序的 URL，並包含您想要讓該邏輯應用程式處理的承載。 如需詳細資訊，請參閱[呼叫、觸發或巢狀邏輯應用程式](../logic-apps/logic-apps-http-endpoint.md)。

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>為 Azure 函數啟用身份驗證

要驗證對其他 Azure 活動目錄 （Azure AD） 租戶中的資源的存取權限，而無需登錄並提供憑據或機密，邏輯應用可以使用[託管標識](../active-directory/managed-identities-azure-resources/overview.md)（以前稱為託管服務標識或 MSI）。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。 詳細瞭解支援[Azure AD 身份驗證的託管標識的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

如果將邏輯應用設置為使用系統分配的標識或手動創建的使用者分配的標識，則邏輯應用中的 Azure 函數也可以使用相同的標識進行身份驗證。 有關邏輯應用中 Azure 函數的身份驗證支援的詳細資訊，請參閱[將身份驗證添加到出站調用](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

要將託管標識與函數一起設置和使用，請按照以下步驟操作：

1. 在邏輯應用中啟用託管標識，並設置該標識對目標資源的存取權限。 請參閱[在 Azure 邏輯應用中使用託管標識對 Azure 資源的身份驗證訪問](../logic-apps/create-managed-service-identity.md)。

1. 按照以下步驟在 Azure 函數和函數應用中啟用身份驗證：

   * [在函數中設置匿名身份驗證](#set-authentication-function-app)
   * [在函數應用中設置 Azure AD 身份驗證](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>在函數中設置匿名身份驗證

要在 Azure 函數中使用邏輯應用的託管標識，可以將函數的身份驗證級別設置為匿名。 否則，邏輯應用將引發"錯誤請求"錯誤。

1. 在[Azure 門戶](https://portal.azure.com)中，查找並選擇函數應用。 這些步驟使用"Fabrikam函數應用程式"作為示例功能應用。

1. 在功能應用窗格中，選擇 **"平臺"功能**。 在**開發工具**下，選擇**高級工具（庫杜）。**

   ![為庫杜打開高級工具](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. 在 Kudu 網站的標題列上，從**調試主控台**功能表中選擇**CMD**。

   ![從調試主控台功能表中，選擇"CMD"選項](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. 下一頁顯示後，從資料夾清單中，選擇**網站** > **wwwroot** > *您的功能*。 這些步驟使用"FabrikamAzure函數"作為示例函數。

   ![選擇"網站">"wwwroot">函數](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. 打開`function.json`檔進行編輯。

   ![按一下"函數.json"檔的編輯](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. 在物件`bindings`中，檢查該`authLevel`屬性是否存在。 如果屬性存在，則將屬性值設置為`anonymous`。 否則，添加該屬性並設置該值。

   ![添加"authLevel"屬性並設置為"匿名"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. 完成後，請保存設置，然後繼續下一節。

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>為函數應用設置 Azure AD 身份驗證

在開始此任務之前，查找這些值並將其放在一邊供以後使用：

* 為表示邏輯應用的系統分配標識生成的物件識別碼

  * 要生成此物件識別碼，[請啟用邏輯應用的系統分配的標識](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)。

  * 否則，要查找此物件識別碼，請在邏輯應用設計器中打開邏輯應用。 在邏輯應用功能表中，**在"設置"** 下，選擇分配的**標識** > **系統**。

* Azure 活動目錄 （Azure AD） 中的租戶的目錄 ID

  要獲取租戶的目錄 ID，可以運行[`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount)Powershell 命令。 或者，在 Azure 門戶中，按照以下步驟操作：

  1. 在[Azure 門戶](https://portal.azure.com)中，查找並選擇函數應用。

  1. 查找並選擇 Azure AD 租戶。 這些步驟使用"Fabrikam"作為示例租戶。

  1. 在租戶的功能表上，在 **"管理**"下，選擇**屬性**。

  1. 例如，複製租戶的目錄 ID，並保存該 ID 以供以後使用。

     ![查找和複製 Azure AD 租戶的目錄 ID](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* 要訪問的目標資源的資源識別碼

  * 要查找這些資源標識，請查看支援 Azure [AD 的 Azure 服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

  > [!IMPORTANT]
  > 此資源識別碼 必須與 Azure AD 期望的值完全符合，包括任何必需的尾隨斜杠。

  此資源識別碼 與以後在 **"訪問群體"** 屬性中使用的值相同，當您[設置函數操作以使用系統分配的標識](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)時。

現在，您已準備好為函數應用設置 Azure AD 身份驗證。

1. 在[Azure 門戶](https://portal.azure.com)中，查找並選擇函數應用。

1. 在功能應用窗格中，選擇 **"平臺"功能**。 在 **"網路"** 下，選擇**身份驗證/授權**。

   ![查看身份驗證和授權設置](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. 將**應用服務身份驗證**設置更改為 **"打開**"。 從**請求未經過身份驗證時執行的操作**清單中，選擇使用 Azure**活動目錄登錄**。 在 [驗證提供者]**** 底下，選取 [Azure Active Directory]****。

   ![使用 Azure AD 打開身份驗證](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. 在**Azure 活動目錄設置**窗格中，按照以下步驟操作：

   1. 將**管理模式**設置為 **"高級**"。

   1. 在**用戶端 ID**屬性中，輸入邏輯應用的系統分配標識的物件識別碼。

   1. 在**頒發者 Url**屬性中`https://sts.windows.net/`，輸入 URL 並追加 Azure AD 租戶的目錄 ID。

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. 在 **"允許的權杖訪問群體"** 屬性中，輸入要訪問的目標資源的資源識別碼。

      此資源識別碼 與以後在 **"訪問群體"** 屬性中使用的值相同，當您[設置函數操作以使用系統分配的標識](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)時。

   此時，您的版本與此示例類似：

   ![Azure 活動目錄身份驗證設置](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. 完成後，選取 [確定]****。

1. 返回到邏輯應用設計器，然後按照[步驟對託管標識的訪問進行身份驗證](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)。

## <a name="next-steps"></a>後續步驟

* 了解[Logic Apps 連接器](../connectors/apis-list.md)
