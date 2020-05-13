---
title: 在 Azure 中建立由佇列訊息所觸發的函式
description: 使用 Azure Functions 來建立無伺服器函式，此函式是由提交至 Azure 中佇列的訊息所叫用。
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: c4c20579f2306b61741f3c6ab1549285271435a3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123170"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>建立 Azure 佇列儲存體所觸發的函式

了解如何建立函式，並讓此函式在訊息提交至 Azure 儲存體佇列時觸發。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-function-app"></a>建立 Azure 函數應用程式

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-create-success.png" alt-text="已成功建立函數應用程式。。" border="true":::

接下來，您要在新的函式應用程式中建立函式。

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>建立由佇列觸發的函式

1. 選取 [**函數**]，然後選取 [ **+ 新增**] 以加入新的函式。

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-app-quickstart-choose-template.png" alt-text="選擇 Azure 入口網站中的函式範本。" border="true":::

1. 選擇 [ **Azure 佇列儲存體] 觸發**程式範本。

1. 使用影像下方資料表中所指定的設定。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png" alt-text="命名並設定佇列儲存體觸發的函式。" border="true":::


    | 設定 | 建議的值 | 描述 |
    |---|---|---|
    | **名稱** | 函式應用程式中的唯一名稱 | 這個由佇列所觸發之函式的名稱。 |
    | **佇列名稱**   | myqueue-items    | 儲存體帳戶中的連線目標佇列名稱。 |
    | **儲存體帳戶連線** | AzureWebJobsStorage | 您可以使用應用程式函式已在使用的儲存體帳戶連線，或建立新的連線。  |    

1. 選取 [**建立函數**] 來建立您的函式。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-3.png" alt-text="建立佇列儲存體觸發的函式。" border="true":::

接下來，連接到您的 Azure 儲存體帳戶，並建立**myqueue 專案**儲存體佇列。

## <a name="create-the-queue"></a>建立佇列

1. 在您的函式中，于 [**總覽**] 頁面上選取您的資源群組。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-resource-group.png" alt-text="選取您的 Azure 入口網站資源群組。" border="true":::

1. 尋找並選取您的資源群組的儲存體帳戶。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-account-access.png" alt-text="存取儲存體帳戶。" border="true":::

1. 選擇 [**佇列**]，然後選擇 [ **+ 佇列**]。 

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-add-queue.png" alt-text="在 Azure 入口網站中，將佇列新增至您的儲存體帳戶。" border="true":::

1. 在 [**名稱**] 欄位中，輸入 `myqueue-items` ，然後選取 [**建立**]。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-name-queue.png" alt-text="為佇列儲存體容器命名。" border="true":::

您已擁有儲存體佇列，接下來您可以在佇列中新增訊息以測試函式。

## <a name="test-the-function"></a>測試函式

1. 回到 Azure 入口網站，瀏覽至您的函式，展開頁面底部的 [記錄]****，並確定記錄串流並未暫停。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-queue-storage-log-expander.png" alt-text="展開 Azure 入口網站中的記錄檔。" border="true":::

1. 在另一個瀏覽器視窗中，移至 [Azure 入口網站中的資源群組，然後選取 [儲存體帳戶]。

1. 選取 [**佇列**]，然後選取 [ **myqueue-items** ] 容器。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue.png" alt-text="在 Azure 入口網站中，移至您的 myqueue 專案佇列。" border="true":::

1. 選取 [**新增訊息**]，然後輸入 "Hello World！" 在**郵件內文**中。 選取 [確定]  。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue-test.png" alt-text="在 Azure 入口網站中，移至您的 myqueue 專案佇列。" border="true":::

1. 等候幾秒鐘，然後回到您的函式記錄，並確認系統已從佇列中讀取新訊息。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png" alt-text="檢視記錄中的訊息。" border="true":::

1. 回到儲存體佇列，選取 [重新整理 **]，並**確認訊息已處理，而且已不在佇列中。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>後續步驟

您已建立了函式，並讓它在儲存體佇列中有訊息新增時執行。 如需佇列儲存體觸發程序的詳細資訊，請參閱 [Azure Functions 儲存體佇列繫結](functions-bindings-storage-queue.md)。

既然您已建立第一個函式，讓我們將輸出繫結新增至函式，以將訊息寫回另一個佇列。

> [!div class="nextstepaction"]
> [使用 Functions 在 Azure 儲存體佇列中新增訊息](functions-integrate-storage-queue-output-binding.md)
