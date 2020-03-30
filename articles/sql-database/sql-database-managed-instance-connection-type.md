---
title: 託管實例連線類型
description: 瞭解託管實例連線類型
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 46223d1701b930d93de7c49c1e216a41045dda16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73819465"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Azure SQL 資料庫託管實例連線類型

本文介紹用戶端如何連接到 Azure SQL 資料庫託管實例，具體取決於連線類型。 下面提供了用於更改連線類型的腳本示例，以及與更改預設連接設置相關的注意事項。

## <a name="connection-types"></a>連線類型

Azure SQL 資料庫託管實例支援以下兩種連線類型：

- **重新導向 (建議使用)：** 用戶端直接與裝載資料庫的節點建立連線。 要使用重定向啟用連接，必須打開防火牆和網路安全性群組 （NSG） 才能允許在埠 1433 和 11000-11999 上訪問。 資料包直接轉到資料庫，因此使用重定向通過代理有延遲和輸送量性能改進。
- **代理（預設）：** 在此模式下，所有連接都使用代理閘道元件。 要啟用連接，只需打開私人網路絡埠 1433，需要打開用於公共連接的埠 3342。 視工作負載的本質而定，選擇此模式可能會導致延遲提高和輸送量降低。 為了將延遲降到最低及將輸送量提升到最高，強烈建議您採用 [重新導向] 連線原則，而不要採用 [Proxy] 連線原則。

## <a name="redirect-connection-type"></a>重定向連線類型

重定向連線類型意味著在 TCP 會話建立到 SQL 引擎後，用戶端會話從負載等化器獲取虛擬叢集節點的目標虛擬 IP。 後續資料包直接流向虛擬叢集節點，繞過閘道。 下圖說明此流量。

![重定向.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> 重定向連線類型當前僅適用于專用終結點。 無論連線類型設置如何，通過公共終結點的連接都將通過代理。

## <a name="proxy-connection-type"></a>代理連線類型

代理連線類型意味著使用閘道建立 TCP 會話，並且所有後續資料包都流經它。 下圖說明此流量。

![代理.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>使用 PowerShell 更改連線類型設置的腳本

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下 PowerShell 腳本演示如何將託管實例的連線類型更改為重定向。

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

- [將資料庫還原到託管實例](sql-database-managed-instance-get-started-restore.md)
- 瞭解如何[在託管實例上配置公共終結點](sql-database-managed-instance-public-endpoint-configure.md)
- 瞭解[託管實例連接體系結構](sql-database-managed-instance-connectivity-architecture.md)