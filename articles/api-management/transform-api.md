---
title: 教學課程 - 在 Azure API 管理中轉換及保護您的 API | Microsoft Docs
description: 在本教學課程中，您將了解如何使用轉換和節流 (速率限制) 原則來保護 API 管理中的 API。
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/28/2020
ms.author: apimpm
ms.openlocfilehash: 979bdaa1e0dac4f45a321abda2a208f46983f9cd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010226"
---
# <a name="tutorial-transform-and-protect-your-api"></a>教學課程：轉換及保護您的 API

本教學課程示範如何轉換您的 API，使其不會揭露私人後端的資訊。 例如，您可以隱藏在後端執行技術堆疊的相關資訊。 您也可以隱藏 API HTTP 回應主體中出現的原始 URL，改為將其重新導向至 APIM 閘道。

本教學課程示範透過使用「Azure API 管理」設定速率限制，來為後端 API 新增保護是多麼簡單容易的工作。 例如，您可能會想要限制 API 呼叫的速率，讓開發人員不會過度使用 API。 如需詳細資訊，請參閱 [API 管理原則](api-management-policies.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> -   轉換 API 以刪除回應標頭
> -   使用 APIM 閘道 URL 取代 API 回應主體中的原始 URL
> -   新增速率限制原則 (節流) 來保護 API
> -   測試轉換

:::image type="content" source="media/transform-api/api-management-management-console.png" alt-text="入口網站中的原則":::

## <a name="prerequisites"></a>必要條件

-   了解 [Azure API 管理術語](api-management-terminology.md)。
-   了解 [Azure API 管理的原則概念](api-management-howto-policies.md)。
-   完成下列快速入門：[建立 Azure APIM 執行個體](get-started-create-service-instance.md)。
-   此外，請完成下列教學課程：[匯入和發佈您的第一個 API](import-and-publish.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>轉換 API 以刪除回應標頭

本節示範如何隱藏您不想向使用者顯示的 HTTP 標頭。 此範例示範如何在 HTTP 回應中刪除下列標頭：

-   **X-Powered-By**
-   **X-AspNet-Version**

### <a name="test-the-original-response"></a>測試原始回應

查看原始回應：

1. 在您的 API 管理服務執行個體中，選取 [API]。
1. 選取 API 清單中的 [Demo Conference API]。
1. 選取畫面頂端的 [測試] 索引標籤。
1. 選取 **GetSpeakers** 作業，然後選取 [傳送]。

原始回應看起來應該如下所示：

:::image type="content" source="media/transform-api/original-response.png" alt-text="原始 API 回應":::

如您所見，回應會包含 **X-AspNet-Version** 和 **X-Powered-By** 標頭。

### <a name="set-the-transformation-policy"></a>設定轉換原則

1. 選取 [Demo Conference API]  >  [設計]  >  [所有作業]。
4. 在 [輸出處理] 區段中，選取程式碼編輯器 ( **</>** ) 圖示。

   :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Outbound.png" alt-text="瀏覽至輸出原則" border="false":::

1. 將游標放在 **&lt;outbound&gt;** 元素中，然後選取右上角的 [顯示程式碼片段]。
1. 在右側視窗的 [轉換原則] 下方，選取兩次 [設定 HTTP 標頭] (以插入兩個原則程式碼片段)。

   :::image type="content" source="media/transform-api/transform-api.png" alt-text="設定 HTTP 標頭原則":::

1. 修改您的 **\<outbound>** 程式碼，使它看起來如下：

   ```
   <set-header name="X-Powered-By" exists-action="delete" />
   <set-header name="X-AspNet-Version" exists-action="delete" />
   ```

   :::image type="content" source="media/transform-api/set-policy.png" alt-text="設定 HTTP 標頭":::

1. 選取 [儲存]。

## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>使用 APIM 閘道 URL 取代 API 回應主體中的原始 URL

本節示範如何隱藏 API HTTP 回應主體中出現的原始 URL，改為將其重新導向至 APIM 閘道。

### <a name="test-the-original-response"></a>測試原始回應

查看原始回應：

1. 選取 [Demo Conference API]  >  [測試]。
1. 選取 **GetSpeakers** 作業，然後選取 [傳送]。

    如您所見，回應會包含原始後端 URL：

    :::image type="content" source="media/transform-api/original-response2.png" alt-text="回應中的原始 URL":::


### <a name="set-the-transformation-policy"></a>設定轉換原則

1.  選取 [Demo Conference API]  >  [所有作業]  >  [設計]。
1.  在 [輸出處理] 區段中，選取程式碼編輯器 ( **</>** ) 圖示。
1.  將游標放在 **&lt;outbound&gt;** 元素中，然後選取右上角的 [顯示程式碼片段]。
1.  在右側視窗的 [轉換原則] 底下，選取 [遮罩內容中的 URL]。 
1.  選取 [儲存]。

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>新增速率限制原則 (節流) 來保護 API

本節示範如何透過設定速率限制來為後端 API 新增保護。 例如，您可能會想要限制 API 呼叫的速率，讓開發人員不會過度使用 API。 在此範例中，會針對每個訂用帳戶識別碼，將限制設定為每 15 秒呼叫 3 次。 15 秒之後，開發人員就可重試呼叫 API。

1.  選取 [Demo Conference API]  >  [所有作業]  >  [設計]。
1.  在 [輸入處理] 區段中，選取程式碼編輯器 ( **</>** ) 圖示。
1.  將游標放在 **&lt;inbound&gt;** 元素中，然後選取右上角的 [顯示程式碼片段]。

    :::image type="content" source="media/transform-api/04-ProtectYourAPI-01-SetPolicy-Inbound.png" alt-text="設定輸入原則" border="false":::

1.  在右側視窗的 [存取限制原則] 下方，選取 [+ 限制每個金鑰的呼叫速率]。
1.  將您的 **rate-limit-by-key** 程式碼 (位於 **\<inbound\>** 元素中) 修改為下列程式碼：

    ```
    <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
    ```

## <a name="test-the-transformations"></a>測試轉換

此時如果您在程式碼編輯器中查看程式碼，您的原則會顯示如下：

   ```
   <policies>
      <inbound>
        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
        <base />
      </inbound>
      <backend>
        <base />
      </backend>
      <outbound>
        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
        <redirect-content-urls />
        <base />
      </outbound>
      <on-error>
        <base />
      </on-error>
   </policies>
   ```

本節的其餘部分將測試您在此文章中設定的原則轉換。

### <a name="test-the-stripped-response-headers"></a>測試已刪除的回應標頭

1. 選取 [Demo Conference API]  >  [測試]。
1. 選取 **GetSpeakers** 作業，然後選取 [傳送]。

    此時，您可以看到已刪除標頭：

    :::image type="content" source="media/transform-api/final-response1.png" alt-text="已刪除的回應標頭":::

### <a name="test-the-replaced-url"></a>測試已取代的 URL

1. 選取 [Demo Conference API]  >  [測試]。
1. 選取 **GetSpeakers** 作業，然後選取 [傳送]。

    此時，您可以看到已取代 URL。

    :::image type="content" source="media/transform-api/final-response2.png" alt-text="取代的 URL":::

### <a name="test-the-rate-limit-throttling"></a>測試速率限制 (節流)

1. 選取 [Demo Conference API]  >  [測試]。
1. 選取 **GetSpeakers** 作業。 連續選取三次 [傳送]。

    傳送要求 3 次之後，您會收到 **429 太多要求** 的回應。

    :::image type="content" source="media/transform-api/test-throttling.png" alt-text="要求太多":::

1. 大約等候 15 秒之後，再選取一次 [傳送]。 此時，您應該得到 **200 確定** 的回應。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
>
> -   轉換 API 以刪除回應標頭
> -   使用 APIM 閘道 URL 取代 API 回應主體中的原始 URL
> -   新增速率限制原則 (節流) 來保護 API
> -   測試轉換

前進到下一個教學課程：

> [!div class="nextstepaction"]
> [監視您的 API](api-management-howto-use-azure-monitor.md)
