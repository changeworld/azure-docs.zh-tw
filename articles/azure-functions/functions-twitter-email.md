---
title: 建立與 Azure Logic Apps 整合的函式
description: 建立可整合 Azure Logic Apps 和 Azure 認知服務的函數，以將推文情感進行分類，並在偵測到不佳的情感時傳送通知。
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/27/2020
ms.author: cshoe
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: a7cdeb7bfde7396026b782382b34228c309b37d7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088138"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>建立與 Azure Logic Apps 整合的函式

Azure Functions 與 Logic Apps 設計工具中的 Azure Logic Apps 進行整合。 這項整合可讓您搭配其他 Azure 和第三方服務，使用協調流程中的 Functions 計算能力。 

本教學課程說明如何使用 Azure Functions 搭配 Azure 上的 Logic Apps 和認知服務，從 Twitter 貼文執行情感分析。 HTTP 觸發程序函式會以情感分數作為基礎，將推文分類為綠色、黃色或紅色。 偵測到不佳的情感時，會傳送一封電子郵件。 

![此映像顯示邏輯應用程式設計工具中應用程式的前兩個步驟](media/functions-twitter-email/00-logic-app-overview.png)

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立認知服務 API 資源。
> * 建立可將推文情感進行分類的函式。
> * 建立連線至 Twitter 的邏輯應用程式。
> * 將情感偵測新增至邏輯應用程式。 
> * 將邏輯應用程式連線至函式。
> * 以函式的回應作為基礎來傳送電子郵件。

## <a name="prerequisites"></a>Prerequisites

