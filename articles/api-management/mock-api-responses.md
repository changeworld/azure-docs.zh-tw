---
title: 教學課程 - 在 API 管理中模擬 API 回應 - Azure 入口網站 | Microsoft Docs
description: 在本教學課程中，您會使用 API 管理來設定 API 的原則，因此如果後端無法傳送真正的回應，會傳回模擬回應。
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 78743c5f045f2544cafe88414ed996d08bacd2a0
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631077"
---
# <a name="tutorial-mock-api-responses"></a>教學課程：模擬 API 回應

後端 API 可以匯入至 API 管理 (APIM) API，或者以手動方式建立及管理。 本教學課程中的步驟示範如何使用 APIM 來建立空白的 API 並手動管理，然後設定 API 的原則，使其傳回模擬回應。 即使無法使用後端傳送實際回應，這個方法還是能讓開發人員繼續實作和測試 APIM 執行個體。 

模擬回應的能力適用於許多案例：

+ 先設計 API 外觀，再於稍後進行後端實作時。 或者，以平行方式開發後端時。
+ 當後端暫時無法運作或無法調整規模時。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立測試 API 
> * 將作業新增到測試 API
> * 啟用回應模擬
> * 測試模擬的 API


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="模擬 API 回應":::

## <a name="prerequisites"></a>必要條件

+ 了解 [Azure API 管理術語](api-management-terminology.md)。
+ 了解 [Azure API 管理的原則概念](api-management-howto-policies.md)。
+ 完成下列快速入門：[建立 Azure APIM 執行個體](get-started-create-service-instance.md)。

## <a name="create-a-test-api"></a>建立測試 API 

本節中的步驟示範如何建立不具後端的空白 API。 


1. 登入 Azure 入口網站，然後瀏覽至您的 API 管理執行個體。
1. 選取 [API]  >  [+ 新增 API]  >  [空白 API]。
1. 在 [建立空白 API] 視窗中，選取 [完整]。
1. 針對 [顯示名稱] 輸入「測試 API」。
1. 針對 [產品] 選取 [無限制]。
1. 請確定已在 [閘道] 中選取 [受控]。
1. 選取 [建立]  。

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="模擬 API 回應":::

## <a name="add-an-operation-to-the-test-api"></a>將作業新增到測試 API

API 會公開一或多個作業。 在本節中，將作業新增至您建立的空白 API。 完成本節中的步驟之後呼叫作業會產生錯誤。 當您完成[啟用回應模擬](#enable-response-mocking)一節中的步驟之後，將不會收到任何錯誤。

1. 選取您在上一個步驟中建立的 API。
1. 選取 [+ 新增作業]****。
1. 在 [前端] 視窗中，輸入下列值。

     | 設定             | 值                             | 描述                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **顯示名稱**    | 測試呼叫                        | 顯示於[開發人員入口網站](api-management-howto-developer-portal.md)中的名稱。                                                                                                                                       |
    | **URL** (HTTP 指令動詞) | GET                               | 選取其中一個預先定義的 HTTP 指令動詞。                                                                                                                                         |
    | **URL**             | */test*                           | API 的 URL 路徑。                                                                                                                                                                       |
    | **說明**     |                                   |  作業的選擇性描述，可用來將文件提供給在開發人員入口網站中使用此 API 的開發人員。                                                    |
    
1. 選取 [回應] 索引標籤 (位於 [URL]、[顯示名稱] 和 [描述] 欄位下方)。 在此索引標籤上輸入設定，以定義回應狀態碼、內容類型、範例及結構描述。
1. 選取 [+ 新增回應]，然後從清單中選取 [200 確定]。
1. 在右側的 [表示法]**** 標題下方，選取 [+ 新增表示法]****。
1. 在搜尋方塊中輸入「application/json」，然後選取 [application/json] 內容類型。
1. 在 [範例]**** 文字方塊中，輸入  `{ "sampleField" : "test" }`。
1. 選取 [儲存]。

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="模擬 API 回應" border="false":::

雖然在此範例中不是必要的，但是也可以在其他索引標籤上設定 API 作業的其他設定，包括：


|索引標籤      |描述  |
|---------|---------|
|**查詢**     |  新增查詢參數。 除了提供名稱和描述，您還能提供指派給查詢參數的值。 其中一個值可標示為預設值 (選擇性)。        |
|**要求**     |  定義要求內容類型、範例及結構描述。       |

## <a name="enable-response-mocking"></a>啟用回應模擬

1. 選取您在[建立測試 API](#create-a-test-api) 中建立的 API。
1. 選取您新增的測試作業。
1. 在右側視窗中，確定已選取 [設計] 索引標籤。
1. 在 [輸入處理] 視窗中，選取 [+ 新增原則]。

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="模擬 API 回應" border="false":::

1. 從資源庫中選取 [模擬回應]。

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="模擬 API 回應" border="false":::

1. 在 [API 管理回應]**** 文字方塊中，輸入 **200 OK, application/json**。 此選項表示您的 API 應該會傳回您在上一節中定義的回應範例。

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="模擬 API 回應":::

1. 選取 [儲存]。

    > [!TIP]
    > 具有 API **已啟用模擬**字樣的黃色列，表示從 API 管理傳回的回應會傳送模擬原則且不是實際的後端回應。

## <a name="test-the-mocked-api"></a>測試模擬的 API

1. 選取您在[建立測試 API](#create-a-test-api) 中建立的 API。
1. 選取 [測試]  索引標籤。
1. 確定已選取 [測試呼叫]**** API。 選取 [傳送]  進行測試呼叫。

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="模擬 API 回應":::

1. **HTTP 回應**會顯示本教學課程第一節中提供來作為範例的 JSON。

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="模擬 API 回應":::

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立測試 API
> * 將作業新增到測試 API
> * 啟用回應模擬
> * 測試模擬的 API

前進到下一個教學課程：

> [!div class="nextstepaction"]
> [轉換及保護已發佈的 API](transform-api.md)
