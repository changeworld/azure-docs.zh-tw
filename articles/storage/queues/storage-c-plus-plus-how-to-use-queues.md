---
title: 如何使用佇列儲存體 (c + +) -Azure 儲存體
description: 瞭解如何在 Azure 中使用佇列儲存體服務。 範例是以 C++ 撰寫的。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 07/16/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 44d64c54049c02b6602f01b97effcc33b03dbcfe
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591322"
---
# <a name="how-to-use-queue-storage-from-c"></a>如何使用 C++ 的佇列儲存體

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概觀

本指南將說明如何使用 Azure 佇列儲存體服務執行一般案例。 這些範例是以 c + + 撰寫，並使用 [適用于 c + + 的 Azure 儲存體用戶端程式庫](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)。 所涵蓋的案例包括「插入」、「查看」、「取得」和「刪除」佇列訊息，以及「建立和刪除佇列」。

> [!NOTE]
> 本指南的目標是適用于 c + + 1.0.0 版和更新版本的 Azure 儲存體用戶端程式庫。 建議的版本是 Azure 儲存體用戶端程式庫 v 2.2.0，可透過 [NuGet](https://www.nuget.org/packages/wastorage) 或 [GitHub](https://github.com/Azure/azure-storage-cpp/)取得。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>建立 C++ 應用程式

在本指南中，您將使用可以在 C++ 應用程式內執行的儲存體功能。

若要這樣做，您必須安裝適用于 c + + 的 Azure 儲存體用戶端程式庫，並在您的 Azure 訂用帳戶中建立 Azure 儲存體帳戶。

<!-- docutune:casing "Getting Started on Linux" -->

若要安裝適用于 c + + 的 Azure 儲存體用戶端程式庫，您可以使用下列方法：

- **Linux：** 遵循 [Azure 儲存體 client library For c + + 讀我檔案：消費者入門 On Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) 頁面中提供的指示。
- **Windows：** 在 Windows 上，請使用 [vcpkg](https://github.com/microsoft/vcpkg) 作為相依性管理員。 遵循 [快速入門](https://github.com/microsoft/vcpkg#quick-start) 以初始化 `vcpkg` 。 然後，使用下列命令安裝二進位檔：

```powershell
.\vcpkg.exe install azure-storage-cpp
```

您可以在 [自述](https://github.com/Azure/azure-storage-cpp#download--install) 檔中找到如何建立原始程式碼並匯出至 NuGet 的指南。

## <a name="configure-your-application-to-access-queue-storage"></a>設定您的應用程式以存取佇列儲存體

在您想要使用 Azure 儲存體 Api 來存取佇列的 c + + 檔案頂端，加入下列 include 語句：

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>設定 Azure 儲存體連接字串

Azure 儲存體用戶端會使用儲存體連接字串來儲存存取資料管理服務時所用的端點與認證。 在用戶端應用程式中執行時，您必須提供下列格式的儲存體連接字串：使用儲存體帳戶的名稱，以及針對和值的 [Azure 入口網站](https://portal.azure.com) 中所列出儲存體帳戶的儲存體存取金鑰 `AccountName` `AccountKey` 。 如需有關儲存體帳戶和存取金鑰的資訊，請參閱 [關於 Azure 儲存體帳戶](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。 本範例將示範如何宣告靜態欄位來存放連接字串：

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

若要在本機 Windows 電腦中測試您的應用程式，您可以使用 [Azurite 儲存體模擬器](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。 Azurite 是一種公用程式，可在本機開發電腦上模擬 Azure Blob 儲存體和佇列儲存體。 下列範例示範如何宣告靜態欄位以便將連接字串存放到本機儲存體模擬器中：

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

若要開始 Azurite，請參閱 [使用 Azurite 模擬器進行本機 Azure 儲存體開發](../common/storage-use-azurite.md)。

下列範例假設您已經使用這兩個方法之一來取得儲存體連接字串。

## <a name="retrieve-your-connection-string"></a>擷取連接字串

您可以使用 `cloud_storage_account` 類別來代表儲存體帳戶資訊。 若要從儲存體連接字串取出您的儲存體帳戶資訊，您可以使用 `parse` 方法。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>作法：建立佇列

`cloud_queue_client`物件可讓您取得佇列的參考物件。 下列程式碼會建立 `cloud_queue_client` 物件。

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

使用 `cloud_queue_client` 物件取得您想要使用之佇列的參考。 如果佇列不存在，您可以建立佇列。

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>作法：將訊息插入佇列中

若要將訊息插入現有佇列，請先建立新的 `cloud_queue_message` 。 接下來，呼叫 `add_message` 方法。 `cloud_queue_message`可以從字串 (utf-8 格式) 或位元組陣列建立。 以下程式碼會建立佇列 (（如果不存在）) 並插入訊息 `Hello, World` ：

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

您可以藉由呼叫方法，在佇列前面查看訊息，而不需要將它從佇列中移除 `peek_message` 。

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

您可以在佇列中就地變更訊息內容。 如果訊息代表工作作業，則您可以使用此功能來更新工作作業的狀態。 下列程式碼將使用新的內容更新佇列訊息，並將可見度逾時設定延長 60 秒。 這可儲存與訊息相關的工作狀態，並提供用戶端多一分鐘的時間繼續處理訊息。 您可以使用這項技術來追蹤佇列訊息上的多步驟工作流程，而不需要從頭開始（如果處理步驟因為硬體或軟體故障而失敗）。 通常，您也會保留重試計數，如果訊息重試超過 n 次，您會將它刪除。 這麼做可防止每次處理時便觸發應用程式錯誤的訊息。

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

## <a name="how-to-dequeue-the-next-message"></a>作法：清除下一個佇列訊息

您的程式碼可以使用兩個步驟來清除佇列訊息。 當您呼叫時 `get_message` ，會取得佇列中的下一則訊息。 從 `get_message` 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言，將會是不可見的。 若要完成從佇列中移除訊息，您還必須呼叫 `delete_message` 。 這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。 您的程式碼會在 `delete_message` 處理完訊息之後立即呼叫。

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

## <a name="how-to-use-additional-options-for-dequeuing-messages"></a>如何：使用清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種。 首先，您可以取得一批訊息 (最多 32 個)。 其次，您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。 下列程式碼範例將使用 `get_messages` 方法，在一次呼叫中取得 20 個訊息。 接著其會使用 `for` 迴圈處理每個訊息。 它也會將可見度逾時設定為每個訊息五分鐘。 請注意，系統會同時為所有訊息啟動五分鐘的時間，因此，從呼叫以來經過五分鐘後 `get_messages` ，任何尚未刪除的訊息都會重新顯示。

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

您可以取得佇列中的估計訊息數目。 方法會傳回 `download_attributes` 佇列屬性，包括訊息計數。 `approximate_message_count`方法會取得佇列中大約的訊息數目。

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

若要刪除佇列及其內含的所有訊息，請 `delete_queue_if_exists` 在佇列物件上呼叫方法。

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

既然您已瞭解佇列儲存體的基本概念，請遵循下列連結以深入瞭解 Azure 儲存體。

- [如何使用 C++ 的 Blob 儲存體](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
- [如何使用 c + + 的資料表儲存體](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [以 C++ 列出 Azure 儲存體資源](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [適用于 c + + 的 Azure 儲存體用戶端程式庫參考](https://azure.github.io/azure-storage-cpp)
- [Azure 儲存體文件](https://azure.microsoft.com/documentation/services/storage/)