+ 使用中的 [Twitter](https://twitter.com/) 帳戶。 
+ [Outlook.com](https://outlook.com/) 帳戶 (用於傳送通知)。

> [!NOTE]
> 如果您想要使用 Gmail 連接器，只有 G-Suite 的商務帳戶可以在邏輯應用程式中使用此連接器，而不受限制。 如果您有 Gmail 取用者帳戶，您只能使用 Gmail 連接器搭配特定的 Google 核准應用程式及服務，或者您可以[建立 Google 用戶端應用程式，以用來在 Gmail 連接器中進行驗證](/connectors/gmail/#authentication-and-bring-your-own-application)。 如需詳細資訊，請參閱 [Azure Logic Apps 中 Google 連接器的資料安全性和隱私權原則](../connectors/connectors-google-data-security-privacy-policy.md)。

+ 本文使用[從 Azure 入口網站建立您的第一個函式](functions-create-first-azure-function.md)中所建立的資源作為起點。
如果您尚未這麼做，請立即完成這些步驟，才能建立函式應用程式。

## <a name="create-a-cognitive-services-resource"></a>建立認知服務資源

可在 Azure 中使用認知服務 API 作為個別資源。 使用文字分析 API 來偵測受監視的人氣推文。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 按一下 Azure 入口網站左上角的 [建立資源]。

3. 按一下 [AI + 機器學習服務] > [文字分析]。 然後，使用資料表中指定的設定來建立資源。

    ![建立 [識別資源] 頁面](media/functions-twitter-email/01-create-text-analytics.png)

    | 設定      |  建議的值   | 描述                                        |
    | --- | --- | --- |
    | **名稱** | MyCognitiveServicesAccnt | 請選擇唯一的帳戶名稱。 |
    | **位置** | 美國西部 | 使用距離您最近的位置。 |
    | **定價層** | F0 | 從最低層開始。 如果您用完呼叫，請調整為較高層。|
    | **資源群組** | myResourceGroup | 本教學課程中所有的服務，都是使用相同的資源群組。|

4. 按一下 [建立] 以建立資源。 

5. 按一下 [概觀]，然後將 [端點] 的值複製到文字編輯器。 在建立認知服務 API 的連線時，會使用此值。

    ![認知服務設定](media/functions-twitter-email/02-cognitive-services.png)

6. 在左側瀏覽欄位中按一下 [金鑰]，然後複製**金鑰 1** 的值，並放置在文字編輯器中。 您可以使用此金鑰將邏輯應用程式連線至認知服務 API。 
 
    ![認知服務金鑰](media/functions-twitter-email/03-cognitive-serviecs-keys.png)

## <a name="create-the-function-app"></a>建立函式應用程式

Azure Functions 提供的絕佳方法，可讓您將 Logic Apps 工作流程中的處理工作卸載。 本教學課程會使用 HTTP 觸發程序函式來處理認知服務的推文情感分數，並傳回類別值。  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-trigger-function"></a>建立 HTTP 觸發程序函式  

1. 從 [函式] 視窗的左側功能表選取 [函式]，然後從頂端功能表選取 [新增]。

2. 從 [新增函式] 視窗中，選取 [HTTP 觸發程式]。

    ![選擇 HTTP 觸發程序函式](./media/functions-twitter-email/06-function-http-trigger.png)

3. 從 [新增函式] 頁面上，選取 [建立函式]。

4. 在新的 HTTP 觸發程序函式中，從左側功能表中選取 [程式碼 + 測試]，並將 `run.csx` 檔案的內容取代為下列程式碼，然後選取 [儲存]：

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        string category = "GREEN";
    
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        log.LogInformation(string.Format("The sentiment score received is '{0}'.", requestBody));
    
        double score = Convert.ToDouble(requestBody);
    
        if(score < .3)
        {
            category = "RED";
        }
        else if (score < .6) 
        {
            category = "YELLOW";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(category)
            : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    }
    ```

    此函式程式碼會以要求中所收到的情感分數作為基礎，將色彩類別傳回。 

5. 若要測試函式，請從頂端功能表中選取 [測試]。 在 [輸入] 索引標籤的 [主體] 中輸入 `0.2` 的值，然後選取 [執行]。 [輸出] 索引標籤上的 [HTTP 回應內容] 中會傳回 **RED** 值。 

    :::image type="content" source="./media/functions-twitter-email/07-function-test.png" alt-text="定義 Proxy 設定":::

現在您的函式可將情感分數進行分類。 接下來，您可以建立邏輯應用程式，將您的函式與 Twitter 和認知服務 API 進行。 

## <a name="create-a-logic-app"></a>建立邏輯應用程式   

1. 在 Azure 入口網站中，按一下其左上角的 [建立資源] 按鈕。

2. 按一下 [Web] > [邏輯應用程式]。
 
3. 然後，輸入 `TweetSentiment` 之類的 [名稱] 值，並使用表格中指定的設定。

    ![在 Azure 入口網站中建立邏輯應用程式](./media/functions-twitter-email/08-logic-app-create.png)

    | 設定      |  建議的值   | 描述                                        |
    | ----------------- | ------------ | ------------- |
    | **名稱** | TweetSentiment | 為您的應用程式選擇適當名稱。 |
    | **資源群組** | myResourceGroup | 選擇與之前相同的現有資源群組。 |
    | **位置** | 美國東部 | 選擇接近您的位置。 |    

4. 在輸入正確的設定值後，按一下 [建立] 以建立邏輯應用程式。 

5. 建立應用程式之後，按一下釘選到儀表板的新邏輯應用程式。 接著，在 Logic Apps 設計工具中，向下捲動並按一下 [空白的邏輯應用程式] 範本。 

    ![空白的 Logic Apps 範本](media/functions-twitter-email/09-logic-app-create-blank.png)

您現在可以使用 Logic Apps 設計工具，將服務和觸發程序新增至您的應用程式。

## <a name="connect-to-twitter"></a>連接到 Twitter

首先，建立您 Twitter 帳戶的連線。 邏輯應用程式會輪詢推文，從而觸發應用程式加以執行。

1. 在設計工具中，按一下 [Twitter] 服務，然後按一下 [張貼新推文時] 觸發程序。 登入您的 Twitter 帳戶，並授權 Logic Apps 以使用您的帳戶。

2. 使用表格中指定的 Twitter 觸發程序設定。 

    ![Twitter 連接器設定](media/functions-twitter-email/10-tweet-settings.png)

    | 設定      |  建議的值   | 描述                                        |
    | ----------------- | ------------ | ------------- |
    | **搜尋文字** | #Azure | 使用熱門程度足夠在指定間隔內產生新推文的雜湊標記。 當您是使用免費層且您的雜湊標記太熱門時，可能很快就會將認知服務 API 中的交易配額用完。 |
    | **間隔** | 15 | Twitter 要求之間所經過的時間 (以頻率為單位)。 |
    | **頻率** | Minute | 用於輪詢 Twitter 的頻率單位。  |

3.  按一下 [儲存] 可連線到您的 Twitter 帳戶。 

現在，您的應用程式已連線到 Twitter。 接下來，您要連線至文字分析來偵測收集推文的情感。

## <a name="add-sentiment-detection"></a>新增情感偵測

1. 依序按一下 [新增步驟]、[新增動作]。

2. 在 [選擇動作] 中，輸入 [文字分析]，然後按一下 [偵測情感] 動作。
    
    ![選取 [新增步驟]，然後選取 [新增動作]](media/functions-twitter-email/11-detect-sentiment.png)

3. 輸入連線名稱 (例如 `MyCognitiveServicesConnection`)，貼上您存放在文字編輯器中的認知服務 API 金鑰和認知服務端點，然後按一下 [建立]。

    ![選取 [新增步驟]，然後選取 [新增動作]](media/functions-twitter-email/12-connection-settings.png)

4. 接著，在文字方塊中輸入**推文文字**，然後按一下 [新增步驟]。

    ![定義要分析的文字](media/functions-twitter-email/13-analyze-tweet-text.png)

現在您已設定情感偵測，可將連線新增至使用情感分數輸出的函式。

## <a name="connect-sentiment-output-to-your-function"></a>將情感輸出連線至您的函式

1. 在 Logic Apps 設計工具中，按一下 [新增步驟] > [新增動作]、篩選 **Azure Functions**，然後按一下 [選擇 Azure 函式]。

    ![偵測情感](media/functions-twitter-email/14-azure-functions.png)
  
4. 選取您先前建立的函式應用程式。

    ![選取函式](media/functions-twitter-email/15-select-function.png)

5. 選取您為本教學課程建立的函式。

    ![選取函式](media/functions-twitter-email/16-select-function.png)

4. 在**要求本文**中，依序按一下 [分數] 和 [儲存]。

    ![Score](media/functions-twitter-email/17-function-input-score.png)

現在，當邏輯應用程式傳送出情感分數時，就會觸發您的函式。 函式會將以色彩標示的類別傳回至邏輯應用程式。 接下來，您要新增當函式傳回 **RED** 情感值時要傳送的電子郵件通知。 

## <a name="add-email-notifications"></a>新增電子郵件通知

工作流程的最後一個部分是當情感計分為 _RED_ 時要觸發電子郵件。 本文使用 Outlook.com 連接器。 您可以執行類似的步驟，來使用 Gmail 或 Office 365 Outlook 連接器。   

1. 在 Logic Apps 設計工具中，按一下 [新增步驟] > [新增條件]。 

    ![將條件新增至邏輯應用程式。](media/functions-twitter-email/18-add-condition.png)

2. 按一下 [選擇值]，然後按一下 [本文]。 選取 [等於]、按一下 [選擇值] 並輸入 `RED`，然後按一下 [儲存]。 

    ![選擇條件的動作。](media/functions-twitter-email/19-condition-settings.png)    

3. 在 [若為是] 中、按一下 [新增動作]、搜尋 `outlook.com`、按一下 [傳送電子郵件]，然後登入您的 Outlook.com 帳戶。

    ![設定傳送電子郵件動作的電子郵件。](media/functions-twitter-email/20-add-outlook.png)

    > [!NOTE]
    > 如果您沒有 Outlook.com 帳戶，可以選擇另一個連接器，例如 Gmail 或 Office 365 Outlook

4. 在**傳送電子郵件**動作，使用資料表中指定的電子郵件設定。 

    ![設定傳送電子郵件動作的電子郵件。](media/functions-twitter-email/21-configure-email.png)
    
| 設定      |  建議的值   | 描述  |
| ----------------- | ------------ | ------------- |
| **若要** | 輸入您的電子郵件地址 | 接收通知電子郵件地址。 |
| **主旨** | 偵測到負面的推文情感  | 電子郵件通知的主旨列。  |
| **本文** | 推文文字、位置 | 按一下 [推文文字] 和 [位置] 參數。 |

1. 按一下 [檔案] 。

現在，工作流程已完成，您可以啟用邏輯應用程式，並查看工作的函式。

## <a name="test-the-workflow"></a>測試工作流程

1. 在邏輯應用程式設計工具中，按一下 [執行] 可啟動應用程式。

2. 在左欄中，按一下 [概觀] 可查看邏輯應用程式的狀態。 
 
    ![邏輯應用程式執行狀態](media/functions-twitter-email/22-execution-history.png)

3. (選擇性) 按下其中一個執行，可查看執行的詳細資料。

4. 請移至您的函式、檢視記錄，並確認已收到情感值並已加以處理。
 
    ![檢視函式記錄](media/functions-twitter-email/sent.png)

5. 偵測到潛在的負面情感時，您會收到一封電子郵件。 如果您尚未收到電子郵件，可以將函式程式碼變更為每次都傳回 RED︰

    ```csharp
    return (ActionResult)new OkObjectResult("RED");
    ```

    確認電子郵件通知之後，請變更回原始的程式碼︰

    ```csharp
    return requestBody != null
        ? (ActionResult)new OkObjectResult(category)
        : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    ```

    > [!IMPORTANT]
    > 完成本教學課程之後，您應停用邏輯應用程式。 您可以透過停用應用程式，在執行及用完認知服務 API 中的交易時可避免支付費用。

現在您已經知道要將 Functions 整合至 Logic Apps 工作流程有多麼輕鬆。

## <a name="disable-the-logic-app"></a>停用邏輯應用程式

若要停用邏輯應用程式，請依序按一下螢幕頂端的 [概觀] 及 [停用]。 停用應用程式後，無須刪除應用程式，即可使其停止執行及產生費用。

![函式記錄](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立認知服務 API 資源。
> * 建立可將推文情感進行分類的函式。
> * 建立連線至 Twitter 的邏輯應用程式。
> * 將情感偵測新增至邏輯應用程式。 
> * 將邏輯應用程式連線至函式。
> * 以函式的回應作為基礎來傳送電子郵件。

前進至下一個教學課程，了解如何針對函式建立無伺服器 API。

> [!div class="nextstepaction"] 
> [使用 Azure Functions 建立無伺服器 API](functions-create-serverless-api.md)

若要深入了解 Logic Apps，請參閱 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)。
