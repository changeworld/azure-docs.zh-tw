---
title: 取消管理作業
titleSuffix: Azure SQL Managed Instance
description: 瞭解如何取消 Azure SQL 受控執行個體管理作業。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 4ec999cc35e7d18287679c74c6d45a5aa2ecb9e7
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994653"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>取消 Azure SQL 受控執行個體管理作業
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體可以取消某些 [管理作業](management-operations-overview.md)，例如當您部署新的受控實例或更新實例屬性時。 

## <a name="overview"></a>概觀

 所有管理作業都可以分類如下：

- 實例部署 (建立新實例) 。
- 實例更新 (變更實例屬性，例如虛擬核心或保留的儲存體) 。
- 已刪除實例。

您可以 [監視管理作業的進度和狀態](management-operations-monitor.md) ，並視需要取消其中一些作業。 

下表摘要說明您是否可以取消管理作業，以及它們的一般整體持續時間：

類別  |作業  |可  |預估的取消持續時間  |
|---------|---------|---------|---------|
|部署 |建立實例 |Yes |90% 的作業會在5分鐘內完成。 |
|更新 |實例儲存體擴大/縮小 (一般用途)  |No |  |
|更新 |實例儲存體擴大/縮小 (商務關鍵性)  |Yes |90% 的作業會在5分鐘內完成。 |
|更新 |實例計算 (虛擬核心) 向上和向下調整 (一般用途)  |Yes |90% 的作業會在5分鐘內完成。 |
|更新 |實例計算 (虛擬核心) 向上和向下調整 (業務關鍵)  |Yes |90% 的作業會在5分鐘內完成。 |
|更新 |實例服務層級 (一般用途變更為業務關鍵，反之亦然)  |Yes |90% 的作業會在5分鐘內完成。 |
|刪除 |執行個體刪除 |No |  |
|刪除 |虛擬叢集刪除 (為使用者起始的作業)  |No |  |

## <a name="cancel-management-operation"></a>取消管理操作

# <a name="portal"></a>[入口網站](#tab/azure-portal)

若要使用 Azure 入口網站取消管理作業，請遵循下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)
1. 移至 SQL 受控執行個體的 **總覽** 分頁。 
1. 選取進行中作業旁的 **通知** 方塊，以開啟進行中的 **操作** 頁面。 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="選取 [進行中的作業] 方塊，以開啟進行中的動作頁面。":::

1. 選取頁面底部的 **[取消操作** ]。 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="選取 [取消] 以取消作業。":::

1. 確認您要取消操作。 


如果取消要求成功，就會取消管理作業並導致失敗。 您將收到取消作業成功或失敗的通知。

![正在取消作業結果](./media/management-operations-cancel/canceling-operation-result.png)


如果取消要求失敗，或 [取消] 按鈕未啟用，則表示管理作業已進入無法取消的狀態，而且很快就會完成。  管理作業會繼續執行，直到完成為止。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

如果尚未安裝 Azure PowerShell，請參閱 [安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps)。

若要取消管理作業，您必須指定管理作業名稱。 因此，請先使用 get 命令來取出作業清單，然後取消特定的作業。

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

如需詳細的命令說明，請參閱 [AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) 和 [Stop-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

如果尚未安裝 Azure CLI，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

若要取消管理作業，您必須指定管理作業名稱。 因此，請先使用 get 命令來取出作業清單，然後取消特定的作業。

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

如需詳細的命令說明，請參閱 [az sql mi op](https://docs.microsoft.com/cli/azure/sql/mi/op)。

---

## <a name="canceled-deployment-request"></a>已取消部署要求

使用 API 版本2020-02-02 時，只要接受實例建立要求，此實例就會開始以資源的形式存在，無論 **部署程式 (** 受控實例狀態是布建) 的進度。 如果您取消實例部署要求 (建立) 的新實例，受控實例將會從布建 **狀態移** 至 **FailedToCreate**。

無法建立的實例仍會以資源的形式出現，而且： 

- 不收費
- 請勿會計入 (子網或 vCore 配額的資源限制) 
- 保留實例名稱，以部署具有相同名稱的實例，刪除失敗的實例以釋放名稱


> [!NOTE]
> 若要將資源或受控實例清單中的雜訊降至最低，請刪除無法部署的實例，或已取消部署的實例。 


## <a name="next-steps"></a>下一步

- 若要瞭解如何建立您的第一個受控實例，請參閱 [快速入門手冊](instance-create-quickstart.md)。
- 如需功能和比較清單，請參閱 [一般 SQL 功能](../database/features-comparison.md)。
- 如需 VNet 設定的詳細資訊，請參閱 [SQL 受控執行個體 vnet](connectivity-architecture-overview.md)設定。
- 如需建立受控實例，並從備份檔案還原資料庫的快速入門，請參閱 [建立受控實例](instance-create-quickstart.md)。
- 如需使用 Azure 資料庫移轉服務進行遷移的教學課程，請參閱 [SQL 受控執行個體使用資料庫移轉服務進行遷移](../../dms/tutorial-sql-server-to-managed-instance.md)。
