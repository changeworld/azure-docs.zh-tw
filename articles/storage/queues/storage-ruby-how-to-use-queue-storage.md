---
title: 如何使用 Ruby 的佇列儲存體-Azure 儲存體
description: 瞭解如何使用 Azure 佇列儲存體來建立和刪除佇列，以及插入、取得和刪除訊息。 範例以 Ruby 撰寫。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8558949e49bcf551c9276458d375fb9ac9636184
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587657"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>如何使用 Ruby 的佇列儲存體

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>概觀

本指南示範如何使用 Microsoft Azure 佇列儲存體服務執行常見案例。 這些範例是以 Ruby Azure API 撰寫的。 所涵蓋的案例包括「插入」、「查看」、「取得」和「刪除」佇列訊息，以及「建立和刪除佇列」。

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>建立 Ruby 應用程式

建立 Ruby 應用程式。 如需相關指示，請參閱 [在 Linux 上的 App Service 中建立 Ruby 應用程式](../../app-service/quickstart-ruby.md)。

## <a name="configure-your-application-to-access-storage"></a>設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您必須下載並使用 Ruby Azure 套件，其中包含一組可與儲存體 REST 服務通訊的便利程式庫。

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>使用 RubyGems 來取得套件

1. 使用命令列介面，例如 PowerShell (Windows)、Terminal (Mac) 或 Bash (Unix)。
2. `gem install Azure`在命令視窗中輸入，以安裝 gem 和相依性。

### <a name="import-the-package"></a>匯入封裝

使用您偏好的文字編輯器，將以下內容新增至您打算使用儲存體的 Ruby 檔案頂端：

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>設定 Azure 儲存體連接

Azure 模組會讀取環境變數 `AZURE_STORAGE_ACCOUNT` ，以及 `AZURE_STORAGE_ACCESS_KEY` 連接到您的 Azure 儲存體帳戶所需的資訊。 如果未設定這些環境變數，您必須在使用下列程式碼之前指定帳戶資訊 `Azure::QueueService` ：

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

若要從 Azure 入口網站中的傳統或 Resource Manager 儲存體帳戶取得這些值：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽到您要使用的儲存體帳戶。
3. 在右邊的 [ **設定** ] 分頁中，按一下 [ **存取金鑰**]。
4. 在出現的 [ **存取金鑰** ] 分頁中，您會看到存取金鑰1和存取金鑰2。 您可以使用其中一個存取金鑰。
5. 按一下複製圖示以將金鑰複製到剪貼簿。

## <a name="how-to-create-a-queue"></a>作法：建立佇列

下列程式碼會建立 `Azure::QueueService` 物件，讓您能夠使用佇列。

```ruby
azure_queue_service = Azure::QueueService.new
```

使用 `create_queue()` 方法來建立具有指定之名稱的佇列。

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>作法：將訊息插入佇列中

若要將訊息插入佇列中，請使用 `create_message()` 方法來建立新訊息，並將它新增至佇列。

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>作法：查看下一個訊息

您可以藉由呼叫方法，在佇列前面查看訊息，而不需要將它從佇列中移除 `peek_messages()` 。 依預設，會 `peek_messages()` 查看單一訊息。 您也可以指定所要查看的訊息數。

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>作法：清除下一個佇列訊息

您可以使用兩個步驟將訊息從佇列中移除。

1. 當您呼叫時 `list_messages()` ，您預設會取得佇列中的下一則訊息。 您也可以指定您要取得的訊息數。 從 `list_messages()` 這個佇列中讀取訊息的任何其他程式碼，都會無法看到從傳回的訊息。 您可以傳入以秒為單位的可見性逾時，作為參數。
2. 若要完成從佇列中移除訊息，您還必須呼叫 `delete_message()` 。

這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。 您的程式碼會在 `delete_message()` 處理完訊息之後立即呼叫。

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>作法：變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。 下列程式碼會使用 `update_message()` 方法來更新訊息。 方法會傳回元組，其中包含佇列訊息的 pop 回條，以及表示在 `DateTime` 佇列中顯示訊息時的 UTC 值。

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>如何：清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種。

1. 您可以取得一批訊息。
2. 您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。

下列程式碼範例會使用 `list_messages()` 方法，在一個呼叫中取得15個訊息。 接著，它會列出每個訊息，並加以刪除。 它也會將可見度逾時設定為每個訊息五分鐘。

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>作法：取得佇列長度

您可以取得佇列中的估計訊息數目。 方法會傳回 `get_queue_metadata()` 大約的訊息計數和其他佇列中繼資料。

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>作法：刪除佇列

若要刪除佇列及其內含的所有訊息，請 `delete_queue()` 在佇列物件上呼叫方法。

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>後續步驟

既然您已瞭解佇列儲存體的基本概念，請遵循下列連結以瞭解更複雜的儲存體工作。

- 造訪 [Azure 儲存體團隊部落格](/archive/blogs/windowsazurestorage/)
- 請造訪 GitHub 上的 [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) 儲存機制 (英文)。

如需本文所討論的 Azure 佇列儲存體與 [如何使用服務匯流排佇列](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/)中所討論的 Azure 服務匯流排佇列之間的比較，請參閱 [azure 佇列儲存體和服務匯流排佇列-比較和對比](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
