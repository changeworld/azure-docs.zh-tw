---
title: 教學課程 - 匯入和管理 API - Azure API 管理和 Visual Studio Code | Microsoft Docs
description: 在本教學課程中，您將了解如何使用適用於 Visual Studio Code 的 Azure API 管理延伸模組來匯入、測試及管理 API。
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 6cf5c6f716912689b39264ed71f6a7c55f944ad2
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97410023"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>教學課程：使用適用於 Visual Studio Code 的 API 管理延伸模組來匯入和管理 API

在本教學課程中，您將了解如何使用適用於 Visual Studio Code 的 API 管理延伸模組預覽，在 API 管理中進行一般作業。 使用熟悉的 Visual Studio Code 環境來匯入、更新、測試及管理 API。

您會了解如何：

> [!div class="checklist"]
> * 將 API 匯入至 API 管理
> * 編輯 API
> * 套用 API 管理原則
> * 測試 API


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API 管理延伸模組中的 API":::

如需其他 API 管理功能的簡介，請使用 [Azure 入口網站](import-and-publish.md)參閱 API 管理教學課程。

## <a name="prerequisites"></a>Prerequisites
- 了解 [Azure API 管理術語](api-management-terminology.md)
- 請確定您已安裝 [Visual Studio Code](https://code.visualstudio.com/) 以及[適用於 Visual Studio Code 的最新 Azure API 管理延伸模組 (預覽)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)
- [建立 API 管理執行個體](vscode-create-service-instance.md)

## <a name="import-an-api"></a>匯入 API

下列範例會將 JSON 格式的 OpenAPI 規格匯入至 API 管理。 Microsoft 會提供在此範例中使用的後端 API，並將其裝載在 Azure 上 (`https://conferenceapi.azurewebsites.net?format=json`)。

1. 在 Visual Studio Code 活動列上，選取 Azure 圖示。
1. 在 Explorer 窗格中展開您建立的 API 管理執行個體。
1. 以滑鼠右鍵按一下 [API]，然後選取 [從 OpenAPI 連線匯入]。 
1. 當系統提示時，輸入下列值：
    1. JSON 格式的 **OpenAPI 連結** 內容。 針對此範例： https://conferenceapi.azurewebsites.net?format=json。
    此 URL 是用來實作範例 API 的服務。 API 管理則將要求轉送至此位址。
    1. API 管理執行個體中唯一的 **API 名稱**，例如 demo-conference-api。 名稱只能包含字母、數字和連字號。 第一個字元和最後一個字元必須是字母或數字。 呼叫 API 的路徑會使用此名稱。

成功匯入 API 之後，會出現在 Explorer 窗格中，而可用的 API 作業會出現在 **作業** 節點底下。

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="在 Explorer 窗格中的已匯入 API":::

## <a name="edit-the-api"></a>編輯 API

您可以在 Visual Studio Code 中編輯 API。 例如，在編輯器視窗中編輯 API 的 Resource Manager JSON 描述，以移除用來存取 API 的 **HTTP** 通訊協定。 接著選取 [檔案] > [儲存]

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="編輯 JSON 描述":::

若要編輯 OpenAPI 格式，請在瀏覽器窗格中的 API 名稱上按一下滑鼠右鍵，然後選取 [編輯 OpenAPI]。 進行所有變更，然後選取 [檔案] > [儲存]。

## <a name="apply-policies-to-the-api"></a>將原則新增至 API 

API 管理提供可為 API 設定的[原則](api-management-policies.md)。 原則是陳述式的集合，會因 API 的要求或回應循序執行。 原則可以是全域的，適用於 API 管理執行個體中的所有 API，或可設定為特定 API 或 API 作業的範圍。

本節說明如何將一些常見的輸出原則套用至您的 API，以轉換 API 回應。 此範例中的原則會變更回應標頭，並隱藏顯示在回應主體中的原始後端 URL。

1. 在 Explorer 窗格中，選取您匯入的 demo-conference-api 底下的 [原則]。 原則檔案隨即在編輯器視窗中開啟。 此檔案會針對 API 中的所有作業設定原則。 

1. 以 `<outbound>` 元素中的以下內容更新檔案：
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * 第一個 `set-header` 原則會新增自訂回應標頭以供示範之用。
    * 第二個 `set-header` 原則會刪除 **X-Powered-By** 標頭 (如果有)。 此標頭可以顯示 API 後端中使用的應用程式架構，而發行者通常會加以移除。
    * `redirect-content-urls` 原則會重寫 (遮罩) 回應本文中的連結，使其經由 API 管理閘道指向同等的連結。
    
1. 儲存檔案。 如果出現提示，請選取 [上傳] 將檔案上傳到雲端。

## <a name="test-the-api"></a>測試 API

### <a name="get-the-subscription-key"></a>取得訂用帳戶金鑰

若要測試已匯入的匯入 API 和已套用的原則，您需要 API 管理執行個體的訂用帳戶金鑰。

1. 在 Explorer 窗格中，以滑鼠右鍵按一下您的 API 管理執行個體名稱。
1. 選取 [複製訂用帳戶金鑰]。

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="複製訂用帳戶金鑰":::

### <a name="test-an-api-operation"></a>測試 API 作業

1. 在 Explorer 窗格中，展開您匯入的 demo-conference-api 下的 **作業**。
1. 選取 GetSpeakers 之類的作業。
1. 在編輯器視窗的 **Ocp-Apim-Subscription-Key** 旁，將 `{{SubscriptionKey}}` 取代為您複製的訂用帳戶金鑰。
1. 選取 [傳送要求]  。 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="從 Visual Studio Code 傳送 API 要求":::

要求成功時，後端會回應 **200 OK** 與部分資料。

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="API 測試作業":::

在回應中，請注意下列詳細資料：
* **自訂** 標頭會新增至回應。
* **X-Powered-By** 標頭不會出現在回應中。
* API 後端的 URL 會重新導向至 API 管理閘道，在此案例中為 `https://apim-hello-world.azure-api.net/demo-conference-api`。

### <a name="trace-the-api-operation"></a>追蹤 API 作業

如需可協助您對 API 作業進行偵錯工具的詳細追蹤資訊，請選取 **Ocp-APIM-Trace-Location** 旁出現的連結。 

該位置的 JSON 檔案包含輸入、後端和輸出追蹤資訊，因此您可以判斷在提出要求之後發生任何問題的位置。

> [!TIP]
> 測試 API 作業時，API 管理延伸模組允許選擇性的 [原則偵錯](api-management-debug-policies.md) (可在開發人員服務層級中使用)。

## <a name="clean-up-resources"></a>清除資源

如果不再需要，請移除 API 管理執行個體，方法是以滑鼠右鍵按一下，然後選取 [在入口網站中開啟] 以 [[刪除 API 管理服務](get-started-create-service-instance.md#clean-up-resources)] 及其資源群組。

或者，您也可以選取 [刪除 API 管理]，從而只刪除 API 管理執行個體 (此作業不會刪除其資源群組)。

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="從 VS Code 刪除 API 管理執行個體":::

## <a name="next-steps"></a>後續步驟

本教學課程介紹了適用於 Visual Studio Code 的 API 管理延伸模組的幾項功能，可供您用來匯入及管理 API。 您已了解如何︰

> [!div class="checklist"]
> * 將 API 匯入至 API 管理
> * 編輯 API
> * 套用 API 管理原則
> * 測試 API

API 管理延伸模組提供其他功能，可與您的 API 搭配使用。 例如[偵錯原則](api-management-debug-policies.md) (可在開發人員服務層級中使用) 或建立和管理[具名值](api-management-howto-properties.md)。