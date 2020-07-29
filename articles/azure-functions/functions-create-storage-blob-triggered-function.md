---
title: 在 Azure 中建立由 Blob 儲存體所觸發的函式
description: 使用 Azure Functions 來建立無伺服器函式，並讓此函式由新增至 Blob 儲存體容器的項目來叫用。
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: bf6865d2756579f457dded90b247326d2eec137c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122949"
---
# <a name="create-a-function-in-azure-thats-triggered-by-blob-storage"></a>在 Azure 中建立由 Blob 儲存體所觸發的函式

了解如何建立函式，並讓此函式在檔案上傳至 Blob 儲存體容器時或 Blob 儲存體容器中的檔案更新時觸發。

## <a name="prerequisites"></a>必要條件

+ Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-function-app"></a>建立 Azure 函數應用程式

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

您已成功建立新的函數應用程式。

:::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-create-success.png" alt-text="已成功建立函數應用程式。" border="true":::

接下來，您要在新的函式應用程式中建立函式。

<a name="create-function"></a>

## <a name="create-an-azure-blob-storage-triggered-function"></a>建立由 Azure Blob 儲存體所觸發的函式

1. 選取 [函式]，然後選取 [+ 新增] 以新增函式。

   :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-template.png" alt-text="選擇 Azure 入口網站中的函式範本。" border="true":::

1. 選擇 **Azure Blob 儲存體觸發程序**範本。

1. 使用影像下方資料表中所指定的設定。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png" alt-text="命名及設定由 Blob 儲存體所觸發的函式。" border="true":::

    | 設定 | 建議的值 | 描述 |
    |---|---|---|
    | **新增函式** | 函式應用程式中的唯一名稱 | 這個由 blob 所觸發之函式的名稱。 |
    | **路徑**   | samples-workitems/{name}    | 受監視 Blob 儲存體中的位置。 在繫結中，Blob 的檔案名稱會以「名稱」參數的形式來傳遞。  |
    | **儲存體帳戶連線** | AzureWebJobsStorage | 您可以使用應用程式函式已在使用的儲存體帳戶連線，或建立新的連線。  |

1. 選取 [建立函式] 以建立函式。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-3.png" alt-text="建立由 Blob 儲存體所觸發的函式。" border="true":::

接下來，建立 **samples-workitems** 容器。

## <a name="create-the-container"></a>建立容器

1. 在您的函式中，在 [概觀] 頁面上，選取您的資源群組。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-resource-group.png" alt-text="選取您的 Azure 入口網站資源群組。" border="true":::

1. 尋找並選取您資源群組的儲存體帳戶。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-account-access.png" alt-text="存取儲存體帳戶。" border="true":::

1. 選擇 [容器]，然後選擇 [+ 容器]。 

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-add-container.png" alt-text="新增容器到 Azure 入口網站中的儲存體帳戶。" border="true":::

1. 在 [名稱] 欄位中輸入 `samples-workitems`，然後選取 [建立]。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-name-blob-container.png" alt-text="為儲存體容器命名。" border="true":::

您已擁有 Blob 容器，接下來您可以將檔案上傳至容器以測試函式。

## <a name="test-the-function"></a>測試函式

1. 回到 Azure 入口網站，瀏覽至您的函式，展開頁面底部的 [記錄]，並確定記錄串流並未暫停。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-log-expander.png" alt-text="在 Azure 入口網站中展開記錄。" border="true":::

1. 在另一個瀏覽器視窗中，移至 Azure 入口網站中的資源群組，然後選取儲存體帳戶。

1. 選取 [容器]，然後選取 **samples-workitems** 容器。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-container.png" alt-text="移至 Azure 入口網站中的 samples-workitems 容器。" border="true":::

1. 選取 [上傳]，然後選取資料夾圖示以選擇要上傳的檔案。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png" alt-text="將檔案上傳至 Blob 容器。" border="true":::

1. 瀏覽至本機電腦上的檔案 (例如影像檔)，然後選擇檔案。 選取 [開啟]，然後選取 [上傳]。

1. 返回您的函式記錄，並確認系統已讀取 Blob。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png" alt-text="檢視記錄中的訊息。" border="true":::

    >[!NOTE]
    > 當函式應用程式以預設的取用方案執行時，從有 Blob 新增或更新到系統觸發函式，中間可能會延遲好幾分鐘。 如果您不想讓 Blob 所觸發的函式延遲太久，請考慮在 App Service 方案執行函式應用程式。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>後續步驟

您已建立了函式，並讓它在 Blob 儲存體中有 Blob 新增或更新時執行。 如需 Blob 儲存體觸發程序的詳細資訊，請參閱 [Azure Functions Blob 儲存體繫結](functions-bindings-storage-blob.md)。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
