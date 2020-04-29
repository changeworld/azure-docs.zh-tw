---
title: 啟用 Azure SQL Database 受控執行個體的服務輔助子網設定
description: 啟用 Azure SQL Database 受控執行個體的服務輔助子網設定
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79533261"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>啟用 Azure SQL Database 受控執行個體的服務輔助子網設定
服務輔助子網設定為裝載受控實例的子網提供自動化的網路設定管理。 使用服務輔助子網設定使用者可完全控制資料的存取權（TDS 流量），而受控實例則負責確保管理流量不中斷，以履行 SLA。

客戶可以看到自動設定的網路安全性群組和路由表規則，並以前置詞_Microsoft. Sql-managedInstances_UseOnly__ 標注。

當您開啟資源提供者的`Microsoft.Sql/managedInstances` [子網委派](../virtual-network/subnet-delegation-overview.md)時，會自動啟用服務輔助設定。

> [!IMPORTANT] 
> 一旦開啟子網委派之後，您就無法將它關閉，直到您從子網移除最後一個虛擬叢集為止。 如需有關如何刪除虛擬叢集的詳細資訊，請參閱下列[文章](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal)。

> [!NOTE] 
> 因為服務輔助子網設定是維護 SLA 的基本功能，從5月 2020 1 日開始，將無法在未委派給受控實例資源提供者的子網中部署受控實例。 2020年7月1日所有包含受控實例的子網，都會自動委派給受控實例資源提供者。 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>啟用新部署的子網委派
若要將受控實例部署至空白子網，您需要將`Microsoft.Sql/managedInstances`其委派給資源提供者，如下列[文章](../virtual-network/manage-subnet-delegation.md)所述。 _請注意，參考的文章`Microsoft.DBforPostgreSQL/serversv2`會使用資源提供者，例如。您必須改用`Microsoft.Sql/managedInstances`資源提供者。_

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>啟用現有部署的子網委派

若要為現有的受控實例部署啟用子網委派，您需要找出放置虛擬網路子網的位置。 

若要瞭解這一點， `Virtual network/subnet`您可以`Overview`在受控實例的入口網站分頁中檢查。

或者，您可以執行下列 PowerShell 命令來瞭解這一點。 將**訂**用帳戶識別碼取代為您的訂用帳戶識別碼。 同時，將**rg 名稱**取代為受控實例的資源群組，並將**mi 名稱**取代為受控實例的名稱。

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

找到受控實例子網之後，您必須將其委派`Microsoft.Sql/managedInstances`給資源提供者，如下列[文章](../virtual-network/manage-subnet-delegation.md)所述。 _請注意，參考的文章`Microsoft.DBforPostgreSQL/serversv2`會使用資源提供者，例如。您必須改用`Microsoft.Sql/managedInstances`資源提供者。_


> [!IMPORTANT]
> 啟用服務輔助設定並不會對已在子網中的受控實例造成容錯移轉或中斷。
