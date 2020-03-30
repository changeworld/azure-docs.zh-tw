---
title: 為 Azure SQL 資料庫託管實例啟用服務輔助子網配置
description: 為 Azure SQL 資料庫託管實例啟用服務輔助子網配置
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533261"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>為 Azure SQL 資料庫託管實例啟用服務輔助子網配置
服務輔助子網配置為託管託管實例的子網提供自動化網路設定管理。 通過服務輔助子網配置，使用者可以完全控制對資料的訪問（TDS 流量流），而託管實例則負責確保管理流量的不間斷流動，以實現 SLA。

自動設定的網路安全性群組和路由表規則對客戶可見，並帶有首碼_Microsoft.Sql-managedInstances_UseOnly_*的注釋。

打開`Microsoft.Sql/managedInstances`資來源提供者的[子網委派](../virtual-network/subnet-delegation-overview.md)後，將自動啟用服務輔助配置。

> [!IMPORTANT] 
> 啟用子網委派後，在從子網中刪除最後一個虛擬叢集之前，無法將其關閉。 有關如何刪除虛擬叢集的更多詳細資訊，請參閱以下[文章](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal)。

> [!NOTE] 
> 由於服務輔助子網配置是維護 SLA 的基本功能，因此從 2020 年 5 月 1 日起，將無法在未委派給託管實例資來源提供者的子網中部署託管實例。 2020 年 7 月 1 日，包含託管實例的所有子網將自動委派給託管實例資來源提供者。 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>為新部署啟用子網委派
要將託管實例部署到空子網，您需要將其委託給`Microsoft.Sql/managedInstances`資來源提供者，如下[文所述](../virtual-network/manage-subnet-delegation.md)。 _請注意，引用的文章使用`Microsoft.DBforPostgreSQL/serversv2`資來源提供者例如。您需要改用`Microsoft.Sql/managedInstances`資來源提供者。_

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>為現有部署啟用子網委派

為了為現有託管實例部署啟用子網委派，您需要找出虛擬網路子網的放置位置。 

要瞭解這一點，`Virtual network/subnet`您可以在託管`Overview`實例的門戶邊欄選項卡上進行檢查。

作為替代方法，您可以運行以下 PowerShell 命令來瞭解這一點。 將**訂閱 ID**替換為訂閱 ID。 還將**rg 名稱**替換為託管實例的資源組，並將**mi 名稱**替換為託管實例的名稱。

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

找到託管實例子網後，需要將其委託給`Microsoft.Sql/managedInstances`資來源提供者，如下[文所述](../virtual-network/manage-subnet-delegation.md)。 _請注意，引用的文章使用`Microsoft.DBforPostgreSQL/serversv2`資來源提供者例如。您需要改用`Microsoft.Sql/managedInstances`資來源提供者。_


> [!IMPORTANT]
> 啟用服務輔助配置不會導致子網中已有的託管實例的容錯移轉或連接中斷。
