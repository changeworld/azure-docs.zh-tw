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
ms.openlocfilehash: e46c6d1c14d226522a1d534418b91076efeaaccf
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070712"
---
# <a name="azure-sql-managed-instance-connection-types"></a>Azure SQL 受控執行個體連線類型
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文說明用戶端如何根據連線類型連接至 Azure SQL 受控執行個體。 以下提供變更連線類型的腳本範例，以及變更預設連線設定的相關考慮。

## <a name="connection-types"></a>連線類型

Azure SQL 受控執行個體支援以下兩種連線類型：

- **重新導向 (建議使用)：** 用戶端直接與裝載資料庫的節點建立連線。 若要啟用使用重新導向的連線，您必須開啟防火牆和網路安全性群組 (NSG) ，以允許在埠1433和11000-11999 上進行存取。 封包會直接傳送到資料庫，因此會使用重新導向 proxy 的延遲和輸送量效能改進。
- **Proxy (預設) ：** 在此模式中，所有連接都會使用 proxy 閘道元件。 若要啟用連線，只需要開啟私人網路的埠1433和公用連接的埠3342。 視工作負載的本質而定，選擇此模式可能會導致延遲提高和輸送量降低。 我們強烈建議透過 proxy 連線原則來進行重新導向連線原則，以獲得最低的延遲和最高的輸送量。

## <a name="redirect-connection-type"></a>重新導向連線類型

在重新導向連線類型中，在建立 SQL 引擎的 TCP 會話之後，用戶端會話會從負載平衡器取得虛擬叢集節點的目的地虛擬 IP。 後續的封包會直接流向虛擬叢集節點，略過閘道。 下圖說明此流量。

![redirect.png](./media/connection-types-overview/redirect.png)

> [!IMPORTANT]
> 重新導向連線類型目前僅適用于私人端點。 無論連線類型設定為何，透過公用端點的連接都會通過 proxy。

## <a name="proxy-connection-type"></a>Proxy 連線類型

在 proxy 連線類型中，會使用閘道建立 TCP 會話，且所有後續的封包都會流經它。 下圖說明此流量。

![proxy.png](./media/connection-types-overview/proxy.png)

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
