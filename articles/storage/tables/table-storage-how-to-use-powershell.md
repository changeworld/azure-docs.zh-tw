---
title: 使用 PowerShell 執行 Azure 資料表儲存體作業 | Microsoft Docs
description: 瞭解如何使用 PowerShell 運行常見任務，如創建、查詢、從 Azure 表存儲帳戶中刪除資料。
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.openlocfilehash: f1846fae4cbf473df688a2b184c307d72ab2f8d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721467"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>使用 Azure PowerShell 執行 Azure 資料表儲存體作業 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure 資料表儲存體是 NoSQL 資料存放區，您可以用來儲存和查詢龐大的結構化、非關聯式資料集。 服務的主要元件是資料表、實體和屬性。 資料表是一組實體。 實體是一組屬性。 每個實體最多可有 252 個屬性，也就是所有的名稱/值組。 本文假設您已熟悉 Azure 資料表儲存體服務概念。 如需詳細資訊，請參閱[了解表格服務資料模型](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)和[以 .NET 開始使用 Azure 資料表儲存體](../../cosmos-db/table-storage-how-to-use-dotnet.md)。

本做法文章涵蓋一般的 Azure 資料表儲存體作業。 您會了解如何： 

> [!div class="checklist"]
> * 建立資料表
> * 擷取資料表
> * 新增資料表實體
> * 查詢資料表
> * 刪除資料表實體
> * 刪除資料表

本做法文章示範如何在新的資源群組中建立新的 Azure 儲存體帳戶，以便在完成時輕鬆地將它移除。 如果您想要使用現有的儲存體帳戶，則可以改為那麼做。

這些示例需要 Az PowerShell 模組`Az.Storage (1.1.0 or greater)`和`Az.Resources (1.2.0 or greater)`。 在 PowerShell 視窗中，執行 `Get-Module -ListAvailable Az*` 以尋找版本。 如果未顯示任何項目，或者您必須升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> 使用 PowerShell 中的 Azure 功能需要您安裝 `Az` 模組。 的`AzTable`當前版本與較舊的 AzureRM 模組不相容。
> 如果需要，請按照[安裝 Az 模組的最新安裝說明](/powershell/azure/install-az-ps)進行操作。

安裝或更新 Azure PowerShell 後，必須安裝具有管理實體的命令的模組**AzTable**。 若要安裝此模組，請以系統管理員身分執行 PowerShell，並使用 **Install-Module** 命令。

> [!IMPORTANT]
> 出於模組名稱相容性的原因，我們仍在 PowerShell 庫中的舊名稱`AzureRmStorageTables`下發布此同一模組。 本文檔將僅引用新名稱。

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>登入 Azure

使用 `Add-AzAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>擷取位置清單

如果您不知道要使用哪一個位置，您可以列出可用的位置。 當清單顯示之後，尋找您想要使用的位置。 這些範例會使用 **eastus**。 將這個值儲存於變數 **location** 中供未來使用。

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令來建立資源群組。 

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 在變數中儲存資源群組名稱供日後使用。 在此範例中，會在 *eastus* 區域中建立名為 *pshtablesrg* 的資源群組。

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>建立儲存體帳戶

使用 [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) 來建立具有本地備援儲存體 (LRS) 的標準一般用途儲存體帳戶。 請確保指定唯一的存儲帳戶名稱。 接下來，獲取表示存儲帳戶的上下文。 在存儲帳戶上操作時，可以引用上下文，而不是重複提供憑據。

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>建立新的資料表

要創建表，請使用新[AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) Cmdlet。 在此範例中，資料表名為 `pshtesttable`。

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>擷取儲存體帳戶中的資料表清單

使用[Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable)檢索存儲帳戶中的表清單。

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>擷取特定資料表的參考

若要在資料表上執行作業，您需要針對那個特定資料表的參考。 使用[獲取-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable)獲取引用。

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>特定表的引用 CloudTable 屬性

> [!IMPORTANT]
> 使用 AzTable PowerShell 模組時，必須使用**CloudTable。** 調用**Get-AzTable**命令以獲取對此物件的引用。 如果表不存在，則此命令也會創建該表。

要使用**AzTable**對表執行操作，您需要引用特定表的 CloudTable 屬性。

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>刪除資料表

要刪除表，請使用[刪除-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable)。 此 Cmdlet 可移除資料表，包括其中的所有資料。

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>清除資源

如果您依照此做法文章開頭的步驟，建立了新的資源群組和儲存體帳戶，就可以藉由移除資源群組來移除您在此練習中建立的所有資產。 此命令會刪除群組內含的所有資源，以及資源群組本身。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在本做法文章中，您學會使用 PowerShell 進行一般的 Azure 資料表儲存體作業，包括如何： 

> [!div class="checklist"]
> * 建立資料表
> * 擷取資料表
> * 新增資料表實體
> * 查詢資料表
> * 刪除資料表實體
> * 刪除資料表

如需詳細資訊，請參閱下列文章

* [儲存體 PowerShell Cmdlet](/powershell/module/az.storage#storage)

* [使用 PowerShell 中的 Azure 表 - AzureRm 存儲表/AzTable PS 模組 v2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure 儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。
