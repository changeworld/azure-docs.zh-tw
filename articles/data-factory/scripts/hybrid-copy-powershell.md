---
title: 使用 PowerShell 將資料從本地複製到 Azure
description: 這個 PowerShell 指令碼會將資料從內部部署 SQL Server 資料庫複製到另一個「Azure Blob 儲存體」。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: jingwang
author: linda33wj
manager: shwang
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 10555defc4888af66bb88d19190b6543aa8ae0c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974695"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>使用 PowerShell 來建立資料處理站管線，以將資料從內部部署環境複製到 Azure

這個範例 PowerShell 指令碼會在 Azure Data Factory 中建立管線，以將資料從內部部署 SQL Server 資料庫複製到「Azure Blob 儲存體」。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Prerequisites

- **SQL 伺服器**。 在此範例中，您會使用內部部署 SQL Server 資料庫作為**來源**資料存放區。
- **Azure 存儲帳戶**。 在此範例中，您會使用 Azure Blob 儲存體作為**目的地/接收**資料存放區。 如果沒有 Azure 存儲帳戶，請參閱[創建存儲帳戶](../../storage/common/storage-account-create.md)文章，瞭解創建存儲帳戶的步驟。
- **自託管的集成運行時**。 從[下載中心](https://www.microsoft.com/download/details.aspx?id=39717)下載 MSI 檔案，然後執行它以在您的電腦上安裝自我裝載整合執行階段。  

### <a name="create-sample-database-in-sql-server"></a>在 SQL Server 中建立範例資料庫
1. 在內部部署 SQL Server 資料庫中，使用下列 SQL 指令碼來建立一個名為 **emp** 的資料表：

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. 將一些範例資歷插入到此資料表中：

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>範例指令碼

> [!IMPORTANT]
> 這個指令碼會建立 JSON 檔案，該檔案定義硬碟上 c:\ 資料夾中的 Data Factory 實體 (已連結的服務、資料集和管線)。

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


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
| [集-阿茲達工廠V2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | 建立資料處理站。 |
| [新-阿茲達工廠V2連結服務加密憑據](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | 加密已連結服務中的認證，然後使用加密的認證來產生一個新的已連結服務定義。
| [集-阿茲達工廠V2連結服務](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | 在資料處理站中建立已連結的服務。 已連結的服務會將資料存放區或計算連結到資料處理站。 |
| [資料集-阿茲達工廠V2資料集](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | 在資料處理站中建立資料集。 資料集代表管線中活動的輸入/輸出。 |
| [集-阿茲達工廠V2管道](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | 在資料處理站中建立管線。 管線包含一或多個執行特定作業的活動。 在此管線中，複製活動會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 |
| [調用-阿茲達工廠V2管道](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | 建立管線的執行。 也就是說，執行管線。 |
| [獲取阿茲達工廠V2活動運行](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | 在管線中取得活動的執行 (活動執行) 的相關詳細資料。
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 刪除資源群組，包括所有的巢狀資源。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在 [Azure Data Factory PowerShell 範例](../samples-powershell.md)中，找到其他 Azure Data Factory PowerShell 指令碼範例。
