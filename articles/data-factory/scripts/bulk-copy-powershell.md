---
title: 使用 PowerShell 大量複製資料
description: 這個 PowerShell 指令碼示範如何使用 Azure Data Factory，將資料從來源資料存放區大量複製到目的地資料存放區。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: ecfc9930c3387e19fd74d7cc52d11864ef25259a
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89439022"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell 指令碼 - 使用 Azure Data Factory 來大量複製多個資料表

此範例 PowerShell 腳本會將資料從 Azure SQL Database 中的多個資料表複製到先前的 SQL 資料倉儲) Azure Synapse Analytics (。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

如需了解執行此範例的必要條件，請參閱[教學課程：大量複製](../tutorial-bulk-copy.md#prerequisites)。

## <a name="sample-script"></a>範例指令碼

> [!IMPORTANT]
> 這個指令碼會建立 JSON 檔案，該檔案定義硬碟上 c:\ 資料夾中的 Data Factory 實體 (已連結的服務、資料集和管線)。

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure Synapse Analytics")]

## <a name="clean-up-deployment"></a>清除部署

執行範例指令碼之後，您可以使用下列命令以移除資源群組及與其相關聯的所有資源：

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
若要從資源群組移除資料處理站，請執行下列命令： 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [設定->set-azdatafactoryv2](/powershell/module/az.datafactory/set-azdatafactoryv2) | 建立資料處理站。 |
| [設定->set-azdatafactoryv2linkedservice](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | 在資料處理站中建立已連結的服務。 已連結的服務會將資料存放區或計算連結到資料處理站。 |
| [設定->set-azdatafactoryv2dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | 在資料處理站中建立資料集。 資料集代表管線中活動的輸入/輸出。 | 
| [設定->invoke-azdatafactoryv2pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | 在資料處理站中建立管線。 管線包含一或多個執行特定作業的活動。 在此管線中，複製活動會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 |
| [Invoke->invoke-azdatafactoryv2pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | 建立管線的執行。 也就是說，執行管線。 |
| [AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | 在管線中取得活動的執行 (活動執行) 的相關詳細資料。 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure Data Factory PowerShell 指令碼](../samples-powershell.md)中，找到其他 Azure Data Factory PowerShell 指令碼範例。
