---
title: 如何使用佇列儲存體 (c + +) -Azure 儲存體
description: 瞭解如何在 Azure 中使用佇列儲存體服務。 範例是以 C++ 撰寫的。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 73d88f69057dc6fe39f6329e89eb72ecebf853f0
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491973"
---
# <a name="how-to-use-queue-storage-from-c"></a>如何使用 C++ 的佇列儲存體

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概觀

本指南將示範如何使用 Azure 佇列儲存體服務執行一般案例。 這些範例均以 C++ 撰寫，並使用 [Azure Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)。 所涵蓋的案例包括「插入」、「查看」、「取得」和「刪除」佇列訊息，以及「建立和刪除佇列」。

> [!NOTE]
> 本指南以 Azure Storage Client Library for C++ 1.0.0 版和更新版本為對象。 建議的版本是 Storage Client Library 2.2.0，可透過 [NuGet](https://www.nuget.org/packages/wastorage) 或 [GitHub](https://github.com/Azure/azure-storage-cpp/) 取得。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>建立 C++ 應用程式

在本指南中，您將使用可以在 C++ 應用程式內執行的儲存體功能。

若要這樣做，您需要安裝 Azure Storage Client Library for C++，並在 Azure 訂用帳戶中建立 Azure 儲存體帳戶。

若要安裝 Azure Storage Client Library for C++，您可以使用下列方法：

- **Linux：** 依照 [Azure Storage Client Library for C++ 讀我檔案：在 Linux 上開始使用](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)頁面中提供的指示操作。
- **Windows：** 在 Windows 上，請使用 [vcpkg](https://github.com/microsoft/vcpkg) 作為相依性管理員。 遵循 [快速入門](https://github.com/microsoft/vcpkg#quick-start) 以初始化 vcpkg。 然後，使用下列命令安裝二進位檔：

```powershell
.\vcpkg.exe install azure-storage-cpp
```

您可以在 [自述](https://github.com/Azure/azure-storage-cpp#download--install) 檔中找到如何建立原始程式碼並匯出至 NuGet 的指南。

## <a name="configure-your-application-to-access-queue-storage"></a>設定您的應用程式以存取佇列儲存體

在您要使用 Azure 儲存體 API 來存取佇列的 C++ 檔案頂端，加入下列 include 陳述式：

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>設定 Azure 儲存體連接字串

Azure 儲存體用戶端會使用儲存體連接字串來儲存存取資料管理服務時所用的端點與認證。 在用戶端應用程式中執行時，您必須以下列格式提供儲存體連接字串：使用您的儲存體帳戶名稱，以及 AccountKey [Azure 入口網站](https://portal.azure.com)中針對 *AccountName* 和 *AccountKey* 值所列之儲存體帳戶的儲存體存取金鑰。 如需有關儲存體帳戶和存取金鑰的資訊，請參閱 [關於 Azure 儲存體帳戶](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。 本範例將示範如何宣告靜態欄位來存放連接字串：

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

若要在本機 Windows 電腦中測試您的應用程式，您可以使用 [Azurite 儲存體模擬器](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。 Azurite 是一種公用程式，可在您的本機開發電腦上模擬 Azure 提供的 Blob 和佇列服務。 下列範例示範如何宣告靜態欄位以便將連接字串存放到本機儲存體模擬器中：

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

若要開始 Azurite，請參閱 [使用 Azurite 模擬器進行本機 Azure 儲存體開發](../common/storage-use-azurite.md)。

下列範例假設您已經使用這兩個方法之一來取得儲存體連接字串。

## <a name="retrieve-your-connection-string"></a>擷取連接字串

您可以使用 **cloud_storage_account** 類別來代表您的儲存體帳戶資訊。 若要從儲存體連接字串擷取儲存體帳戶資訊，您可以使用 **parse** 方法。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>作法：建立佇列

**Cloud_queue_client** 物件可讓您取得佇列的參考物件。 下列程式碼會建立 **cloud_queue_client** 物件。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

使用 **cloud_queue_client** 物件取得您想要使用之佇列的參考。 如果佇列不存在，您可以建立佇列。

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>作法：將訊息插入佇列中

若要將訊息插入現有佇列，請先建立新的 **cloud_queue_message**。 接下來，呼叫 **add_message** 方法。 **cloud_queue_message** 便可以從字串或 **byte** 陣列建立。 以下是建立佇列 (如果佇列不存在) 並插入訊息 'Hello, World' 的程式碼：

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>作法：查看下一個訊息

您可以藉由呼叫 **peek_message** 方法，在佇列前面查看訊息，而不將它從佇列中移除。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>作法：變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。 如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。 下列程式碼將使用新的內容更新佇列訊息，並將可見度逾時設定延長 60 秒。 這可儲存與訊息相關的工作狀態，並提供用戶端多一分鐘的時間繼續處理訊息。 您可以使用此技巧來追蹤佇列訊息上的多步驟工作流程，如果因為硬體或軟體故障而導致某個處理步驟失敗，將無需從頭開始。 通常，您也會保留重試計數，如果訊息重試超過 n 次，您會將它刪除。 這麼做可防止每次處理時便觸發應用程式錯誤的訊息。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-de-queue-the-next-message"></a>作法：在下一個訊息清除佇列

您的程式碼可以使用兩個步驟將訊息自佇列中清除佇列。 當您呼叫 **get_message** 時，您會取得佇列中的下一則訊息。 從這個佇列中讀取訊息的任何其他程式碼，都會無法看到從 **get_message** 傳回的訊息。 若要完成從佇列中移除訊息的作業，您也必須呼叫 **delete_message**。 這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。 在處理訊息之後，您的程式碼會立即呼叫 **delete_message** 。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>作法：運用清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種。 首先，您可以取得一批訊息 (最多 32 個)。 其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。 下列程式碼範例會使用 **get_messages** 方法，在一個呼叫中取得20個訊息。 然後，它會使用 **for** 迴圈處理每個訊息。 它也會將可見度逾時設定為每個訊息五分鐘。 請注意，系統會針對所有訊息同時開始計時 5 分鐘，所以從呼叫 **get_messages** 開始的 5 分鐘後，任何尚未刪除的訊息都會重新出現。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>作法：取得佇列長度

您可以取得佇列中的估計訊息數目。 **download_attributes** 方法會要求佇列服務擷取佇列屬性，其中包含訊息計數。 **approximate_message_count** 方法會取得佇列中大約的訊息數目。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>作法：刪除佇列

若要刪除佇列及其內含的所有訊息，請針對佇列物件呼叫 **delete_queue_if_exists** 方法。

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>後續步驟

了解佇列儲存體的基礎概念之後，請依照下列連結深入了解 Azure 儲存體。

- [如何使用 C++ 的 Blob 儲存體](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
- [如何使用 c + + 的資料表儲存體](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [列出 c + + 中的 Azure 儲存體資源](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [適用于 c + + 的儲存體用戶端程式庫參考](https://azure.github.io/azure-storage-cpp)
- [Azure 儲存體檔](https://azure.microsoft.com/documentation/services/storage/)
