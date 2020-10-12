---
title: 閘道流量遷移通知
description: 文章提供有關遷移 Azure SQL Database 閘道 IP 位址的使用者通知
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1 
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: ee55e0a66a0487291233713498f6eb541980dbba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90004267"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database 流量遷移至較新的閘道
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

隨著 Azure 基礎結構的改進，Microsoft 會定期重新整理硬體，以確保我們能提供最佳的客戶體驗。 在接下來的幾個月，我們計畫新增以較新硬體世代為基礎的閘道、將流量遷移至這些閘道，最後在某些區域以較舊硬體為基礎的閘道解除委任。  

客戶會在每個區域中可用閘道的任何變更之前，透過電子郵件和 Azure 入口網站中的通知。 最新的資訊會在 [ [閘道 IP 位址](connectivity-architecture.md#gateway-ip-addresses) ] 資料表中維持 Azure SQL Database 的狀態。

## <a name="status-updates"></a>狀態更新

# <a name="in-progress"></a>[正在進行](#tab/in-progress-ip)
### <a name="october-2020"></a>2020 年 10 月

即將將新的 SQL 閘道新增至下欄區域：

- 德國中西部：51.116.240.0、51.116.248。0

這些 SQL 閘道應該會在2020年10月12日開始接受客戶流量。 

### <a name="september-2020"></a>2020 年 9 月
即將將新的 SQL 閘道新增至下欄區域。 這些 SQL 閘道應該會在 **2020 年9月 15**日開始接受客戶流量：

- 澳大利亞東南部：13.77.48.10
- 加拿大東部：40.86.226.166、52.242.30.154
- 英國南部：51.140.184.11、51.105.64。0

現有的 SQL 閘道將開始接受下欄區域中的流量。 這些 SQL 閘道應該會在 **2020 年9月 15** 日開始接受客戶流量：

- 澳大利亞東南部：191.239.192.109 和13.73.109.251
- 美國中部：13.67.215.62、52.182.137.15、23.99.160.139、104.208.16.96 和104.208.21。1
- 東亞：191.234.2.139、52.175.33.150 和13.75.32。4
- 美國東部：40.121.158.30、40.79.153.12、191.238.6.43 和40.78.225.32
- 美國東部2：40.79.84.180、52.177.185.181、52.167.104.0、191.239.224.107 和104.208.150。3
- 法國中部：40.79.137.0 和40.79.129。1
- 日本西部：104.214.148.156、40.74.100.192、191.238.68.11 和40.74.97.10
- 美國中北部：23.96.178.199、23.98.55.75 和52.162.104.33
- 東南亞：104.43.15.0、23.100.117.95 和40.78.232。3
- 美國西部：104.42.238.205、23.99.34.75 和13.86.216.196

即將將新的 SQL 閘道新增至下欄區域。 這些 SQL 閘道應該會在 **2020 年9月 10**日開始接受客戶流量：

- 美國中西部：13.78.248.43 
- 南非北部：102.133.120。2  

即將將新的 SQL 閘道新增至下欄區域。 這些 SQL 閘道應該會在 **2020 年9月 1**日開始接受客戶流量：

- 北歐：13.74.104.113 
- West 美國2：40.78.248.10 
- 西歐：52.236.184.163 
- 美國中南部：20.45.121.1、20.49.88。1 

現有的 SQL 閘道將開始接受下欄區域中的流量。 這些 SQL 閘道應該會在 **2020 年9月 1** 日開始接受客戶流量：
- 日本東部：40.79.184.8、40.79.192。5

# <a name="completed"></a>[已完成](#tab/completed-ip)

下列閘道遷移已完成： 

### <a name="august-2020"></a>2020 年 8 月

即將將新的 SQL 閘道新增至下欄區域：

- 澳大利亞東部：13.70.112。9
- 加拿大中部：52.246.152.0、20.38.144。1 
- 美國西部2：40.78.240。8

這些 SQL 閘道應該會在2020年8月10日開始接受客戶流量。 

### <a name="october-2019"></a>2019 年 10 月
- 巴西南部
- 美國西部
- 西歐
- 美國東部
- 美國中部
- 東南亞
- 美國中南部
- 北歐
- 美國中北部
- 日本西部
- 日本東部
- 美國東部 2
- 東亞

---

## <a name="impact-of-this-change"></a>這項變更的影響

流量遷移可能會變更 DNS 針對 Azure SQL Database 中的資料庫所解析的公用 IP 位址。
如果您有下列情況，可能會受到影響：

- 對內部部署防火牆中任何特定閘道的 IP 位址進行硬式編碼
- 有任何子網使用 Microsoft 做為服務端點，但無法與閘道 IP 位址通訊
- 針對您的資料庫使用 [區域多餘](high-availability-sla.md#zone-redundant-configuration) 設定

如果您有下列情況，將不會受到影響：
 
- 重新導向為連接原則
- 從 Azure 內部和使用服務標記的 SQL Database 連線
- 使用支援的 JDBC Driver for SQL Server 版本所建立的連接將不會有任何影響。 如需支援的 JDBC 版本，請參閱 [下載適用于 SQL Server 的 MICROSOFT JDBC Driver](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)。

## <a name="what-to-do-you-do-if-youre-affected"></a>如果您受到影響，該怎麼辦？

建議您在 TCP 埠1433和埠範圍11000-11999 的區域中，允許所有 [閘道 ip 位址](connectivity-architecture.md#gateway-ip-addresses) 的連出流量連至 ip 位址。 這項建議適用于從內部部署連線的用戶端，也適用于透過服務端點連接的用戶端。 如需埠範圍的詳細資訊，請參閱 [連接原則](connectivity-architecture.md#connection-policy)。

使用 Microsoft JDBC Driver 4.0 版的應用程式所進行的連線，可能會導致憑證驗證失敗。 較低版本的 Microsoft JDBC 會依賴憑證的 [主旨] 欄位中的一般名稱 (CN) 。 緩和措施是確保 hostNameInCertificate 屬性設定為 *. database.windows.net。 如需有關如何設定 hostNameInCertificate 屬性的詳細資訊，請參閱 [使用加密進行連接](/sql/connect/jdbc/connecting-with-ssl-encryption)。

如果上述緩和措施無法運作，請使用下列 URL 提出 SQL Database 或 SQL 受控執行個體的支援要求： https://aka.ms/getazuresupport

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [AZURE SQL 連線架構](connectivity-architecture.md)
