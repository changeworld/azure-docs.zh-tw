---
title: 如何使用 PowerShell 的 Azure 佇列儲存體-Azure 儲存體
description: 透過 PowerShell 在 Azure 佇列儲存體上執行作業。 您可以使用 Azure 佇列儲存體來儲存可透過 HTTP/HTTPS 存取的大量訊息。
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 05/15/2019
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fba288f76377e744b1fe21a52e03a43409c505bf
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585610"
---
# <a name="how-to-use-azure-queue-storage-from-powershell"></a>如何使用 PowerShell 的 Azure 佇列儲存體

Azure 佇列儲存體是用來儲存大量訊息的服務，可透過 HTTP 或 HTTPS 從世界各地存取。 如需詳細資訊，請參閱 [Azure 佇列儲存體簡介](storage-queues-introduction.md)。 本操作說明文章涵蓋一般的佇列儲存體作業。 您會了解如何：

> [!div class="checklist"]
>
> - 建立佇列
> - 擷取佇列
> - 新增訊息
> - 讀取訊息
> - 刪除訊息
> - 刪除佇列

本操作指南需要 Azure PowerShell (`Az`) module v 0.7 或更新版本。 執行 `Get-Module -ListAvailable Az` 以尋找目前安裝的版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

沒有任何適用於佇列資料層的 PowerShell Cmdlet。 若要執行資料層作業 (例如新增訊息、讀取訊息，以及刪除訊息)，您必須使用 PowerShell 中公開的 .NET 儲存體用戶端程式庫。 您會建立訊息物件，然後您可以使用命令（例如） `AddMessage` 來執行該訊息的作業。 本文示範如何執行這項作業。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>擷取位置清單

如果您不知道要使用哪一個位置，您可以列出可用的位置。 當清單顯示之後，尋找您想要使用的位置。 此練習將會使用 `eastus` 。 將此儲存在變數中 `location` 以供日後使用。

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立資源群組。

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 在變數中儲存資源群組名稱供日後使用。 在此範例中，`eastus` 區域中會建立名為 `howtoqueuesrg` 的資源群組。

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>建立儲存體帳戶

使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 來建立具有本地備援儲存體 (LRS) 的標準一般用途儲存體帳戶。 取得儲存體帳戶內容，定義要使用的儲存體帳戶。 使用儲存體帳戶時，會參考內容而非重複提供認證。

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>建立佇列

下列範例會先使用儲存體帳戶內容建立 Azure 儲存體的連線，其中包含儲存體帳戶名稱及其存取金鑰 。 接下來，它會呼叫 [remove-azstoragequeue](/powershell/module/az.storage/new-azstoragequeue) Cmdlet 來建立名為的佇列 `howtoqueue` 。

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

如需 Azure 佇列儲存體的命名慣例的詳細資訊，請參閱 [命名佇列和中繼資料](/rest/api/storageservices/naming-queues-and-metadata)。

## <a name="retrieve-a-queue"></a>擷取佇列

您可以查詢和取出特定佇列，或儲存體帳戶中所有佇列的清單。 下列範例示範如何擷取儲存體帳戶中的所有佇列，以及一個特定的佇列；這兩個命令都會使用 [Get-AzStorageQueue](/powershell/module/az.storage/get-azstoragequeue) Cmdlet。

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>將訊息新增至佇列

影響佇列中實際訊息的作業會使用 PowerShell 中公開的 .NET 儲存體用戶端程式庫。 若要將訊息新增至佇列，請建立新的 message 物件實例（ [`Microsoft.Azure.Storage.Queue.CloudQueueMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) class）。 接下來，呼叫 [`AddMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage) 方法。 `CloudQueueMessage`可以從字串 (utf-8 格式) 或位元組陣列建立。

下列範例示範如何將訊息新增至佇列。

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

如果您使用 [Azure 儲存體總管](https://storageexplorer.com)，則可以連線到您的 Azure 帳戶，以及檢視儲存體帳戶中的佇列，並向下鑽研至某個佇列以檢視佇列上的訊息。

## <a name="read-a-message-from-the-queue-then-delete-it"></a>讀取佇列的訊息，然後刪除它

會嘗試以最佳的先進先出順序讀取訊息。 不保證會採用此作法。 當您從佇列讀取訊息時，查看佇列的其他處理序會看不到它。 這可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。

此 **不可見逾時** 定義在重新開放處理前，訊息維持不可見的時間。 預設值為 30 秒。

您的程式碼可使用兩個步驟，從佇列讀取訊息。 當您呼叫 [`Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) 方法時，您會取得佇列中的下一則訊息。 從 `GetMessage` 傳回的訊息，對於從此佇列讀取訊息的任何其他程式碼而言，將會是不可見的。 若要完成從佇列中移除訊息的作業，您可以呼叫 [`Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) 方法。

在下列範例中，您可以閱讀三個佇列訊息，然後等待 10 秒 (不可見逾時)。 然後，您會再次讀取三則訊息，藉由呼叫來刪除訊息 `DeleteMessage` 。 如果您嘗試在刪除訊息之後讀取佇列， `$queueMessage` 將會傳回為 `$null` 。

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>刪除佇列

若要刪除佇列及其內含的所有訊息，請呼叫 `Remove-AzStorageQueue` Cmdlet。 下列範例說明如何使用 Cmdlet 刪除此練習中使用的特定佇列 `Remove-AzStorageQueue` 。

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>清除資源

若要移除您在這個練習中建立的所有資產，請移除此資源群組。 這會同時刪除群組內含的所有資源。 在本例中，它會移除建立的儲存體帳戶和資源群組本身。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在此操作說明文章中，您已瞭解如何使用 PowerShell 進行基本佇列儲存體管理，包括如何：

> [!div class="checklist"]
>
> - 建立佇列
> - 擷取佇列
> - 新增訊息
> - 讀取下一個訊息
> - 刪除訊息
> - 刪除佇列

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell 儲存體 Cmdlet

- [儲存體 PowerShell Cmdlet](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 儲存體總管

- [Microsoft Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。
