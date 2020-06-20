---
title: Azure SQL Edge 支援的功能 (預覽)
description: 了解 Azure SQL Edge 支援的功能詳細資料 (預覽)
keywords: SQL Edge 簡介, 什麼是 SQL Edge, SQL Edge 概觀
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e3bac1cbb0e57f9be4a489207f2fe8b30b5d457e
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233218"
---
# <a name="supported-features-of-azure-sql-edge-preview"></a>Azure SQL Edge 支援的功能 (預覽) 

本文提供 Azure SQL Edge 所支援功能的詳細資料。 Azure SQL Edge 會以 Linux 上最新版 Microsoft SQL Server 資料庫引擎為建置基礎，其支援 Linux 上 SQL Server 2019 支援的一部份功能，還有一些 SQL Server 2019 目前在 Linux 或 Windows 上不支援或無法使用的功能。 如需 Linux 上 SQL Server 支援的功能完整清單，請參閱 [Linux 上的 SQL Server 2019 版本和支援功能](https://docs.microsoft.com/sql/linux/sql-server-linux-editions-and-components-2019)。 如需 Windows 上 SQL Server 的版本和支援功能，請參閱 [SQL Server 2019 (15.x) 的版本和支援功能](https://docs.microsoft.com/sql/sql-server/editions-and-components-of-sql-server-version-15)。

> [!NOTE]
> Azure SQL Edge 目前為預覽版，因此不應用於實際執行環境。 在驗證部署和使用案例之後，Microsoft 可能會建議您在實際執行環境執行 Azure SQL Edge。

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge 版本

Azure SQL Edge 提供兩種不同的版本或軟體方案。 這些版本具有相同的功能集，不同之處在於其使用權限和可在主機系統上存取的 CPU/記憶體數量。

   |**規劃**  |**說明**  |
   |---------|---------|
   |Azure SQL Edge 開發人員  |  僅限開發 SKU，每個 Azure SQL Edge 開發人員容器皆限制為最多 4 個核心和 32 GB 的記憶體  |
   |Azure SQL Edge    |  生產 SKU，每個 Azure SQL Edge 容器皆限制為最多 8 個核心和 64 GB 的記憶體  |

## <a name="operating-system"></a>作業系統

Azure SQL Edge 容器目前是以 Ubuntu 16.04 為基礎，因此僅支援在執行 Ubuntu 16.04 (建議使用) 或 Ubuntu 18.04 的 Docker 主機上執行。 Azure SQL Edge 也可以在其他作業系統主機上執行，例如其他 Linux 發行版本或 Windows (使用 Docker CE 或 Docker EE)，不過 Microsoft 並不會廣泛地測試這些設定。

Azure SQL Edge 目前僅支援透過 Azure IoT Edge 進行部署。 如需 Azure IoT Edge 支援系統的詳細資訊，請參閱 [Azure IoT Edge 支援的系統](https://docs.microsoft.com/azure/iot-edge/support)。

在 Windows 上執行 Azure SQL Edge 的建議設定是在 Windows 主機上設定 Ubuntu VM，然後在 Linux VM 內執行 SQL Edge。

## <a name="hardware-support"></a>硬體支援

Azure SQL Edge 需要 64 位元處理器 (可能是來自 Intel、AMD 或 ARM)，而且主機上至少要有一個處理器和 1 GB 的 RAM。 雖然 Azure SQL Edge 的啟動記憶體使用量接近 500 MB，但在邊緣裝置上執行的其他 IoT Edge 模組需要更多記憶體。

## <a name="azure-sql-edge-components"></a>Azure SQL Edge 元件

Azure SQL Edge 僅支援資料庫引擎，不支援 Windows 上 SQL Server 2019 或 Linux 上 SQL Server 2019 提供的其他元件。 具體而言，Azure SQL Edge 不支援 Analysis Services、Reporting Services、Integration Services、Master Data Services、機器學習服務 (資料庫內) 和 Machine Learning Server (獨立) 等 SQL Server 元件。

## <a name="supported-features"></a>支援的功能

除了支援 Linux 上 SQL Server 的部分功能之外，Azure SQL Edge 還可支援下列新功能。 

- SQL 串流，以支援 Azure 串流分析的引擎為建置基礎，可在 Azure SQL Edge 中提供即時資料串流功能。 
- 名為 Date_Bucket 的新 T-SQL 函式，用於進行時間序列資料分析。
- 透過 ONNX 執行階段 (隨附於 SQL 引擎) 執行的 Machine Learning 功能。

## <a name="sql-server-on-linux-features-not-supported-in-azure-sql-edge"></a>Azure SQL Edge 中不支援哪些 Linux 上的 SQL Server 功能

下列清單列出 Azure SQL Edge 中目前不支援哪些 Linux 上的 SQL Server 2019 功能。

| 區域 | 不支援的功能或服務 |
|-----|-----|
| **資料庫設計** | 記憶體內部 OLTP 和相關的 DDL 命令及 Transact-SQL 函式、目錄檢視和動態管理檢視 |
| &nbsp; | HierarchyID 資料類型和相關的 DDL 命令及 Transact-SQL 函式、目錄檢視和動態管理檢視 |
| &nbsp; | 空間資料類型和相關的 DDL 命令及 Transact-SQL 函式、目錄檢視和動態管理檢視 |
| &nbsp; | Stretch DB 和相關的 DDL 命令及 Transact-SQL 函式、目錄檢視和動態管理檢視 |
| &nbsp; | 全文索引和搜尋與相關的 DDL 命令及 Transact-SQL 函式、目錄檢視和動態管理檢視|
| &nbsp; | FileTable、FILESTREAM 和相關的 DDL 命令及 Transact-SQL 函式、目錄檢視和動態管理檢視|
| **資料庫引擎** | 複寫。 不過，您可以將 Azure SQL Edge 設定為複寫拓撲的推送訂閱者。 |
| &nbsp; | Polybase。 不過，您可以將 Azure SQL Edge 設定為 Polybase 中外部資料表的目標 |
| &nbsp; | 透過 Java 和 Spark 提供的語言擴充性 |
| &nbsp; | Active Directory 整合 |
| &nbsp; | 資料庫快照集 |
| &nbsp; | 支援持續性記憶體 |
| &nbsp; | Microsoft 分散式交易協調器 |
| &nbsp; | Resource Governor 和 IO 資源管理 |
| &nbsp; | 緩衝集區擴充 |
| &nbsp; | 搭配協力廠商連線的分散式查詢 |
| &nbsp; | 連結的伺服器 |
| &nbsp; | 系統擴充預存程序 (XP_CMDSHELL 等) |
| &nbsp; | CLR 組件和相關的 DDL 命令及 Transact-SQL 函式、目錄檢視和動態管理檢視 |
| &nbsp; | 與 CLR 相依的 T-SQL 函式，例如 ASSEMBLYPROPERTY、FORMAT、PARSE、TRY_PARSE |
| &nbsp; | 與 CLR 相依的日期和時間目錄檢視、函式和查詢子句 |
| &nbsp; | 緩衝集區擴充 |
| &nbsp; | Database Mail |
| **SQL Server Agent** |  子系統：CmdExec、PowerShell、佇列讀取器、SSIS、SSAS、SSRS |
| &nbsp; | 警示 |
| &nbsp; | 受控備份 |
| **高可用性** | AlwaysOn 可用性群組  |
| &nbsp; | 基本可用性群組 |
| &nbsp; | Always On 容錯移轉叢集執行個體 |
| &nbsp; | 資料庫鏡像 |
| &nbsp; | 熱新增記憶體和 CPU |
| **安全性** | 可延伸金鑰管理 |
| &nbsp; | Active Directory 整合|
| &nbsp; | 支援安全記憶體保護區|
| **服務** | SQL Server Browser |
| &nbsp; | 透過 R 和 Python 提供 Machine Learning |
| &nbsp; | StreamInsight |
| &nbsp; | Analysis Services |
| &nbsp; | Reporting Services |
| &nbsp; | Data Quality Services |
| &nbsp; | Master Data Services |
| &nbsp; | Distributed Replay |
| **管理能力** | SQL Server 公用程式控制點 |

## <a name="next-steps"></a>後續步驟

- [部署 Azure SQL Edge](deploy-portal.md)
- [設定 Azure SQL Edge](configure.md)
- [使用 SQL Server 用戶端工具連線到 Azure SQL Edge 執行個體](connect.md)
- [在 Azure SQL Edge 中備份和還原資料庫](backup-restore.md)
