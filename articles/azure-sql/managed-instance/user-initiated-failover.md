---
title: 在 SQL 受控執行個體上手動起始容錯移轉
description: 瞭解如何在 Azure SQL 受控執行個體上手動容錯移轉主要和次要複本。
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 08/31/2020
ms.openlocfilehash: ebf36c99e6c4dd636c41086d4c72fd6761f6d5ca
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791625"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>SQL 受控執行個體上使用者起始的手動容錯移轉

本文說明如何在 SQL 受控執行個體一般用途 (GP) 和業務關鍵 (BC) 服務層級，手動容錯移轉主要節點，以及如何在 BC 服務層上手動容錯移轉次要唯讀複本節點。

## <a name="when-to-use-manual-failover"></a>使用手動容錯移轉的時機

[高可用性](../database/high-availability-sla.md) 是 SQL 受控執行個體平臺的基本部分，可針對您的資料庫應用程式以透明方式運作。 在節點降級或錯誤偵測時，從主要節點容錯移轉至次要節點，或在一般每月軟體更新期間，在 Azure 中使用 SQL 受控執行個體的所有應用程式都必須發生此情況。

基於下列原因，您可能會考慮在 SQL 受控執行個體上執行 [手動容錯移轉](../database/high-availability-sla.md#testing-application-fault-resiliency) ：
- 在部署至生產環境之前，測試容錯移轉復原的應用程式
- 測試端對端系統，以在自動容錯移轉時復原錯誤
- 測試容錯移轉如何影響現有的資料庫會話
- 確認容錯移轉是否因為網路延遲的變更而變更端對端效能
- 在查詢效能降低的某些情況下，手動容錯移轉有助於減輕效能問題。

> [!NOTE]
> 在部署至生產環境之前，確保您的應用程式具有容錯移轉復原能力，有助於降低生產環境中應用程式錯誤的風險，並且會為您的客戶提供應用程式可用性。

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>在 SQL 受控執行個體上起始手動容錯移轉

### <a name="rbac-permissions-required"></a>需要 RBAC 許可權

起始容錯移轉的使用者必須具有下列其中一個 RBAC 角色：

- 訂用帳戶擁有者角色，或
- 受控執行個體參與者角色，或
- 具有下列許可權的自訂角色：
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>使用 PowerShell

Az. Sql 的最小版本必須是 [v 2.9.0 版](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)。 請考慮使用一律具有最新 PowerShell 版本的 Azure 入口網站 [Azure Cloud Shell](../../cloud-shell/overview.md) 。 

作為必要條件，請使用下列 PowerShell 腳本來安裝所需的 Azure 模組。 此外，請選取您想要容錯移轉受控執行個體所在的訂用帳戶。

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

使用 PowerShell 命令 [調用-AzSqlInstanceFailover](/powershell/module/az.sql/invoke-azsqlinstancefailover) 搭配下列範例，以起始主要節點的容錯移轉，同時適用于 BC 和 GP 服務層級。

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

使用下列 PS 命令以容錯移轉讀取次要節點（僅適用于 BC 服務層）。

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>使用 CLI

確定已安裝最新的 CLI 腳本。

使用 az sql mi 容錯移轉 CLI 命令搭配下列範例，以起始主要節點的容錯移轉，同時適用于 BC 和 GP 服務層級。

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

使用下列 CLI 命令來容錯移轉讀取次要節點（僅適用于 BC 服務層）。

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>使用 Rest API

針對可能需要自動容錯移轉其 SQL 受控實例以進行持續測試管線，或自動化效能 mitigators 的 advanced 使用者，此函式可以透過 API 呼叫來初始容錯移轉來完成。 如需詳細資料，請參閱 [受控實例-容錯移轉 REST API](/rest/api/sql/managed%20instances%20-%20failover/failover) 。

若要使用 REST API 呼叫來起始容錯移轉，請先使用您選擇的 API 用戶端來產生驗證權杖。 產生的驗證權杖會在 API 要求標頭中用來做為授權屬性，而且是必要的。

下列程式碼是要呼叫的 API URI 範例：

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

下列屬性必須在 API 呼叫中傳遞：

| **API 屬性** | **參數** |
| --- | --- |
| subscriptionId | 部署受控實例的訂用帳戶識別碼 |
| resourceGroupName | 包含受控實例的資源群組 |
| managedInstanceName | 受控實例的名稱 |
| replicaType |  (選用)  (主要或 ReadableSecondary) 。 這些參數代表要進行容錯移轉的複本類型：主要或可讀取的次要資料庫。 如果未指定，則預設會在主要複本上起始容錯移轉。 |
| api-version | 靜態值，目前必須是 "2019-06-01-preview" |

API 回應會是下列其中一種：

- 202 已接受
- 400要求的其中一個錯誤。

您可以透過審核回應標頭中的 API 回應來追蹤作業狀態。 如需詳細資訊，請參閱 [非同步 Azure 作業的狀態](../../azure-resource-manager/management/async-operations.md)。

## <a name="monitor-the-failover"></a>監視容錯移轉

若要監視使用者起始的手動容錯移轉進度，請在您最愛的用戶端中執行下列 T-SQL 查詢 (例如 SQL 受控執行個體上的 SSMS) 。 它會讀取 [系統檢視] sys.dm_hadr_fabric_replica_states，以及實例上可用的報表複本。 初始化手動容錯移轉之後，請重新整理相同的查詢。

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

在起始容錯移轉之前，您的輸出將會指出 BC 服務層上目前的主要複本，其中包含 AlwaysOn 可用性群組中的一個主要和三個次要資料庫。 執行容錯移轉時，再次執行此查詢需要指出主要節點的變更。

您將看不到與上述的「BC 服務層」相同的輸出，如下所示。 這是因為 GP 服務層級只以單一節點為基礎。 GP 服務層級的 t-SQL 查詢輸出只會顯示容錯移轉之前和之後的單一節點。 在容錯移轉期間從您的用戶端中斷連線（通常會在一分鐘內持續執行）將會是容錯移轉執行的指示。

> [!NOTE]
> 完成容錯移轉程式 (不是實際的短時間無法使用) 可能需要幾分鐘的時間來處理 **高強度** 的工作負載。 這是因為實例引擎會處理主要節點上的所有目前交易，並在容錯移轉之前趕上次要節點上的所有交易。

> [!IMPORTANT]
> 使用者起始的手動容錯移轉的功能限制如下：
> - 每隔 **30 分鐘** 可能會有一個 (1) 容錯移轉起始于相同的受控執行個體。
> - 對於 BC 實例，必須有複本的仲裁，才能接受容錯移轉要求。
> - 對於 BC 實例，無法指定要在哪個可讀取次要複本上起始容錯移轉。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [AZURE SQL 受控執行個體受控實例高可用性](../database/high-availability-sla.md)的高可用性。
- 如需總覽，請參閱 [什麼是 AZURE SQL 受控執行個體？](sql-managed-instance-paas-overview.md)。