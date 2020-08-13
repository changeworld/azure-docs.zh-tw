---
title: 在 SQL 受控執行個體上手動起始容錯移轉
description: 瞭解如何在 Azure SQL 受控執行個體上手動容錯移轉主要和次要複本。
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 08/12/2020
ms.openlocfilehash: faef8c29b2a5e75745e36c5d826b4feee2d60a98
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88169118"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>在 SQL 受控執行個體上由使用者起始的手動容錯移轉

本文說明如何以手動方式容錯移轉 SQL 受控執行個體一般用途 (GP) 和商務關鍵性 (BC) 服務層級的主要節點，以及如何手動容錯移轉 BC 服務層級的次要唯讀複本節點。

## <a name="when-to-use-manual-failover"></a>使用手動容錯移轉的時機

[高可用性](../database/high-availability-sla.md)是 SQL 受控執行個體平臺的基本部分，可讓您的資料庫應用程式以透明的方式運作。 當節點降低或偵測錯誤時，從主要節點容錯移轉到次要節點，或在每月定期執行軟體更新時，都是在 Azure 中使用 SQL 受控執行個體的所有應用程式的預期發生。

基於下列原因，您可能會考慮在 SQL 受控執行個體上執行[手動容錯移轉](../database/high-availability-sla.md#testing-application-fault-resiliency)：
- 在部署至生產環境之前，先測試應用程式的容錯移轉復原
- 測試端對端系統，以在自動容錯移轉時復原錯誤
- 測試容錯移轉如何影響現有的資料庫會話
- 確認容錯移轉是否因網路延遲的變更而變更端對端效能
- 在某些情況下，在查詢效能降低中，手動容錯移轉有助於減輕效能問題。

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>在 SQL 受控執行個體上起始手動容錯移轉

### <a name="using-powershell"></a>使用 PowerShell

Az. Sql 的最低版本必須是[v 2.9.0 版](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)。 請考慮使用一律具有最新 PowerShell 版本的 Azure 入口網站中的[Azure Cloud Shell](../../cloud-shell/overview.md) 。 

如需必要條件，請使用下列 PowerShell 腳本來安裝必要的 Azure 模組。 此外，請選取您想要容錯移轉受控執行個體所在的訂用帳戶。

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

使用 PowerShell 命令叫用[-AzSqlInstanceFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlinstancefailover)與下列範例來起始主要節點的容錯移轉，適用于 BC 和 GP 服務層。

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

使用下列 PS 命令來容錯移轉讀取次要節點，僅適用于 BC 服務層。

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>使用 CLI

請確定已安裝最新的 CLI 腳本。

使用 az sql mi 容錯移轉 CLI 命令搭配下列範例，以起始主要節點的容錯移轉（適用于 BC 和 GP 服務層）。

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

使用下列 CLI 命令來容錯移轉讀取次要節點，僅適用于 BC 服務層。

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>使用 Rest API

對於可能需要針對執行連續測試管線或自動化效能 mitigators 的 SQL 受控實例進行自動容錯移轉的高階使用者，可以透過 API 呼叫來起始容錯移轉來完成此功能。 如需詳細資訊，請參閱[受控實例-容錯移轉 REST API](https://docs.microsoft.com/rest/api/sql/managed%20instances%20-%20failover/failover) 。

若要使用 REST API 呼叫來起始容錯移轉，請先使用您選擇的 API 用戶端來產生驗證權杖。 產生的驗證權杖會當做 API 要求標頭中的 Authorization 屬性使用，而且是必要的。

下列程式碼是要呼叫的 API URI 範例：

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

下列屬性必須在 API 呼叫中傳遞：

| **API 屬性** | **參數** |
| --- | --- |
| subscriptionId | 已部署受控實例的訂用帳戶識別碼 |
| resourceGroupName | 包含受控實例的資源群組 |
| managedInstanceName | 受控實例的名稱 |
| replicaType |  (選擇性)  (主要或 ReadableSecondary) 。 這些參數代表要故障切換的複本類型：主要或可讀取的次要。 如果未指定，預設會在主要複本上起始容錯移轉。 |
| api-version | 靜態值，目前必須是 "2019-06-01-preview" |

API 回應將會是下列其中一種：

- 202 已接受
- 其中一個400要求錯誤。

您可以透過在回應標頭中審查 API 回應來追蹤作業狀態。 如需詳細資訊，請參閱[非同步 Azure 作業的狀態](../../azure-resource-manager/management/async-operations.md)。

## <a name="monitor-the-failover"></a>監視容錯移轉

若要監視使用者起始的手動容錯移轉進度，請在您最愛的用戶端中執行下列 T-SQL 查詢 (例如 SQL 受控執行個體上的 SSMS) 。 它會讀取實例上可用的系統檢視 sys.databases dm_hadr_fabric_replica_states 和報表複本。 在起始手動容錯移轉之後，重新整理相同的查詢。

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

在起始容錯移轉之前，您的輸出將會指出 BC 服務層上目前的主要複本，其中包含 AlwaysOn 可用性群組中的一個主要和三個次要資料庫。 執行容錯移轉時，再次執行此查詢將需要指出主要節點的變更。

您將無法看到與上述適用于 BC 的 GP 服務層相同的輸出。 這是因為 GP 服務層僅以單一節點為基礎。 GP 服務層級的 t-SQL 查詢輸出只會在容錯移轉之前和之後顯示單一節點。 在容錯移轉期間，用戶端的連線中斷（通常會在一分鐘內穩定）將會指出容錯移轉執行的指示。

> [!IMPORTANT]
> 使用者起始的手動容錯移轉功能限制包括：
> - 每隔30分鐘就會在相同的受控執行個體上起始一個 (1) 容錯移轉。
> - 對於 BC 實例，必須有複本的仲裁，才能接受容錯移轉要求。
> - 對於 BC 實例，無法指定要在哪個可讀取的次要複本上起始容錯移轉。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[AZURE SQL 受控執行個體](../database/high-availability-sla.md)的受控實例高可用性高可用性。
- 如需總覽，請參閱[什麼是 AZURE SQL 受控執行個體？](sql-managed-instance-paas-overview.md)。
