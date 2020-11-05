---
title: 效能最佳作法和設定指導方針-Azure SQL Edge
description: 瞭解 Azure SQL Edge 中的效能最佳作法和設定指導方針
keywords: SQL Edge，資料保留
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392005"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>效能最佳作法和設定指導方針

Azure SQL Edge 提供數個功能，可用來改善 SQL Edge 部署的效能。 本文提供一些最佳作法和建議，以將效能最大化。 

## <a name="best-practices"></a>最佳作法 

### <a name="configure-multiple-tempdb-data-files"></a>設定多個 tempdb 資料檔案

根據預設，Azure SQL Edge 只會在容器初始化中建立一個 tempdb 資料檔案。 建議您考慮在部署後建立多個 tempdb 資料檔案。 如需詳細資訊，請參閱文章中的指導方針[避免 SQL Server tempdb 資料庫中配置爭用的建議](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d) \(機器翻譯\)。

### <a name="use-clustered-columnstore-indexes-where-possible"></a>可能的話，請使用叢集資料行存放區索引

IoT 和邊緣裝置通常會產生大量的資料，通常會在某個時間範圍內匯總以進行分析。 個別的資料列很少用於任何分析。 資料行存放區索引非常適合用來儲存和查詢這類大型資料集。 此索引使用以資料行為基礎的資料儲存和查詢處理，最多可達到10倍的查詢效能與傳統的資料列導向儲存體之間的效能。 相較於未壓縮的資料大小，您也可以將資料壓縮提升高達 10 倍。 如需詳細資訊，請參閱資料行存放區 [索引](/sql/relational-databases/indexes/columnstore-indexes-overview)

此外，其他 Azure SQL Edge 功能（例如資料串流處理和資料保留）在資料插入和資料移除的資料行存放區優化方面受益。 

### <a name="simple-recovery-model"></a>簡單復原模式

由於儲存體可在邊緣裝置上受到限制，因此 Azure SQL Edge 中的所有使用者資料庫預設都會使用簡單復原模式。 簡單復原模式會自動回收記錄空間，以保持空間需求，基本上不需要管理交易記錄空間。 在具有可用儲存空間限制的邊緣裝置上，這會很有説明。 如需有關簡單復原模式和其他可用復原模式的詳細資訊，請參閱 [復原模式](/sql/relational-databases/backup-restore/recovery-models-sql-server)

簡單復原模式不支援需要交易記錄備份的作業（例如記錄傳送和時間點還原）。  

## <a name="advanced-configuration"></a>進階組態 

### <a name="setting-memory-limits"></a>設定記憶體限制

Azure SQL Edge 最多可支援 64 GB 的緩衝集區記憶體，而在 SQL Edge 容器內執行的附屬進程可能需要額外的記憶體。 在記憶體有限的小型邊緣裝置上，建議您限制 SQL Edge 容器的可用記憶體，讓 docker 主機和其他邊緣進程或模組可以正常運作。 您可以使用下列機制來控制 SQL Edge 的可用記憶體總計。 

- 限制 SQL Edge 容器可用的記憶體：可以使用容器執行時間設定選項來限制 SQL Edge 容器的可用記憶體總計 `--memory` 。 如需限制 SQL Edge 容器可用記憶體的詳細資訊，請參閱 [使用記憶體、cpu 和 gpu 的執行時間選項](https://docs.docker.com/config/containers/resource_constraints/)。

- 限制容器內 SQL 進程可用的記憶體：根據預設，容器內的 SQL 進程只會使用80% 的可用實體 RAM。 在大部分的部署中，預設設定都沒問題。 不過，在某些情況下，資料串流和在 SQL Edge 容器內執行的 ONNX 處理常式可能需要額外的記憶體。 在這種情況下，可以使用 mssql 設定檔案中的設定來控制 SQL 進程可用的記憶體 `memory.memorylimitmb` 。 如需詳細資訊，請參閱 [使用 mssql](configure.md#configure-by-using-an-mssqlconf-file)檔案進行設定。

設定記憶體限制時，請小心不要將這個值設得太低。 如果您沒有為 SQL 進程設定足夠的記憶體，可能會嚴重影響 SQL 效能。

### <a name="delayed-durability"></a>延遲持久性

Azure SQL Edge 中的交易可以是完全持久、SQL Server 預設值或延遲的持久 (也稱為延遲認可) 。

完全持久交易認可是同步認可，而且只有在交易的記錄檔記錄寫入磁碟之後，才會將認可回報為成功並將控制權傳回給用戶端。 延遲的持久交易認可是非同步認可，而且在交易的記錄檔記錄寫入磁碟之前，就會將認可回報為成功。 將交易記錄項目寫入磁碟是讓交易能夠持久的必要條件。 延遲的持久交易會在交易記錄項目排清至磁碟時變成持久。 

在可容忍 **某些資料遺失** 的部署中，或在儲存體速度較慢的邊緣裝置上，延遲的持久性可以用來優化資料內嵌和資料保留型清除。 如需詳細資訊，請參閱[控制交易持久性](/sql/relational-databases/logs/control-transaction-durability)。


### <a name="linux-os-configurations"></a>Linux OS 設定 

請考慮使用下列 [Linux 作業系統](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration) 設定，以體驗 SQL 安裝的最佳效能。