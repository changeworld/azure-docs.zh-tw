---
title: 為受控實例設定現有的虛擬網路
description: 本文描述如何設定可以在其中部署 Azure SQL Database 受控執行個體的現有虛擬網路和子網路。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 50b832baa9253f47b5f10980ae1764c9425ed4d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476944"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>針對 Azure SQL Database 受控執行個體設定現有虛擬網路

Azure SQL Database 受控執行個體必須部署在 Azure [虛擬網路](../virtual-network/virtual-networks-overview.md)以及受控執行個體專用的子網路內。 如果您的現有虛擬網路和子網路是按照[受控執行個體虛擬網路需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)來設定的，您就可以使用它們。

如果下列案例的其中一個適用於您，您就可以使用本文中說明的指令碼來驗證及修改您的網路：

- 您有一個尚未設定的子網路。
- 您不確定該子網路是否符合[需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。
- 您想要檢查該子網路在您進行變更之後仍然符合[網路需求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。

> [!Note]
> 您只能在透過 Azure Resource Manager 部署模型建立的虛擬網路中建立受控執行個體。 不支援透過傳統部署模型建立的 Azure 虛擬網路。 藉由遵循[決定受控執行個體的子網路大小](sql-database-managed-instance-determine-size-vnet-subnet.md)一文中的指導方針來計算子網路大小。 在其中部署資源後，就無法再調整子網路的大小。
>
> 建立受控實例之後，不支援將受控實例或 VNet 移至另一個資源群組或訂用帳戶。

## <a name="validate-and-modify-an-existing-virtual-network"></a>驗證與修改現有的虛擬網路

如果您想要在現有子網路內建立受控執行個體，我們建議使用下列 PowerShell 指令碼來準備子網路：

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

指令碼經由三個步驟準備子網路：

1. 驗證：它會驗證選取的虛擬網路和子網是否有受控執行個體網路需求。
2. 確認：它會向使用者顯示一組需要進行的變更，以準備受控執行個體部署的子網。 它也會要求同意。
3. 準備：它會適當地設定虛擬網路和子網。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱[受控執行個體是什麼](sql-database-managed-instance.md)。
- 如需示範如何建立虛擬網路、建立受控執行個體，以及從資料庫備份還原資料庫的教學課程，請參閱[建立 Azure SQL Database 受控執行個體](sql-database-managed-instance-get-started.md)。
- 如需 DNS 問題，請參閱設定[自訂 DNS](sql-database-managed-instance-custom-dns.md)。
