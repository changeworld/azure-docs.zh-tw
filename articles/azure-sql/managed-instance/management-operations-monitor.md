---
title: 監視管理作業
titleSuffix: Azure SQL Managed Instance
description: 瞭解監視 Azure SQL 受控執行個體管理作業的不同方式。
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
ms.openlocfilehash: bdb021bc0247972fa29975c62bc9214e3b474e2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994542"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>監視 Azure SQL 受控執行個體管理作業
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體提供 [管理作業](management-operations-overview.md) 的監視，您可以在不再需要時用來部署新的受控實例、更新實例屬性或刪除實例。 

## <a name="overview"></a>概觀

所有管理作業都可以分類如下：

- 實例部署 (建立新實例) 。
- 實例更新 (變更實例屬性，例如虛擬核心或保留的儲存體) 。
- 已刪除實例。

大部分的管理作業都是 [長時間](management-operations-overview.md#duration)執行的作業。 因此，需要監視狀態或遵循作業步驟的進度。 

有數種方式可用來監視受控實例管理作業：

- [資源群組部署](../../azure-resource-manager/templates/deployment-history.md)
- [活動記錄檔](../../azure-monitor/platform/activity-log.md)
- [受控實例作業 API](#managed-instance-operations-api)


下表比較管理作業監視選項： 

| 選項 | 保留 | 支援取消 | 建立 | 更新 | 刪除 | 取消 | 步驟 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 資源群組部署 | 無限<sup>1</sup> | 否<sup>2</sup> | 可見 | 可見 | 不可見 | 可見 | 不可見 |
| 活動記錄檔 | 90 天 | 否 | 可見 | 可見 | 可見 | 可見 |  不可見 |
| 受控實例作業 API | 24 小時 | [是](management-operations-cancel.md) | 可見 | 可見 | 可見 | 可見 | 可見 |
|  |  |  |  |  |  |  | |

<sup>1</sup> 資源群組的部署歷程記錄限制為800部署。

<sup>2</sup> 個資源群組部署支援取消作業。 不過，由於取消邏輯的緣故，只有在執行取消動作之後排定部署的作業才會取消。 當資源群組部署取消時，不會取消進行中的部署。 由於受控實例部署包含一個長時間執行的步驟 (從 Azure 資源管理員的觀點來看) ，取消資源群組部署將不會取消受控實例部署，而作業將會完成。 

## <a name="managed-instance-operations-api"></a>受控實例作業 API

管理作業 Api 專門設計來監視作業。 監視受控實例作業可以提供作業參數和作業步驟的深入解析，以及 [取消特定的作業](management-operations-cancel.md)。 除了作業詳細資料和取消命令之外，您還可以在具有多資源部署的自動化腳本中使用此 API。根據進度步驟，您可以啟動一些相依的資源部署。

這些是 Api： 

| Command | 描述 |
| --- | --- |
|[受控執行個體作業-取得](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/get)|取得受控實例上的管理作業。|
|[受控執行個體作業-取消](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/cancel)|取消受控實例上的非同步作業。|
|[受控執行個體作業-依受控執行個體列出](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|取得在受控實例上執行的作業清單。|

> [!NOTE]
> 使用 API 版本2020-02-02，在作業清單中查看受控實例的建立作業。 這是 Azure 入口網站中所使用的預設版本，以及最新的 PowerShell 和 Azure CLI 套件。

## <a name="monitor-operations"></a>監視作業

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 入口網站中，使用受控實例的 **[總覽** ] 頁面來監視受控實例作業。 

例如， **建立** 作業會顯示在 [ **總覽** ] 頁面的建立程式開始時： 

![受控實例建立進度](./media/management-operations-monitor/monitoring-create-operation.png)

選取 **進行中的** 作業以開啟 **進行** 中的作業頁面，並查看 **建立** 或 **更新** 作業。 您也可以從這個頁面 [取消](management-operations-cancel.md) 作業。  

![受控實例操作詳細資料](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> 您 [可以取消](management-operations-cancel.md)使用 REST API 2020-02-02 版的 Azure 入口網站、PowerShell、Azure CLI 或其他工具提交的建立作業。 早于2020-02-02 的 REST API 版本會用來提交建立作業，將會啟動實例部署，但部署將不會列在 Operations API 中，而且無法取消。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Get-AzSqlInstanceOperation Cmdlet 會取得受控實例上之作業的相關資訊。 您可以藉由提供作業名稱來查看受控實例上的所有作業，或查看特定的作業。

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

如需詳細的命令說明，請參閱 [AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az sql mi op 清單會取得在受控實例上執行的作業清單。 如果尚未安裝 Azure CLI，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

如需詳細的命令說明，請參閱 [az sql mi op](https://docs.microsoft.com/cli/azure/sql/mi/op)。

---

## <a name="next-steps"></a>後續步驟

- 若要了解如何建立您的第一個受控執行個體，請參閱[快速入門指南](instance-create-quickstart.md)。
- 如需功能和比較清單，請參閱 [一般 SQL 功能](../database/features-comparison.md)。
- 如需 VNet 組態的詳細資訊，請參閱 [SQL 受控執行個體 VNet 組態](connectivity-architecture-overview.md)。
- 如需建立受控執行個體，並從備份檔案還原資料庫的快速入門，請參閱[建立受控執行個體](instance-create-quickstart.md)。
- 如需使用 Azure 資料庫移轉服務進行移轉的教學課程，請參閱[使用資料庫移轉服務的 SQL 受控執行個體移轉](../../dms/tutorial-sql-server-to-managed-instance.md)。
