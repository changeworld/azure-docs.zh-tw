---
title: 使用 Azure API 管理以 OpenAPI 公開您的函式
description: 建立 OpenAPI 定義，讓其他應用程式和服務可在 Azure 中呼叫您的函式。
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9465209467c83f7de075d16e724459c307d55bd3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77210203"
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

1. 展開函式應用程式，然後選取 [函式]  旁的 [+]  按鈕。 選取 [入口網站內]   > [繼續]  。

1. 選取 [更多範本...]  ，然後選取 [完成並檢視範本] 

1. 選取 HTTP 觸發程序，輸入 `TurbineRepair` 作為函式 [名稱]  ，選擇 `Function` 作為 **[[驗證層級]](functions-bindings-http-webhook-trigger.md#http-auth)** ，然後選取 [建立]  。  

    ![建立適用於 OpenAPI 的 HTTP 函式](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. 將 run.csx C# 指令碼檔案的內容取代為下列程式碼，然後選擇 [儲存]  ：

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

    此函式程式碼會傳回 `Yes` 或 `No` 的訊息，指出緊急修復是否符合成本效益，以及渦輪機所代表的收入機會與修復渦輪機的成本。

1. 若要測試函式，請按一下最右邊的 [測試]  ，將 [測試] 索引標籤展開。針對 [要求本文]  輸入下列值，然後按一下 [執行]  。

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![在 Azure 入口網站中測試函式](media/functions-openapi-definition/test-function.png)

    下列值會在回應本文中傳回。

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

現在，您有一個函式，可判斷緊急修復是否符合成本效益。 接下來，您會為函式應用程式產生 OpenAPI 定義。

## <a name="generate-the-openapi-definition"></a>產生 OpenAPI 定義

您現在已經準備好產生 OpenAPI 定義。

1. 選取函數應用程式，然後在 [平台功能]  中，選擇 [API 管理]  ，然後選取 [API 管理]  下的 [新建]  。

    ![在 [平台功能] 中選擇 [API 管理]](media/functions-openapi-definition/select-all-settings-openapi.png)

1. 使用影像下方的資料表中指定的 API 管理設定。

    ![建立新的 API 管理服務](media/functions-openapi-definition/new-apim-service-openapi.png)

    | 設定      | 建議的值  | 描述                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名稱** | 全域唯一的名稱 | 系統會根據您的函式應用程式名稱產生名稱。 |
    | **訂用帳戶** | 您的訂用帳戶 | 這項新資源建立所在的訂用帳戶。 |  
    | **[資源群組](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 與您的函式應用程式相同的資源，系統應該會為您設定。 |
    | **位置** | 美國西部 | 選擇 [美國西部] 位置。 |
    | **組織名稱** | Contoso | 用於開發人員入口網站和電子郵件通知的組織名稱。 |
    | **管理員電子郵件** | 您的電子郵件 | 從 API 管理接收系統通知的電子郵件。 |
    | **定價層** | 使用量 (預覽) | 取用量層目前為預覽狀態，且不是所有地區都能使用。 如需完整的定價詳細資料，請參閱 [API 管理定價頁面](https://azure.microsoft.com/pricing/details/api-management/) |

1. 選擇 [建立]  以建立 API 管理執行個體，這可能需要幾分鐘的時間。

1. 選取 [啟用 Application Insights]  以將記錄傳送至與函式應用程式相同的位置，然後接受其餘的預設值，並選取 [連結 API]  。

1. [匯入 Azure Functions]  隨即開啟，並醒目提示 **TurbineRepair** 函式。 選擇 [選取]  以繼續操作。

    ![將 Azure Functions 匯入 API 管理中](media/functions-openapi-definition/import-function-openapi.png)

1. 在 [從函式應用程式建立]  頁面上接受預設值，然後選取 [建立] 

    ![從函式應用程式建立](media/functions-openapi-definition/create-function-openapi.png)

現在已建立函式的 API。

## <a name="test-the-api"></a>測試 API

使用 OpenAPI 定義之前，應先確認 API 能夠運作。

1. 在函式的 [測試]  索引標籤中，選取 [POST]  作業。

1. 輸入 [時數]  和 [容量]  的值

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. 按一下 [傳送]  ，然後檢視 HTTP 回應。

    ![測試函式 API](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>下載 OpenAPI 定義

如果您的 API 可以正常運作，就可以下載 OpenAPI 定義。

1. 選取頁面頂端的 [下載 OpenAPI 定義]  。
   
   ![下載 OpenAPI 定義](media/functions-openapi-definition/download-definition.png)

2. 開啟下載的 JSON 檔案並檢閱定義。

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

您已使用 API 管理整合產生函式的 OpenAPI 定義。 現在可以在入口網站的 API 管理中編輯定義。 您也可以[深入了解 API 管理](../api-management/api-management-key-concepts.md)。

> [!div class="nextstepaction"]
> [在 API 管理中編輯 OpenAPI 定義](../api-management/edit-api.md)
