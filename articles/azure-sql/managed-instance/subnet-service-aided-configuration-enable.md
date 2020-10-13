---
title: 啟用 Azure SQL 受控執行個體的服務輔助子網設定
description: 啟用 Azure SQL 受控執行個體的服務輔助子網設定
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: 67b398194d9094cd99fccaa85ed0df3be362ce2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618007"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-managed-instance"></a>啟用 Azure SQL 受控執行個體的服務輔助子網設定
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

服務輔助子網設定可為裝載受控實例的子網提供自動化的網路設定管理。 使用服務輔助子網設定時，使用者可以完全掌控 (TDS 流量) 流程的資料存取，而受控實例則負責確保管理流量不中斷流動，以履行 SLA。

客戶可以看到自動設定的網路安全性群組和路由表規則，並使用前置詞 _Microsoft. Sql-managedInstances_UseOnly__ 進行批註。

當您開啟資源提供者的 [子網委派](../../virtual-network/subnet-delegation-overview.md) 時，即會自動啟用服務輔助設定 `Microsoft.Sql/managedInstances` 。

> [!IMPORTANT] 
> 一旦開啟子網委派之後，您就無法將它關閉，直到您從子網中移除最後一個虛擬叢集為止。 如需有關如何刪除虛擬叢集的詳細資訊，請參閱下列 [文章](virtual-cluster-delete.md#delete-a-virtual-cluster-from-the-azure-portal)。

> [!NOTE] 
> 服務輔助子網設定是維護 SLA 的基本功能，從2020年5月1日開始，將無法在未委派給受控實例資源提供者的子網中部署受控實例。 從2020年7月1日起，所有包含受控實例的子網都會自動委派給受控實例資源提供者。 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>啟用新部署的子網委派
若要將受控實例部署到空的子網，您必須將其委派給 `Microsoft.Sql/managedInstances` 資源提供者，如下列 [文章](../../virtual-network/manage-subnet-delegation.md)所述。 _請注意，參考的文章會使用 `Microsoft.DBforPostgreSQL/serversv2` 資源提供者，例如。您必須改為使用 `Microsoft.Sql/managedInstances` 資源提供者。_

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>啟用現有部署的子網委派

若要針對您現有的受控實例部署啟用子網委派，您需要找出放置該虛擬網路的虛擬網路子網。 

若要瞭解這一點，您可以 `Virtual network/subnet` 在 `Overview` 受控實例的入口網站分頁中檢查。

或者，您可以執行下列 PowerShell 命令來瞭解這一點。 將 **訂** 用帳戶識別碼取代為您的訂用帳戶識別碼。 也請將 **rg 名稱** 取代為您受控實例的資源群組，並將 **mi 名稱** 取代為您受控實例的名稱。

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

一旦您找到受控實例子網，您必須將其委派給 `Microsoft.Sql/managedInstances` 資源提供者，如下列 [文章](../../virtual-network/manage-subnet-delegation.md)所述。 _請注意，參考的文章會使用 `Microsoft.DBforPostgreSQL/serversv2` 資源提供者，例如。您必須改為使用 `Microsoft.Sql/managedInstances` 資源提供者。_


> [!IMPORTANT]
> 針對已在子網中的受控實例，啟用服務輔助設定不會導致容錯移轉或中斷連線。
