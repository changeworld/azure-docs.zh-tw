---
title: 使用 Azure API 管理以 OpenAPI 公開您的函式
description: 建立 OpenAPI 定義，讓其他應用程式和服務可在 Azure 中呼叫您的函式。
ms.topic: tutorial
ms.date: 04/21/2020
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 7d63d5ea17184ffa6e456877079da0821a75d59e
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121387"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>使用 Azure API 管理為無伺服器 API 建立 OpenAPI 定義

REST API 通常會使用 OpenAPI 定義來描述。 此定義包含有關 API 中可以使用哪些作業，以及應該如何結構化 API 之要求和回應資料的資訊。

在本教學課程中，您會建立一個函式，來判斷風力渦輪機的緊急修復是否符合成本效益。 然後，您會使用 [Azure API 管理](../api-management/api-management-key-concepts.md)為函式應用程式建立 OpenAPI 定義，以便從其他應用程式和服務呼叫函式。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在 Azure 中建立函式
> * 使用 Azure API 管理產生 OpenAPI 定義
> * 呼叫函式以測試定義
> * 下載 OpenAPI 定義

## <a name="create-a-function-app"></a>建立函數應用程式

您必須擁有函式應用程式以便主控函式的執行。 函式應用程式可讓您將多個函式群組為邏輯單位，以方便您管理、部署、調整和共用資源。

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>建立函式

本教學課程會使用 HTTP 觸發的函式，並採用兩個參數：

* 修復渦輪機的預估時間 (以小時為單位)。
* 渦輪機的容量 (以千瓦為單位)。 

然後，此函式會計算修復的費用，以及渦輪機在 24 小時內的收入。 若要在 [Azure 入口網站](https://portal.azure.com)中建立 HTTP 觸發的函式：

1. 在函式應用程式中的左側功能表中選取 [函式]，然後從頂端功能表選取 [新增]。

1. 在 [新增函式] 視窗中，選取 [HTTP 觸發程序]。

1. 針對 [新增函式]，輸入 `TurbineRepair`。 

1. 從 **[授權層級](functions-bindings-http-webhook-trigger.md#http-auth)** 下拉式清單中選擇 [函式]，然後選取 [建立函式]。

    :::image type="content" source="media/functions-openapi-definition/select-http-trigger-openapi.png" alt-text="建立適用於 OpenAPI 的 HTTP 函式":::

1. 選取 [程式碼 + 測試]，然後從下拉式清單中選取 [run.csx]。 將 run.csx C# 指令碼檔案的內容取代為下列程式碼，然後選擇 [儲存]：

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    此函式程式碼會傳回 `Yes` 或 `No` 的訊息，指出緊急修復是否符合成本效益。 此外也會傳回渦輪機所代表的收益機會，與修復渦輪機的成本。

1. 若要測試函式，請選取 [測試]、選取 [輸入] 索引標籤、針對 [主體] 輸入下列內容，然後選取 [執行]：

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    :::image type="content" source="media/functions-openapi-definition/test-function.png" alt-text="在 Azure 入口網站中測試函式":::

    在 [輸出] 索引標籤中會傳回下列輸出：

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

現在，您有一個函式，可判斷緊急修復是否符合成本效益。 接下來，您會為函式應用程式產生 OpenAPI 定義。

## <a name="generate-the-openapi-definition"></a>產生 OpenAPI 定義

若要產生 OpenAPI 定義：

1. 選取函式應用程式，從左側功能表中選擇 [API 管理]，然後選取 [API 管理] 底下的 [新建]。

    :::image type="content" source="media/functions-openapi-definition/select-all-settings-openapi.png" alt-text="選擇 API 管理":::


1. 使用下表中指定的 API 管理設定：

    | 設定      | 建議的值  | 描述                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名稱** | 全域唯一的名稱 | 系統會根據您的函式應用程式名稱產生名稱。 |
    | **訂用帳戶** | 您的訂用帳戶 | 這項新資源建立所在的訂用帳戶。 |  
    | **[資源群組](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 與您的函式應用程式相同的資源，系統應該會為您設定。 |
    | **位置** | 美國西部 | 選擇 [美國西部] 位置。 |
    | **組織名稱** | Contoso | 用於開發人員入口網站和電子郵件通知的組織名稱。 |
    | **管理員電子郵件** | 您的電子郵件 | 從 API 管理接收系統通知的電子郵件。 |
    | **定價層** | 耗用量 | 取用量層並非所有區域都能使用。 如需完整的定價詳細資料，請參閱 [API 管理定價頁面](https://azure.microsoft.com/pricing/details/api-management/) |

    ![建立新的 API 管理服務](media/functions-openapi-definition/new-apim-service-openapi.png)

1. 選擇 [建立] 以建立 API 管理執行個體，這可能需要幾分鐘的時間。

1. Azure 在建立執行個體後，會啟用頁面上的 [啟用 Application Insights] 選項。 請選取該選項以將記錄傳送至與函式應用程式相同的位置，然後選取 [連結 API]。

1. [匯入 Azure Functions] 隨即開啟，並醒目提示 **TurbineRepair** 函式。 選擇 [選取] 以繼續操作。

    ![將 Azure Functions 匯入 API 管理中](media/functions-openapi-definition/import-function-openapi.png)

1. 在 [從函式應用程式建立] 頁面上接受預設值，然後選取 [建立]。

    :::image type="content" source="media/functions-openapi-definition/create-function-openapi.png" alt-text="從函式應用程式建立":::

    Azure 會建立函式的 API。

## <a name="test-the-api"></a>測試 API

使用 OpenAPI 定義之前，應先確認 API 能夠運作。

1. 在您的函式應用程式頁面上，依序選取 [API 管理]、[測試] 索引標籤和 [POST TurbineRepair]。 

1. 在 [要求本文] 中輸入下列程式碼：

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. 選取 [傳送]，然後檢視 **HTTP 回應**。

    :::image type="content" source="media/functions-openapi-definition/test-function-api-openapi.png" alt-text="測試函式 API":::

## <a name="download-the-openapi-definition"></a>下載 OpenAPI 定義

如果您的 API 可以正常運作，就可以下載 OpenAPI 定義。

1. 選取頁面頂端的 [下載 OpenAPI 定義]。
   
   ![下載 OpenAPI 定義](media/functions-openapi-definition/download-definition.png)

2. 儲存已下載的 JSON 檔案，然後加以開啟。 檢閱定義。

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

您已使用 API 管理整合產生函式的 OpenAPI 定義。 現在可以在入口網站的 API 管理中編輯定義。 您也可以[深入了解 API 管理](../api-management/api-management-key-concepts.md)。

> [!div class="nextstepaction"]
> [在 API 管理中編輯 OpenAPI 定義](../api-management/edit-api.md)
