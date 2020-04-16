---
title: 連線架構
description: 本文件介紹 Azure 內部或 Azure 外部的資料庫連接的 Azure SQL 連接體系結構。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: 2028aac9c01aedc4baa568d370c9f7d21c920647
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419258"
---
# <a name="azure-sql-connectivity-architecture"></a>Azure SQL 連線架構
> [!NOTE]
> 本文適用於 Azure SQL Server，以及在 Azure SQL Server 上建立的 SQL Database 和 SQL 資料倉儲資料庫。 為了簡單起見，參考 SQL Database 和 SQL 資料倉儲時都會使用 SQL Database。

> [!IMPORTANT]
> 本文「不」** 適用於 **Azure SQL Database 受控執行個體**。 請參考[託管實體的連線架構結構](sql-database-managed-instance-connectivity-architecture.md)。

本文介紹將網路流量定向到 Azure SQL 資料庫或 SQL 數據倉庫的各種元件的體系結構。 它還解釋了不同的連接策略,以及它如何影響從 Azure 內部連接的用戶端和從 Azure 外部連接的用戶端。 

## <a name="connectivity-architecture"></a>連線架構

下圖提供 Azure SQL Database 連線架構的高階概觀。

![架構概觀](./media/sql-database-connectivity-architecture/connectivity-overview.png)

下列步驟會說明如何建立至 Azure SQL 資料庫的連線：

- 用戶端會連線到具有公用 IP 位址並在連接埠 1433 進行接聽的閘道。
- 此閘道會視有效的連線原則而定，將流量重新導向或透過 Proxy 傳送到正確的資料庫叢集。
- 在資料庫叢集內部，流量會轉送到適當的 Azure SQL 資料庫。

## <a name="connection-policy"></a>連線原則

Azure SQL Database 支援下列三個 SQL Database 伺服器連線原則設定選項：

- **重定向(推薦):** 用戶端直接建立與託管資料庫的節點的連接,從而降低延遲並提高輸送量。 要使用此模式的連線,用戶端需要:
   - 允許從用戶端向區域中所有 Azure SQL IP 位址進行 11000 11999 範圍內的所有 Azure SQL IP 位址通訊。 使用 SQL 的服務標記使其更易於管理。  
   - 允許從用戶端到埠 1433 上的 Azure SQL 資料庫閘道 IP 位址的出站通訊。

- **代理:** 在此模式下,所有連接都通過 Azure SQL 資料庫閘道進行接近,從而導致延遲增加和輸送量降低。 要使用此模式的連接,用戶端需要允許從用戶端到埠 1433 上的 Azure SQL 資料庫閘道 IP 位址的出站通信。

- **預設值:** 這是建立後對所有伺服器上有效的連線策略,除非您顯式將連線策略更改為或`Proxy``Redirect`。 預設策略適用於`Redirect`源自 Azure 內部的所有用戶端連接(例如,來自 Azure`Proxy`虛擬機器)和源自外部的所有用戶端連接(例如,來自本地工作站的連接)。

 我們強烈建議通過`Redirect``Proxy`連接策略對連接策略進行連接策略,以提供最低延遲和最高輸送量。但是,您需要滿足上述允許網路流量的其他要求。 如果用戶端是 Azure 虛擬機器,則可以使用具有[服務標記](../virtual-network/security-overview.md#service-tags)的網路安全組 (NSG) 完成此目的。 如果用戶端正在本地工作站進行連接,則可能需要與網路管理員合作,允許網路流量通過公司防火牆。

## <a name="connectivity-from-within-azure"></a>從 Azure 內部連線

如果您從 Azure 內部連線，該連線預設的連線原則為 `Redirect`。 `Redirect` 原則代表在和 Azure SQL Database 建立 TCP 工作階段之後，會將其 Azure SQL Database 閘道的目的地虛擬 IP 變更為該叢集的 Azure SQL Database 閘道，將該用戶端工作階段重新導向至正確的資料庫叢集。 此後，所有後續封包會略過 Azure SQL Database 閘道，直接流經叢集。 下圖說明此流量。

![架構概觀](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>從 Azure 外部連線

如果您從 Azure 外部連線，該連線預設的連線原則為 `Proxy`。 `Proxy` 原則代表會透過 Azure SQL Database 閘道建立 TCP 工作階段，且所有後續封包都會流經閘道。 下圖說明此流量。

![架構概觀](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> 此外,打開連接埠 14000-14999,啟用[與 DAC 的連線](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL Database 閘道 IP 位址

下表按區域列出了閘道的 IP 位址。 要連接到 Azure SQL 資料庫,需要允許網路流量從該區域**的所有**閘道&。

有關如何將流量遷移到特定區域中的新閘道的詳細資訊,請於以下文章[:Azure SQL 資料庫流量遷移到較新的閘道](sql-database-gateway-migration.md)


| 區域名稱          | 閘道 IP 位址 |
| --- | --- |
| 澳大利亞中部    | 20.36.105.0 |
| 澳大利亞中部2   | 20.36.113.0 |
| 澳大利亞東部       | 13.75.149.87, 40.79.161.1 |
| 澳大利亞東南部 | 191.239.192.109, 13.73.109.251 |
| 巴西南部         | 104.41.11.5, 191.233.200.14 |
| 加拿大中部       | 40.85.224.249      |
| 加拿大東部          | 40.86.226.166      |
| 美國中部           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| 中國東部           | 139.219.130.35     |
| 中國東部 2         | 40.73.82.1         |
| 中國北部          | 139.219.15.17      |
| 中國北部 2        | 40.73.50.0         |
| 東亞            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| 美國東部              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| 美國東部 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| 法國中部       | 40.79.137.0, 40.79.129.1 |
| 德國中部      | 51.4.144.100       |
| 德國東北部   | 51.5.144.179       |
| 印度中部        | 104.211.96.159     |
| 印度南部          | 104.211.224.146    |
| 印度西部           | 104.211.160.80     |
| 日本東部           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| 日本西部           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| 南韓中部        | 52.231.32.42       |
| 南韓南部          | 52.231.200.86      |
| 美國中北部     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| 北歐         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| 挪威東部          | 51.120.96.0        |
| 挪威西部          | 51.120.216.0       |
| 南非北部   | 102.133.152.0      |
| 南非西部    | 102.133.24.0       |
| 美國中南部     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| 東南亞      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| 阿拉伯聯合大公國中部          | 20.37.72.64        |
| 阿拉伯聯合大公國北部            | 65.52.248.0        |
| 英國南部             | 51.140.184.11      |
| 英國西部              | 51.141.8.11        |
| 美國中西部      | 13.78.145.25       |
| 西歐          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| 美國西部              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| 美國西部 2            | 13.66.226.202      |
|                      |                    |



## <a name="next-steps"></a>後續步驟

- 如需如何變更 Azure SQL Database 伺服器的 Azure SQL Database 連線原則相關資訊，請參閱 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) \(英文\)。
- 如需使用 ADO.NET 4.5 或更新版本用戶端之 Azure SQL Database 連接行為的詳細資訊，請參閱 [ADO.NET 4.5 超過 1433以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)。
- 如需一般應用程式開發概觀的資訊，請參閱 [SQL Database 應用程式開發概觀](sql-database-develop-overview.md)。
