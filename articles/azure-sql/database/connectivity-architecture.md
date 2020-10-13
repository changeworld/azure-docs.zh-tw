---
title: Azure SQL Database 連線架構
description: 本檔說明從 Azure 內部或從 Azure 外部進行資料庫連線的 Azure SQL Database 連線架構。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 06/26/2020
ms.openlocfilehash: 71bd250cbfb2642a291d495273c4cd66ebb2c350
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325380"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Azure SQL Database 與 Azure Synapse Analytics 連線架構
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文說明各種元件的架構，這些元件會將網路流量導向 Azure SQL Database 或 Azure Synapse Analytics 的伺服器。 它也會說明不同的連線原則，以及它如何影響從 azure 內部連線的用戶端和從 Azure 外部連線的用戶端。

> [!IMPORTANT]
> 本文「不」適用於 **Azure SQL Database 受控執行個體**。 請參閱 [受控實例](../managed-instance/connectivity-architecture-overview.md)的連線架構。

## <a name="connectivity-architecture"></a>連線架構

下圖提供連線架構的概要說明。

![架構概觀](./media/connectivity-architecture/connectivity-overview.png)

下列步驟說明如何建立與 Azure SQL Database 的連接：

- 用戶端會連線到具有公用 IP 位址並在連接埠 1433 進行接聽的閘道。
- 此閘道會視有效的連線原則而定，將流量重新導向或透過 Proxy 傳送到正確的資料庫叢集。
- 在資料庫叢集中，會將流量轉送到適當的資料庫。

## <a name="connection-policy"></a>連線原則

SQL Database 和 Azure Synapse 中的伺服器支援伺服器連線原則設定的下列三個選項：

- 重新**導向 (建議的) ：** 用戶端會直接與裝載資料庫的節點建立連線，進而降低延遲並提高輸送量。 針對使用此模式的連接，用戶端必須：
  - 允許從用戶端到 11000 11999 範圍內埠上的所有 Azure SQL IP 位址的輸出通訊。 使用 SQL 的服務標籤可讓您更輕鬆地管理這些標記。  
  - 允許從用戶端到埠1433上 Azure SQL Database 閘道 IP 位址的輸出通訊。

- **Proxy：** 在此模式中，所有連線都會透過 Azure SQL Database 閘道進行 proxy，進而提高延遲和降低輸送量。 針對使用此模式的連線，用戶端必須允許從用戶端到埠1433上 Azure SQL Database 閘道 IP 位址的輸出通訊。

- **預設值：** 除非您明確地將連線原則變更為或，否則在建立之後，這是在所有伺服器上生效的連接原則 `Proxy` `Redirect` 。 預設原則是 `Redirect` 針對源自 azure (的所有用戶端連線，例如，從 Azure 虛擬機器) ，以及 `Proxy` 源自于 (外部的所有用戶端連線（例如，來自本機工作站的連接) ）。

為了將延遲降到最低及將輸送量提升到最高，強烈建議您採用 `Redirect` 連線原則，而不要採用 `Proxy` 連線原則。 不過，您必須符合如上面所述允許網路流量的其他需求。 如果用戶端是 Azure 虛擬機器，您可以使用網路安全性群組來完成此操作， (NSG) 搭配 [服務](../../virtual-network/security-overview.md#service-tags)標籤。 如果用戶端是從內部部署的工作站進行連線，您可能需要與網路系統管理員合作，以允許透過公司防火牆的網路流量。

## <a name="connectivity-from-within-azure"></a>從 Azure 內部連線

如果您從 Azure 內部連線，該連線預設的連線原則為 `Redirect`。 的原則 `Redirect` 表示在 Azure SQL Database 建立 TCP 會話之後，會將用戶端會話重新導向至正確的資料庫叢集，並將目的地虛擬 IP 的變更從 Azure SQL Database 閘道的目的地虛擬 IP 變更為叢集。 此後，所有後續封包會略過 Azure SQL Database 閘道，直接流經叢集。 下圖說明此流量。

![架構概觀](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>從 Azure 外部連線

如果您從 Azure 外部連線，該連線預設的連線原則為 `Proxy`。 `Proxy` 原則代表會透過 Azure SQL Database 閘道建立 TCP 工作階段，且所有後續封包都會流經閘道。 下圖說明此流量。

![架構概觀](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> 額外的開啟 TCP 通訊埠1434和14000-14999，以啟用 [與 DAC 的連接](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>閘道 IP 位址

下表依區域列出閘道的 IP 位址。 若要連線到 SQL Database 或 Azure Synapse，您必須允許該區域 **所有** 閘道的網路流量。

下列文章說明如何將流量遷移至特定區域中新閘道的詳細資料，請參閱下列文章： [Azure SQL Database 將流量遷移至較新的閘道](gateway-migration.md)

| 區域名稱          | 閘道 IP 位址 |
| --- | --- |
| 澳大利亞中部    | 20.36.105.0 |
| 澳大利亞 Central2   | 20.36.113.0 |
| 澳大利亞東部       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| 澳大利亞東南部 | 191.239.192.109, 13.73.109.251, 13.77.48.10 |
| 巴西南部         | 104.41.11.5, 191.233.200.14 |
| 加拿大中部       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| 加拿大東部          | 40.86.226.166, 52.242.30.154 |
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
| 德國中西部 | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| 印度中部        | 104.211.96.159     |
| 印度南部          | 104.211.224.146    |
| 印度西部           | 104.211.160.80     |
| 日本東部           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 |
| 日本西部           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 |
| 南韓中部        | 52.231.32.42       |
| 南韓南部          | 52.231.200.86      |
| 美國中北部     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| 北歐         | 40.113.93.91, 191.235.193.75, 52.138.224.1, 13.74.104.113 |
| 挪威東部          | 51.120.96.0        |
| 挪威西部          | 51.120.216.0       |
| 南非北部   | 102.133.152.0, 102.133.120.2       |
| 南非西部    | 102.133.24.0       |
| 美國中南部     | 13.66.62.124, 23.98.162.75, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| 東南亞      | 104.43.15.0, 23.100.117.95, 40.78.232.3   |
| 瑞士北部    | 51.107.56.0, 51.107.57.0 |
| 瑞士西部     | 51.107.152.0, 51.107.153.0 |
| 阿拉伯聯合大公國中部          | 20.37.72.64        |
| 阿拉伯聯合大公國北部            | 65.52.248.0        |
| 英國南部             | 51.140.184.11, 51.105.64.0 |
| 英國西部              | 51.141.8.11        |
| 美國中西部      | 13.78.145.25, 13.78.248.43        |
| 西歐          | 40.68.37.158, 191.237.232.75, 104.40.168.105, 52.236.184.163  |
| 美國西部              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| 美國西部 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |

## <a name="next-steps"></a>後續步驟

- 如需有關如何變更伺服器 Azure SQL Database 連接原則的詳細資訊，請參閱 [連結 [-原則](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)]。
- 如需使用 ADO.NET 4.5 或更新版本用戶端之 Azure SQL Database 連接行為的詳細資訊，請參閱 [ADO.NET 4.5 超過 1433以外的連接埠](adonet-v12-develop-direct-route-ports.md)。
- 如需一般應用程式開發概觀的資訊，請參閱 [SQL Database 應用程式開發概觀](develop-overview.md)。
