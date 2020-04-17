---
title: 機器學習服務的主要差異(預覽)
description: 本主題說明 Azure SQL Database 機器學習服務 (搭配 R) 和 SQL Server 機器學習服務的主要差異。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 34ba75b6126024c9cd43d6fe474f7c1b62dd990f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453144"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Azure SQL 資料庫(預覽)和 SQL Server 中的機器學習服務之間的主要區別

Azure SQL 資料庫機器學習服務(在(預覽)中具有 R 的功能類似於[SQL Server 機器學習服務](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)。 下面是一些關鍵區別。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>語言支援

SQL Server 透過[擴充性架構](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)支援 R 和 Python。 SQL Database 兩種語言皆不支援。 主要差異包括：

- R 是 SQL Database 中唯一支援的語言， 目前不支援 Python。
- R 版本為 3.4.4。
- 不需要透過 `sp_configure` 設定 `external scripts enabled`。

## <a name="package-management"></a>套件管理

SQL Database 和 SQL Server 對於 R 套件的管理和安裝方式有所不同。 這些差異包括：

- R 套件是透過 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 或 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) 安裝。
- 套件無法執行輸出網路呼叫。 此限制類似於 SQL Server 中[機器學習服務的預設防火牆規則](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration),但不能在 SQL 資料庫中更改。
- 對於依賴外部執行階段 (如 Java) 或需要 OS API 安裝或使用存取權的套件並不支援。

## <a name="writing-to-a-temporary-table"></a>寫入暫存表

如果在 Azure SQL 資料庫中使用 RODBC,則無法寫入暫時`sp_execute_external_script`表,無論它是在工作階段內部還是外部創建。 解決方法是使用[RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata)和[rxDataStep(](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep)使用覆蓋_FALSE 和追加="`sp_execute_external_script`行")寫入 查詢之前創建的全域臨時表。

## <a name="resource-governance"></a>資源管理

無法透過 [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 和外部資源集區限制 R 資源。

在公共預覽期間,R 資源設置為最多 20% 的 SQL 資料庫資源,並取決於您選擇的服務層。 如需詳細資訊，請參閱 [Azure SQL Database 購買模型](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)。
### <a name="insufficient-memory-error"></a>記憶體不足錯誤

如果 R 的可用記憶體不足,則會收到一條錯誤消息。 常見錯誤訊息包括:

- 無法與「R」腳本的運行時通信,請求 ID: | 請檢查「R」執行時的要求
- 在 HRESULT 0x80004004 執行"sp_execute_external_script"期間發生了"R"腳本錯誤。 ...發生了外部文本錯誤:".無法在 C 函數"R_AllocStringBuffer"中分配內存 (0 Mb)"
- 出現外部腳本錯誤:錯誤:無法分配大小向量。

記憶體使用方式取決於 R 腳本中使用的使用量以及要執行的並行查詢數。 如果收到上述錯誤,可以將資料庫擴展到更高的服務層以解決此問題。

## <a name="next-steps"></a>後續步驟

- 請參閱概述[,Azure SQL 資料庫機器學習服務與 R(預覽)](sql-database-machine-learning-services-overview.md)。
- 要瞭解如何使用 R 查詢 Azure SQL 資料庫機器學習服務(預覽),請參閱[快速入門指南](sql-database-connect-query-r.md)。
- 要開始使用一些簡單的 R 文稿,請參閱[在 Azure SQL 資料庫機器學習服務(預覽)中建立與執行簡單的 R 文稿](sql-database-quickstart-r-create-script.md)。
