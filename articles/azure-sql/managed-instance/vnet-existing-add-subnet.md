---
title: 設定現有的虛擬網路
titleSuffix: Azure SQL Managed Instance
description: 本文說明如何設定現有的虛擬網路和子網，您可以在其中部署 Azure SQL 受控執行個體。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 5d91fc0d5dd51ea5ef766f23519f38202b24a337
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711301"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>為 Azure SQL 受控執行個體設定現有的虛擬網路
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體必須部署在 Azure[虛擬網路](../../virtual-network/virtual-networks-overview.md)內，以及僅供受控實例專用的子網中。 您可以使用現有的虛擬網路和子網（如果它們是根據[SQL 受控執行個體虛擬網路需求](connectivity-architecture-overview.md#network-requirements)進行設定）。

如果下列案例的其中一個適用於您，您就可以使用本文中說明的指令碼來驗證及修改您的網路：

- 您有一個尚未設定的子網路。
- 您不確定該子網路是否符合[需求](connectivity-architecture-overview.md#network-requirements)。
- 您想要檢查該子網路在您進行變更之後仍然符合[網路需求](connectivity-architecture-overview.md#network-requirements)。

> [!Note]
> 您只能在透過 Azure Resource Manager 部署模型建立的虛擬網路中建立受控實例。 不支援透過傳統部署模型建立的 Azure 虛擬網路。 遵循[判斷 SQL 受控執行個體的子網大小一](vnet-subnet-determine-size.md)文中的指導方針來計運算元網大小。 在其中部署資源後，就無法再調整子網路的大小。
>
> 建立受控實例之後，不支援將實例或 VNet 移至另一個資源群組或訂用帳戶。

## <a name="validate-and-modify-an-existing-virtual-network"></a>驗證與修改現有的虛擬網路

如果您想要在現有子網內建立受控實例，我們建議使用下列 PowerShell 腳本來準備子網：

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

1. 驗證：它會驗證所選的虛擬網路和子網是否有 SQL 受控執行個體網路需求。
2. 確認：它會向使用者顯示一組需要進行的變更，以準備 SQL 受控執行個體部署的子網。 它也會要求同意。
3. 準備：它會適當地設定虛擬網路和子網。

## <a name="next-steps"></a>後續步驟

- 如需總覽，請參閱[什麼是 SQL 受控執行個體？](sql-managed-instance-paas-overview.md)。
- 如需示範如何建立虛擬網路、建立受控實例，以及從資料庫備份還原資料庫的教學課程，請參閱[建立受控實例](instance-create-quickstart.md)。
- 如需 DNS 問題，請參閱設定[自訂 DNS](custom-dns-configure.md)。
