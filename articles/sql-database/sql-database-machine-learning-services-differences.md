---
title: Machine Learning 服務的主要差異（預覽）
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81453144"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Azure SQL Database （預覽）和 SQL Server 中 Machine Learning 服務之間的主要差異

（預覽）中 Azure SQL Database Machine Learning 服務（含 R）的功能與[SQL Server Machine Learning 服務](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)類似。 以下是一些主要的差異。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>語言支援

SQL Server 透過[擴充性架構](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)支援 R 和 Python。 SQL Database 兩種語言皆不支援。 主要差異包括：

- R 是 SQL Database 中唯一支援的語言， 目前不支援 Python。
- R 版本為 3.4.4。
- 不需要透過 `sp_configure` 設定 `external scripts enabled`。

## <a name="package-management"></a>套件管理

SQL Database 和 SQL Server 對於 R 套件的管理和安裝方式有所不同。 這些差異包括：

- R 套件是透過 [sqlmlutils](https://github.com/Microsoft/sqlmlutils) 或 [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) 安裝。
- 套件無法執行輸出網路呼叫。 這項限制類似于 SQL Server 中[Machine Learning 服務的預設防火牆規則](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration)，但無法在 SQL Database 中變更。
- 對於依賴外部執行階段 (如 Java) 或需要 OS API 安裝或使用存取權的套件並不支援。

## <a name="writing-to-a-temporary-table"></a>寫入臨時表

如果您在 Azure SQL Database 中使用 RODBC，則無法寫入臨時表，不論是在`sp_execute_external_script`會話內部或外部建立。 因應措施是使用[RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata)和[rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) （with overwrite = FALSE 和 append = "rows"）來寫入在`sp_execute_external_script`查詢之前建立的全域臨時表。

## <a name="resource-governance"></a>資源管理

無法透過 [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) 和外部資源集區限制 R 資源。

在公開預覽期間，R 資源會設定為最多20% 的 SQL Database 資源，並取決於您選擇的服務層級。 如需詳細資訊，請參閱 [Azure SQL Database 購買模型](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)。
### <a name="insufficient-memory-error"></a>記憶體不足錯誤

如果 R 沒有足夠的記憶體可用，您會收到錯誤訊息。 常見的錯誤訊息為：

- 無法與要求識別碼： * * * * * * 的 ' R ' 腳本的執行時間通訊。 請檢查 ' R ' 執行時間的需求
- 執行 ' sp_execute_external_script ' 時發生 ' R ' 腳本錯誤，HRESULT 0x80004004。 ...發生外部腳本錯誤： ".。無法在 C 函數 ' R_AllocStringBuffer ' 中配置記憶體（0 Mb）
- 發生外部腳本錯誤：錯誤：無法配置大小的向量。

記憶體使用量取決於 R 腳本中使用的數量，以及執行的平行查詢數目。 如果您收到上述錯誤，您可以將資料庫調整為較高的服務層級，以解決此問題。

## <a name="next-steps"></a>後續步驟

- 請參閱[使用 R （預覽） Machine Learning 服務](sql-database-machine-learning-services-overview.md)的總覽 Azure SQL Database。
- 若要瞭解如何使用 R 來查詢 Azure SQL Database Machine Learning 服務（預覽），請參閱[快速入門手冊](sql-database-connect-query-r.md)。
- 若要開始使用一些簡單的 R 腳本，請參閱[在 Azure SQL Database Machine Learning 服務（預覽）中建立和執行簡單的 r 腳本](sql-database-quickstart-r-create-script.md)。
