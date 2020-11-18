---
title: 教學課程 - 在 Azure API 管理中使用要求追蹤對 API 進行偵錯
description: 依照本教學課程的步驟，在 Azure API 管理中啟用追蹤及檢查要求處理步驟。
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: 531e346569b85ababc382f997fd7764a92b3d05f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542309"
---
# <a name="tutorial-debug-your-apis-using-request-tracing"></a>教學課程：使用要求追蹤對 API 進行偵錯

本教學課程說明如何在 Azure API 管理中檢查 (追蹤) 要求處理，以便對您的 API 進行偵錯和疑難排解。 

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 追蹤範例呼叫
> * 檢閱要求處理步驟

:::image type="content" source="media/api-management-howto-api-inspector/api-inspector-001.png" alt-text="API 偵測器":::

## <a name="prerequisites"></a>必要條件

+ 了解 [Azure API 管理術語](api-management-terminology.md)。
+ 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
+ 請完成下列教學課程：[匯入和發佈您的第一個 API](import-and-publish.md)。

## <a name="verify-allow-tracing-setting"></a>確認允許追蹤設定 

對於您的 API 所使用的訂用帳戶，必須啟用 [允許追蹤] 設定。 如果您使用內建的所有存取訂用帳戶，則依預設會啟用此功能。 若要在入口網站中確認，請瀏覽至您的 API 管理執行個體，然後選取 [訂用帳戶]。

   :::image type="content" source="media/api-management-howto-api-inspector/allow-tracing.png" alt-text="允許訂用帳戶的追蹤":::

## <a name="trace-a-call"></a>追蹤呼叫

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至您的 API 管理執行個體。
1. 選取 [API]。
1. 選取 API 清單中的 [Demo Conference API]。
1. 選取 [測試]  索引標籤。
1. 選取 **GetSpeakers** 作業。
1. 確認 HTTP 要求標頭包含 **Ocp-Apim-Trace:True** 和 **Ocp-Apim-Subscription-Key** 的有效值。 若非如此，請選取 [+ 新增標頭] 以新增標頭。
1. 選取 [傳送] 以進行 API 呼叫。

  :::image type="content" source="media/api-management-howto-api-inspector/06-debug-your-apis-01-trace-call.png" alt-text="設定 API 追蹤":::

> [!TIP]
> 如果 **Ocp-Apim-Subscription-Key** 未自動填入 HTTP 要求中，您可以在入口網站中加以擷取。 選取 [訂用帳戶]，然後開啟您訂用帳戶的操作功能表 ( **...** )。 選取 [顯示/隱藏金鑰]。 您也可以視需要重新產生金鑰。 然後，將金鑰新增至標頭。

## <a name="review-trace-information"></a>檢閱追蹤資訊

1. 在呼叫完成後，移至 [HTTP 回應] 中的 [追蹤] 索引標籤。
1. 選取下列任一連結，以跳至詳細的追蹤資訊：**輸入**、**後端**、**輸出**。

     :::image type="content" source="media/api-management-howto-api-inspector/response-trace.png" alt-text="檢閱回應追蹤":::

    * **輸入** - 顯示從呼叫者接收到的原始要求 API 管理，以及套用至要求的原則。 例如，如果您在[教學課程：轉換及保護您的 API](transform-api.md) 中新增了原則，這些原則會顯示在這裡。

    * **後端** - 顯示 API 管理傳送至 API 後端的要求及其接收的回應。

    * **輸出** - 顯示回應在傳回至呼叫者之前套用的所有原則。

    > [!TIP]
    > 每個步驟也會顯示 API 管理收到要求後耗用的時間。

1. 在 [訊息] 索引標籤上，**ocp-apim-trace-location** 標頭會顯示儲存在 Azure Blob 儲存體中的追蹤資料所在的位置。 如有需要，請移至此位置以取得追蹤。

     :::image type="content" source="media/api-management-howto-api-inspector/response-message.png" alt-text="Azure 儲存體中的追蹤位置":::
## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 追蹤範例呼叫
> * 檢閱要求處理步驟

前進到下一個教學課程：

> [!div class="nextstepaction"]
> [使用修訂](api-management-get-started-revise-api.md)
