---
title: 教學課程 - 在 .NET 中使用 Azure 儲存體佇列
description: 本教學課程說明如何使用 Azure 佇列服務來建立佇列，以及如何使用 .NET 程式碼插入、取得和刪除訊息。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/09/2020
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: 23684dbbc5cb8c2d5fc4880ae8fe1999450928e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400565"
---
# <a name="tutorial-work-with-azure-storage-queues-in-net"></a>教學課程：在 .NET 中使用 Azure 儲存體佇列

Azure 佇列儲存體可實作雲端式佇列，使分散式應用程式的元件之間可進行通訊。 每個佇列都會維護一份可由傳送端元件新增、且可由接收端元件處理的訊息清單。 透過佇列，您的應用程式將可立即進行調整以因應需求。 本文說明使用 Azure 儲存體佇列的基本步驟。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> - 建立 Azure 儲存體帳戶
> - 建立應用程式
> - 新增 Azure 用戶端程式庫
> - 新增非同步程式碼的支援
> - 建立佇列
> - 將訊息插入佇列中
> - 清除佇列中的訊息
> - 刪除空的佇列
> - 檢查命令列引數
> - 建置並執行應用程式

## <a name="prerequisites"></a>必要條件

- 取得免費的跨平台 [Visual Studio Code](https://code.visualstudio.com/download) 編輯器。
- 下載並安裝 [.NET Core SDK](https://dotnet.microsoft.com/download) 3.1 版或更新版本。
- 如果您還沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-an-azure-storage-account"></a>建立 Azure 儲存體帳戶

首先，請建立 Azure 儲存體帳戶。 如需建立儲存體帳戶的逐步指南，請參閱[建立儲存體帳戶](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json)快速入門。 這是您在必要條件中建立免費的 Azure 帳戶之後所執行的個別步驟。

## <a name="create-the-app"></a>建立應用程式

建立名為 **QueueApp** 的 .NET Core 應用程式。 為了方便說明，此應用程式將可透過佇列傳送和接收訊息。

1. 在主控台視窗中 (例如 CMD、PowerShell 或 Azure CLI)，使用 `dotnet new` 命令建立名為 **QueueApp** 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：**Program.cs**。

   ```console
   dotnet new console -n QueueApp
   ```

2. 切換至新建的 **QueueApp** 資料夾，然後建置應用程式以確認一切都正常運作。

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   您應該會看到類似下面輸出的結果：

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.63 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp3.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-the-azure-client-libraries"></a>新增 Azure 用戶端程式庫

1. 使用 `dotnet add package` 命令，將 Azure 儲存體用戶端程式庫新增至專案。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   從主控台視窗中的專案資料夾執行下列命令。

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   從主控台視窗中的專案資料夾執行下列命令。

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   ```

   ```console
   dotnet add package Microsoft.Azure.Storage.Queue
   ```
   ---

### <a name="add-using-statements"></a>新增 using 陳述式

1. 從專案目錄中的命令列輸入 `code .`，以在目前的目錄中開啟 Visual Studio Code。 將命令列視窗保持開啟。 稍後將會執行其他命令。 如果系統提示您新增建置和偵錯所需的 C# 資產，請按一下 [是]**** 按鈕。

1. 開啟 **Program.cs** 來源檔案，並緊接在 `using System;` 陳述式後面新增下列命名空間。 此應用程式會使用這些命名空間中的類型連線至 Azure 儲存體，並使用佇列。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. 儲存 **Program.cs** 檔案。

## <a name="add-support-for-asynchronous-code"></a>新增非同步程式碼的支援

由於應用程式使用雲端資源，因此程式碼會以非同步方式執行。

1. 更新 **Main** 方法，以非同步方式執行。 將 **void** 取代為**非同步工作**傳回值。

   ```csharp
   static async Task Main(string[] args)
   ```

1. 儲存 **Program.cs** 檔案。

## <a name="create-a-queue"></a>建立佇列

在對 Azure Api 進行任何呼叫之前，您必須先從 Azure 入口網站取得您的認證。

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>將連接字串新增至應用程式

將連接字串新增至應用程式，使其能夠存取儲存體帳戶。

1. 切換回 Visual Studio Code。

1. 在 **Main** 方法中，將 `Console.WriteLine("Hello World!");` 程式碼取代為下列行，以從環境變數取得連接字串。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. 將下列程式碼新增至 **Main** 以建立佇列物件，此物件稍後會傳入 send 和 receive 方法中。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. 儲存檔案。

## <a name="insert-messages-into-the-queue"></a>將訊息插入佇列中

建立將訊息傳送至佇列中的新方法。

1. 將下列 **InsertMessageAsync** 方法新增至您的 **Program** 類別。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   已傳遞佇列參考給這個方法。 藉由呼叫 [CreateIfNotExistsAsync](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync)，建立新的佇列 (如果尚未存在)。 接著，藉由呼叫 [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync)，將 *newMessage* 新增至佇列。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   已傳遞佇列參考給這個方法。 藉由呼叫 [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync)，建立新的佇列 (如果尚未存在)。 接著，藉由呼叫 [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync)，將 *newMessage* 新增至佇列。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. **選擇性** 根據預設，訊息的存留時間上限會設為 7 天。 您可以指定任何正數的訊息存留時間。 下列程式碼片段會新增「永不」** 過期的訊息。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

    若要新增不會過期的訊息，請在 **SendMessageAsync** 的呼叫中使用 `Timespan.FromSeconds(-1)`。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

    若要新增不會過期的訊息，請在 **AddMessageAsync** 的呼叫中使用 `Timespan.FromSeconds(-1)`。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. 儲存檔案。

佇列訊息的格式必須與使用 UTF-8 編碼的 XML 要求相容。 一則訊息的大小可能高達 64 KB。 如果訊息包含二進位資料，則將訊息[編碼為 Base64](/dotnet/api/system.convert.tobase64string)。

## <a name="dequeue-messages"></a>清除佇列中的訊息

建立新方法已從佇列中擷取訊息。 在成功接收訊息後，務必要從佇列中刪除該訊息，以免受到多次處理。

1. 將名為 **RetrieveNextMessageAsync** 的新方法新增至您的 **Program** 類別。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   此方法會藉由呼叫 [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync)，在第一個參數中傳遞 1 僅擷取佇列中的下一則訊息，以接收佇列中的訊息。 收到訊息後，請呼叫 [DeleteMessageAsync](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync) 以從佇列中刪除該訊息。

   當使用 v12 之前的 SDK 版本將訊息傳送至佇列時，會自動以 Base64 編碼。 從 v12 開始，已移除該功能。 使用 v12 SDK 來擷取訊息時，不會自動進行 Base64 解碼。 您必須自行明確地對內容進行 [Base64 解碼](/dotnet/api/system.convert.frombase64string)。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   此方法會藉由呼叫 [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync) 接收來自佇列的訊息。 收到訊息後，請呼叫 [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync) 以從佇列中刪除該訊息。

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. 儲存檔案。

## <a name="delete-an-empty-queue"></a>刪除空的佇列

在專案結束後確認您是否還需要您建立的資源，是最佳做法。 讓資源繼續執行可能會產生費用。 如果佇列存在，但是空的，請詢問使用者是否要加以刪除。

1. 展開 **RetrieveNextMessageAsync** 方法，以納入刪除空佇列的提示。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. 儲存檔案。

## <a name="check-for-command-line-arguments"></a>檢查命令列引數

如果在應用程式中傳入了任何命令列引數，請假設它們是要新增至佇列的訊息。 引數聯結在一起，組成字串。 藉由呼叫我們先前新增的 **InsertMessageAsync** 方法，將此字串新增至訊息佇列。

如果沒有任何命令列引數，請嘗試擷取作業。 呼叫 **RetrieveNextMessageAsync** 方法，以擷取佇列中的下一則訊息。

最後，等到使用者輸入後，再呼叫 **Console.ReadLine** 結束作業。

1. 展開 **Main** 方法以檢查命令列引數，並等候使用者輸入。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. 儲存檔案。

## <a name="complete-code"></a>完整程式碼

以下列出此專案的完整程式碼。

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_AllCode":::
   ---

## <a name="build-and-run-the-app"></a>建置並執行應用程式

1. 從專案目錄中的命令列執行下列 dotnet 命令，以建置專案。

   ```console
   dotnet build
   ```

1. 專案成功建置後，請執行下列命令，將第一個訊息新增至佇列。

   ```console
   dotnet run First queue message
   ```

   您應該會看見此輸出：

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

1. 執行未使用命令列引數的應用程式，以接收和移除佇列中的第一個訊息。

   ```console
   dotnet run
   ```

1. 繼續執行應用程式，直到所有訊息皆移除。 如果您再執行一次，應該會出現佇列空白的訊息，以及刪除佇列的提示。

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

1. 建立佇列
1. 從佇列新增和移除訊息
1. 刪除 Azure 儲存體佇列

如需詳細資訊，請參閱 Azure 佇列快速入門。

> [!div class="nextstepaction"]
> [入口網站的佇列快速入門](storage-quickstart-queues-portal.md)

- [.NET 的佇列快速入門](storage-quickstart-queues-dotnet.md)
- [Java 的佇列快速入門](storage-quickstart-queues-java.md)
- [Python 的佇列快速入門](storage-quickstart-queues-python.md)
- [JavaScript 的佇列快速入門](storage-quickstart-queues-nodejs.md)
