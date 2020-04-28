---
title: 受控實例連線類型
description: 瞭解受控實例連線類型
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73819465"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Azure SQL Database 受控實例連線類型

本文說明用戶端如何根據連線類型，連接到 Azure SQL Database 受控實例。 以下提供變更連線類型的腳本範例，以及變更預設連線設定的相關考慮。

## <a name="connection-types"></a>連線類型

Azure SQL Database 受控實例支援下列兩種連線類型：

- **重新導向 (建議使用)：** 用戶端直接與裝載資料庫的節點建立連線。 若要使用 [重新導向] 啟用連線，您必須開啟防火牆和網路安全性群組（NSG），以允許在埠1433和11000-11999 上進行存取。 封包會直接移至資料庫，因此使用 [透過 Proxy 重新導向] 會改善延遲和輸送量效能。
- **Proxy （預設值）：** 在此模式中，所有連接都使用 proxy 閘道元件。 若要啟用連線，只需要開啟私人網路的埠1433和公用連接的埠3342。 視工作負載的本質而定，選擇此模式可能會導致延遲提高和輸送量降低。 為了將延遲降到最低及將輸送量提升到最高，強烈建議您採用 [重新導向] 連線原則，而不要採用 [Proxy] 連線原則。

## <a name="redirect-connection-type"></a>重新導向連線類型

[重新導向連線類型] 表示在 TCP 會話建立至 SQL 引擎之後，用戶端會話會從負載平衡器取得虛擬叢集節點的目的地虛擬 IP。 後續的封包會直接流向虛擬叢集節點，略過閘道。 下圖說明此流量。

![重新導向 .png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> 重新導向連線類型目前僅適用于私人端點。 無論連線類型設定為何，透過公用端點的連線都會透過 proxy。

## <a name="proxy-connection-type"></a>Proxy 連線類型

Proxy 連線類型表示會使用閘道建立 TCP 會話，且所有後續封包都會流經它。 下圖說明此流量。

![proxy .png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>使用 PowerShell 變更連線類型設定的腳本

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

下列 PowerShell 腳本顯示如何將受控實例的連線類型變更為 [重新導向]。

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>後續步驟

- [將資料庫還原到受控實例](sql-database-managed-instance-get-started-restore.md)
- 瞭解如何[在受控實例上設定公用端點](sql-database-managed-instance-public-endpoint-configure.md)
- 瞭解[受控實例連線架構](sql-database-managed-instance-connectivity-architecture.md)