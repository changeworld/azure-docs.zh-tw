---
title: 閘道流量移轉通知
description: 文章向使用者提供有關 Azure SQL 資料庫閘道 IP 位址遷移的通知
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 9e3c33bb7493f07d9fbf19710f21d0114e7abec8
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757085"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL 資料庫流量移到較新的閘道

隨著 Azure 基礎結構的改進,Microsoft 將定期刷新硬體,以確保我們提供最佳的客戶體驗。 在接下來的幾個月里,我們計劃添加基於較新的硬體代構建的閘道,將流量遷移到它們,並最終停用某些區域中基於舊硬體構建的閘道。  

客戶將通過電子郵件和 Azure 門戶中收到通知,提前通知每個區域中可用的閘道的任何更改。 最新的資訊將在[Azure SQL 資料庫閘道 IP 位址](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)表中維護。

## <a name="impact-of-this-change"></a>此變更的影響

第一輪流量遷移到較新的閘道定於**2019 年 10 月 14**日在以下區域進行:
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

流量移轉將更改 DNS 為 SQL 資料庫解析的公共 IP 位址。
如果您有:
- 對本地防火牆中任何特定閘道的 IP 位址進行硬編碼
- 使用 Microsoft.SQL 作為服務終結點但無法與閘道 IP 位址通訊的任何子網路

如果您有 :
- 重定為連線原則
- 從 Azure 內部與使用服務標記連線到 SQL 資料庫
- 使用支援的 SQL Server 支援的 JDBC 驅動程式版本建立的連接不會造成任何影響。 有關支援的 JDBC 版本,請參閱[下載 SQL Server 的 Microsoft JDBC 驅動程式](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)。

## <a name="what-to-do-you-do-if-youre-affected"></a>如果您受到影響,該怎麼辦

我們建議您允許 TCP 連接埠 1433 上區域中的所有[Azure 資料庫閘道 IP 位址](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)的出站流量到 IP 位址,埠範圍為 11000-11999。 此建議適用於從本地連接的用戶端,以及通過服務終結點連接的用戶端。 有關連接埠範圍的詳細資訊,請參考[連線政策](sql-database-connectivity-architecture.md#connection-policy)。

使用版本 4.0 以下的 Microsoft JDBC 驅動程式的應用程式建立的連接可能會失敗證書驗證。 Microsoft JDBC 的較低版本依賴於證書的主題欄位中的通用名稱 (CN)。 緩解是確保主機NameIn證書屬性設置為 *.database.windows.net。 有關如何設定主機NameIn證書屬性的詳細資訊,請參閱[使用加密進行連接](/sql/connect/jdbc/connecting-with-ssl-encryption)。

如果上述緩解措施不起作用,請使用以下網址提交 SQL 資料庫支援請求:https://aka.ms/getazuresupport

## <a name="next-steps"></a>後續步驟

- 瞭解有關 Azure [SQL 連線架構結構的更多內容](sql-database-connectivity-architecture.md)
