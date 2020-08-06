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
ms.openlocfilehash: 30e57736d0b0e40eb01573d6acca2c618dcf6ee3
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87759694"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database 流量遷移至較新的閘道
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

隨著 Azure 基礎結構的改善，Microsoft 會定期重新整理硬體，以確保我們能提供最佳的客戶體驗。 在接下來的幾個月，我們打算新增以較新硬體層代為基礎的閘道、將流量遷移至它們，最後解除委任在某些區域的舊版硬體上建立的閘道。  

客戶將會在每個區域中可用的閘道變更之前，透過電子郵件和 Azure 入口網站中的通知。 最新的資訊將會保留在 [ [Azure SQL Database 閘道 IP 位址](connectivity-architecture.md#gateway-ip-addresses)] 資料表中。

## <a name="status-updates"></a>狀態更新

# <a name="in-progress"></a>[正在進行](#tab/in-progress-ip)
### <a name="september-2020"></a>2020 年 9 月

新的 SQL 閘道將會新增至下欄區域。 這些 SQL 閘道應于2020年9月10日開始接受客戶流量：

- 美國中西部：13.78.248.43 
- 南非北部：102.133.120。2  

新的 SQL 閘道將會新增至下欄區域。 這些 SQL 閘道應于2020年9月1日開始接受客戶流量：

- 北歐：13.74.104.113 
- West 美國2：40.78.248.10 
- 西歐：52.236.184.163 
- 美國中南部：20.45.121.1、20.49.88。1 

現有的 SQL 閘道將會開始接受下欄區域中的流量。 這些 SQL 閘道應于2020年9月1日開始接受客戶流量。 :
- 日本東部：40.79.184.8、40.79.192。5

### <a name="august-2020"></a>2020 年 8 月

新的 SQL 閘道將會新增至下欄區域：

- 澳大利亞東部：13.70.112。9
- 加拿大中部：52.246.152.0、20.38.144。1 
- 美國西部2：40.78.240。8

這些 SQL 閘道應于2020年8月10日開始接受客戶流量。 

# <a name="completed"></a>[Completed](#tab/completed-ip)

下列閘道遷移已完成： 

### <a name="october-2019"></a>2019 年 10 月
- 巴西南部
- 美國西部
- 西歐
- 美國東部
- 美國中部
- 東南亞
- 美國中南部
- 歐洲北部
- 美國中北部
- 日本西部
- 日本東部
- 美國東部 2
- 東亞

---

## <a name="impact-of-this-change"></a>這項變更的影響

流量遷移可能會變更 DNS 在 Azure SQL Database 中為您的資料庫解析的公用 IP 位址。
如果您這樣做，可能會受到影響：

- 將內部部署防火牆中任何特定閘道的 IP 位址硬式編碼
- 有任何子網使用 Microsoft 做為服務端點，但無法與閘道 IP 位址通訊
- 針對您的資料庫使用[區域多餘](high-availability-sla.md#zone-redundant-configuration)設定

如果您有下列情況，將不會受到影響：
 
- 重新導向作為連線原則
- 從 Azure 內部和使用服務標籤的 SQL Database 連線
- 使用支援的 JDBC Driver for SQL Server 版本所建立的連接將不會有任何影響。 如需支援的 JDBC 版本，請參閱[下載適用于 SQL Server 的 MICROSOFT JDBC Driver](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)。

## <a name="what-to-do-you-do-if-youre-affected"></a>如果受到影響，該怎麼辦？

我們建議您針對 TCP 通訊埠1433上的區域中的所有[閘道 ip 位址](connectivity-architecture.md#gateway-ip-addresses)，以及埠範圍11000-11999，允許對 ip 位址的輸出流量。 這項建議適用于從內部部署連線，以及透過服務端點連線的用戶端。 如需埠範圍的詳細資訊，請參閱連線[原則](connectivity-architecture.md#connection-policy)。

使用 Microsoft JDBC Driver 4.0 版的應用程式所進行的連接可能會使憑證驗證失敗。 較低版本的 Microsoft JDBC 依賴憑證的 [主旨] 欄位中的一般名稱 (CN) 。 緩和措施是確保 hostNameInCertificate 屬性設定為 *. database.windows.net。 如需有關如何設定 hostNameInCertificate 屬性的詳細資訊，請參閱[使用加密連接](/sql/connect/jdbc/connecting-with-ssl-encryption)。

如果上述緩和措施無法解決問題，請使用下列 URL 提出 SQL Database 或 SQL 受控執行個體的支援要求：https://aka.ms/getazuresupport

## <a name="next-steps"></a>後續步驟

- 深入瞭解[AZURE SQL 連線架構](connectivity-architecture.md)
