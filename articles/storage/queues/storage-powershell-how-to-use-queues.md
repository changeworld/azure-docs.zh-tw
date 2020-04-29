---
title: 在 PowerShell 中執行 Azure 佇列儲存體動作
description: 如何在使用 PowerShell 的 Azure 佇列儲存體上執行作業
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/15/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 96828a854c340b89c26023ce60f9c85dd1bb4cdd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80473923"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>使用 Azure PowerShell 執行 Azure 佇列儲存體作業

Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可透過 HTTP 或 HTTPS 叫來存取這些訊息。 如需詳細資訊，請參閱 [Azure 佇列簡介](storage-queues-introduction.md)。 本做法文章涵蓋一般的佇列儲存體作業。 您會了解如何：

> [!div class="checklist"]
>
> * 建立佇列
> * 擷取佇列
> * 新增訊息
> * 讀取訊息
> * 刪除訊息
> * 刪除佇列

本做法需要 Azure PowerShell 模組 Az 0.7 版或更新版本。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。

沒有任何適用於佇列資料層的 PowerShell Cmdlet。 若要執行資料層作業 (例如新增訊息、讀取訊息，以及刪除訊息)，您必須使用 PowerShell 中公開的 .NET 儲存體用戶端程式庫。 您可以建立訊息物件，然後使用 AddMessage 這類命令對該訊息執行作業。 本文示範如何執行這項作業。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

使用 `Connect-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>擷取位置清單

如果您不知道要使用哪一個位置，您可以列出可用的位置。 當清單顯示之後，尋找您想要使用的位置。 這個練習將使用 **eastus**。 將其儲存於變數 **location** 中供未來使用。

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立資源群組。

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 在變數中儲存資源群組名稱供日後使用。 在此範例中，會在 *eastus* 區域中建立名為 *howtoqueuesrg* 的資源群組。

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>建立儲存體帳戶

使用 [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) 來建立具有本地備援儲存體 (LRS) 的標準一般用途儲存體帳戶。 取得儲存體帳戶內容，定義要使用的儲存體帳戶。 使用儲存體帳戶時，會參考內容而非重複提供認證。

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>建立佇列

下列範例會先使用儲存體帳戶內容建立 Azure 儲存體的連線，其中包含儲存體帳戶名稱及其存取金鑰 。 接下來，它會呼叫[remove-azstoragequeue](/powershell/module/az.storage/New-AzStorageQueue) Cmdlet 來建立名為 ' howtoqueue ' 的佇列。

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

如需 Azure 佇列服務命名慣例的資訊，請參閱 [為佇列和中繼資料命名](https://msdn.microsoft.com/library/azure/dd179349.aspx)。

## <a name="retrieve-a-queue"></a>擷取佇列

您可以查詢與擷取儲存體帳戶中的特定佇列或所有佇列清單。 下列範例示範如何擷取儲存體帳戶中的所有佇列，以及一個特定的佇列；這兩個命令都會使用 [Get-AzStorageQueue](/powershell/module/az.storage/Get-AzStorageQueue) Cmdlet。

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>將訊息新增至佇列

影響佇列中實際訊息的作業會使用 PowerShell 中公開的 .NET 儲存體用戶端程式庫。 若要將訊息新增至佇列，請建立 message 物件[CloudQueueMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage)類別的新實例。 接下來，呼叫[AddMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage)方法。 您可以從字串 (採用 UTF-8 格式) 或位元組陣列建立 CloudQueueMessage。

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

此**不可見逾時**定義在重新開放處理前，訊息維持不可見的時間。 預設值為 30 秒。

您的程式碼可使用兩個步驟，從佇列讀取訊息。 當您呼叫[CloudQueue. GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage)方法時，您會取得佇列中的下一個訊息。 從這個佇列讀取訊息的任何其他程式碼都不會看到從**GetMessage**傳回的訊息。 若要完成從佇列中移除訊息的作業，請呼叫[CloudQueue. DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage)方法。

在下列範例中，您可以閱讀三個佇列訊息，然後等待 10 秒 (不可見逾時)。 接著重新讀取三個訊息，然後呼叫 **DeleteMessage** 來刪除讀取後的訊息。 如果嘗試在刪除訊息後讀取佇列，會將 $queueMessage 傳回成 NULL。

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

若要刪除佇列及其內含的所有訊息，請呼叫 Remove-AzStorageQueue Cmdlet。 下列範例示範如何使用 Remove-AzStorageQueue Cmdlet 來刪除本練習所使用的特定佇列。

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

在本做法文章中，您學會使用 PowerShell 進行基本佇列儲存體管理，包括如何：

> [!div class="checklist"]
>
> * 建立佇列
> * 擷取佇列
> * 新增訊息
> * 讀取下一個訊息
> * 刪除訊息
> * 刪除佇列

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell 儲存體 Cmdlet

* [儲存體 PowerShell Cmdlet](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 儲存體總管

* [Microsoft Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。
