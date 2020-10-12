---
title: 連線類型
titleSuffix: Azure SQL Managed Instance
description: 瞭解 Azure SQL 受控執行個體連線類型
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f1c4fe8268d24026609f55d76a102a5c9a4e8295
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91356293"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Azure SQL 受控執行個體連線類型
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文說明用戶端如何根據連線類型連接至 Azure SQL 受控執行個體。 以下提供變更連線類型的腳本範例，以及變更預設連線設定的相關考慮。

## <a name="connection-types"></a>連線類型

Azure SQL 受控執行個體支援以下兩種連線類型：

- 重新**導向 (建議的) ：** 用戶端會直接與裝載資料庫的節點建立連接。 若要使用重新導向來啟用連線，您必須開啟防火牆和網路安全性群組 (NSG)，以允許在連接埠 1433 和 11000-11999 上進行存取。 封包會直接移至資料庫，因此使用「透過 Proxy 重新導向」可改善延遲和輸送量效能。
- **Proxy (預設) ：** 在此模式中，所有連接都會使用 proxy 閘道元件。 若要啟用連線，只需要開啟私人網路的連接埠 1433 和公用連線的連接埠 3342。 視工作負載的本質而定，選擇此模式可能會導致延遲提高和輸送量降低。 為了將延遲降到最低及將輸送量提升到最高，強烈建議您採用重新導向連線原則，而不要採用 Proxy 連線原則。

## <a name="redirect-connection-type"></a>重新導向連線類型

在重新導向連線類型中，在建立 SQL 引擎的 TCP 會話之後，用戶端會話會從負載平衡器取得虛擬叢集節點的目的地虛擬 IP。 後續的封包會直接流向虛擬叢集節點，略過閘道。 下圖說明此流量。

![圖表顯示內部部署網路，該網路已連線到 Azure 虛擬網路中的閘道，以及連線到虛擬網路中資料庫主要節點的重新導向查詢。](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> 重新導向連線類型目前僅適用于私人端點。 無論連線類型設定為何，透過公用端點的連接都會通過 proxy。

## <a name="proxy-connection-type"></a>Proxy 連線類型

在 proxy 連線類型中，會使用閘道建立 TCP 會話，且所有後續的封包都會流經它。 下圖說明此流量。

![圖表顯示內部部署網路，其中 proxy 已連線到 Azure 虛擬網路中的閘道，請在虛擬網路中的資料庫主要節點旁進行連線。](./media/connection-types-overview/proxy.png)

## <a name="changing-connection-type"></a>變更連線類型

- **使用入口網站：** 若要使用 Azure 入口網站來變更連線類型，請開啟 [虛擬網路] 頁面，並使用 [連線 **類型** ] 設定來變更連線類型，並儲存變更。

- **使用 PowerShell 變更連線類型設定的腳本：**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

下列 PowerShell 腳本顯示如何將受控實例的連線類型變更為 `Redirect` 。

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

- [將資料庫還原至 SQL 受控執行個體](restore-sample-database-quickstart.md)
- 瞭解如何 [在 SQL 受控執行個體上設定公用端點](public-endpoint-configure.md)
- 瞭解 [SQL 受控執行個體連接架構](connectivity-architecture-overview.md)
