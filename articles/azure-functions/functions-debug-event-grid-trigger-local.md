---
title: Azure Functions 的事件方格本機偵錯
description: 了解如何在本機偵錯由事件方格事件所觸發的 Azure 函式
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: fdd046a855c4e2114e96e7911a928165f808710c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530539"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Azure Functions 事件方格觸發程序本機偵錯

本文示範如何對可處理儲存體帳戶所引發 Azure 事件方格事件的本機函式進行偵錯。 

## <a name="prerequisites"></a>必要條件

- 建立或使用現有的函式應用程式
- 建立或使用現有的儲存體帳戶
- 下載 [ngrok](https://ngrok.com/)，允許 Azure 呼叫本機函式

## <a name="create-a-new-function"></a>建立新函式

在 Visual Studio 中開啟函式應用程式，並於 [方案總管] 中的專案名稱上按一下滑鼠右鍵，然後按一下 [新增] > [新增 Azure 函式]****。

在 [新增 Azure 函式]** 視窗中，選取 [事件方格觸發程序]**** 並按一下 [確定]****。

![建立新的函式](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

建立函式之後，開啟程式碼檔案，並複製在檔案頂端加上註解的 URL。 設定事件方格觸發程序時會使用此位置。

![複製位置](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

然後，在以 `log.LogInformation` 開頭的這行上設定中斷點。

![設定中斷點](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


接下來，**按 F5** 啟動偵錯工作階段。

## <a name="allow-azure-to-call-your-local-function"></a>允許 Azure 呼叫本機函式

若要進入正在電腦上進行偵錯的函式，您必須讓 Azure 能夠從雲端與您的本機函式通訊。

[ngrok](https://ngrok.com/) 公用程式可讓 Azure 呼叫正在電腦上執行的函式。 使用下列命令啟動 ngrok**：

```bash
ngrok http -host-header=localhost 7071
```
設定此公用程式時，命令視窗應如下列螢幕擷取畫面所示：

![在啟動 "ngrok" 公用程式之後顯示命令提示字元的螢幕擷取畫面。](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

複製在執行 ngrok** 時所產生的 **HTTPS** URL。 在設定事件方格事件端點時會使用此值。

## <a name="add-a-storage-event"></a>新增儲存體事件

開啟 Azure 入口網站並瀏覽至儲存體帳戶，然後按一下 [事件]**** 選項。

![新增儲存體帳戶事件](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

在 [事件]** 視窗中，按一下 [事件訂用帳戶]**** 按鈕。 在 [ *事件訂* 用帳戶] 視窗中，按一下 [ *端點類型* ] 下拉式清單， **然後選取 [** webhook]。

![選取訂用帳戶類型](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

設定端點類型後，按一下 [選取端點]**** 以設定端點值。

![選取端點類型](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

[訂閱者端點]** 值是由三個不同的值所組成。 前置詞是由 ngrok** 所產生的 HTTPS URL。 URL 的其餘部分是來自函式程式碼檔案中找到的 URL，結尾會加上函式名稱。 以函式程式碼檔案作為 URL 的開頭，ngrok** URL 會取代 `http://localhost:7071` 且函式名稱會取代 `{functionname}`。

最後一個 URL 應該如下列螢幕擷取畫面所示：

![端點選取範圍](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

輸入適當的值之後，按一下 [確認選取項目]****。

> [!IMPORTANT]
> 每次您啟動 ngrok** 時，就會重新產生 HTTPS URL 且值會變更。 因此您每次透過 ngrok** 向 Azure 公開函式時，都必須建立新的事件訂用帳戶。

## <a name="upload-a-file"></a>上傳檔案

您現在可以將檔案上傳至儲存體帳戶，以觸發事件方格事件以供本機函式處理。 

開啟[儲存體總管](https://azure.microsoft.com/features/storage-explorer/)並連線到您的儲存體帳戶。 

- 展開 [Blob 容器]****。 
- 以滑鼠右鍵按一下並選取 [建立 Blob 容器]****。
- 將容器命名為 **test**。
- 選取 test** 容器
- 按一下 [ **上傳** ] 按鈕
- 按一下 **[上傳**檔案]
- 選取檔案並將它上傳至 Blob 容器。

## <a name="debug-the-function"></a>進行函式偵錯

當事件方格認為新檔案已上傳至儲存體容器時，則會在本機函式中達到中斷點。

![啟動 ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>清除資源

若要清除本文中所建立的資源，請刪除儲存體帳戶中的 **test** 容器。

## <a name="next-steps"></a>後續步驟

- [使用 Event Grid 自動調整已上傳映像的大小](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Azure Functions 的事件方格觸發程序](./functions-bindings-event-grid.md)
