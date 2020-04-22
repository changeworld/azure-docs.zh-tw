---
title: 將 Azure 資料儲存從第 1 代移到第 2 代
description: 將 Azure 資料儲存從第 1 代遷移到第 2 代。
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 80c0afafca3b0bf497689cbd4a0870eedd066cfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677132"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>將 Azure 資料儲存從第 1 代移到第 2 代

您可以將資料、工作負載和應用程式從資料存儲存儲第 1 代遷移到數據湖存儲 Gen2。

Azure 資料存儲第 2 代基於[Azure Blob 存儲](storage-blobs-introduction.md)構建,並提供一組專用於大數據分析的功能。 [數據存儲庫 Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/)結合了[Azure 資料儲存庫 Gen1](https://docs.microsoft.com/azure/data-lake-store/index)的功能,如檔案系統語義、目錄和檔級安全性,以及[Azure Blob 儲存](storage-blobs-introduction.md)的低成本、分層儲存、高可用性/災難恢復功能。

> [!NOTE]
> 為了便於閱讀,本文使用術語*Gen1*引用 Azure 資料存儲第 1 代,術語*Gen2*引用 Azure 數據存儲庫第 2 代。

## <a name="recommended-approach"></a>推薦方法

要遷移到 Gen2,我們建議採用以下方法。

:heavy_check_mark:步驟1:評估準備情況

:heavy_check_mark:步驟 2:準備遷移

:heavy_check_mark:步驟 3:遷移數據和應用程式工作負載

:heavy_check_mark:第4步:從第1代到第2代的分后

> [!NOTE]
> Gen1 和 Gen2 是不同的服務,沒有就地升級體驗,需要有意遷移。 

### <a name="step-1-assess-readiness"></a>第 1 步:評估就緒情況

1. 瞭解[資料儲存湖儲存第 2 代產品](https://azure.microsoft.com/services/storage/data-lake-storage/)/它的好處,成本和一般架構。 

2. 將第 1 代的功能與第 2[代的功能進行比較](#gen1-gen2-feature-comparison)。 

3. 查看[已知問題](data-lake-storage-known-issues.md)清單,以評估功能上的任何差距。

4. Gen2 支援 Blob 儲存功能,如[診斷紀錄紀錄](../common/storage-analytics-logging.md),[存取層](storage-blob-storage-tiers.md)與[Blob 儲存生命週期管理原則](storage-lifecycle-management-concepts.md)。 如果您有興趣使用這些功能中的任何一個,請查看[目前支援等級](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features)。

5. 檢視[Azure 生態系統支援的](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access)當前狀態,以確保 Gen2 支援解決方案所依賴的任何服務。

### <a name="step-2-prepare-to-migrate"></a>第二步:準備遷移

1. 標識要遷移的數據集。

   借此機會清理不再使用的數據集。 除非您計劃一次遷移所有數據,否則請藉此機會確定可以分階段遷移的邏輯數據組。
   
2. 確定遷移對您的業務的影響。

   例如,考慮在遷移進行期間,您是否承受任何停機時間。 這些注意事項可以説明您確定合適的遷移模式,並選擇最合適的工具。

3. 創建遷移計劃。 

   我們建議這些[移轉模式](#migration-patterns)。 您可以選擇這些模式之一,將它們組合在一起,或設計您自己的自定義模式。

### <a name="step-3-migrate-data-workloads-and-applications"></a>第三步:移轉資料、工作負載和應用程式

使用您喜歡的模式遷移數據、工作負載和應用程式。 我們建議您以增量方式驗證方案。

1. [建立存儲帳戶](data-lake-storage-quickstart-create-account.md)並啟用分層命名空間功能。 

2. 遷移數據。 

3. 在[工作負荷中配置服務](data-lake-storage-integrate-with-azure-services.md)以指向 Gen2 終結點。 
   
4. 更新應用程式以使用 Gen2 API。 請參閱[.NET](data-lake-storage-directory-file-acl-dotnet.md)、Java、Python、JAVAScript 和[REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)的指南。 [Java](data-lake-storage-directory-file-acl-java.md) [Python](data-lake-storage-directory-file-acl-python.md) [JavaScript](data-lake-storage-directory-file-acl-javascript.md) 
   
5. 更新文稿以使用資料儲存湖儲存 Gen2 [PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md)與[Azure CLI 指令](data-lake-storage-directory-file-acl-cli.md)。
   
6. 搜尋包含代碼檔中字串`adl://`的URI引用,或在Databricks筆記本、Apache Hive HQL 檔或用作工作負載一部分的任何其他檔中。 將這些引用替換為新儲存帳戶的[Gen2 格式化 URI。](data-lake-storage-introduction-abfs-uri.md) 例如:第 1`adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile`代`abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`URI: 可能變為 。 

7. 將帳號上的安全性設定為包含[角色的存取控制 (RBAC) 角色](../common/storage-auth-aad-rbac-portal.md),[檔案與資料夾層級安全性](data-lake-storage-access-control.md)以及 Azure[儲存防火牆與虛擬網路](../common/storage-network-security.md)。

### <a name="step-4-cutover-from-gen1-to-gen2"></a>第 4 步:從第 1 代到第 2 代的分距

在確信應用程式和工作負載在 Gen2 上保持穩定後,您可以開始使用 Gen2 來滿足您的業務方案。 關閉在Gen1上執行的任何剩餘管道,並停用您的第一代帳戶。 

<a id="gen1-gen2-feature-comparison" />

## <a name="gen1-vs-gen2-capabilities"></a>第一個第二代功能

下表將第一代的功能與第 2 代的功能進行比較。

|區域 |Gen1   |Gen2 |
|---|---|---|
|資料組織|[階層式命名空間](data-lake-storage-namespace.md)<br>檔案與資料夾支援|[階層式命名空間](data-lake-storage-namespace.md)<br>容器、檔案與資料夾支援 |
|異地備援| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|驗證|[AAD 託管識別](../../active-directory/managed-identities-azure-resources/overview.md)<br>[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD 託管識別](../../active-directory/managed-identities-azure-resources/overview.md)<br>[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)<br>[分享存取金鑰](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|授權|管理 - [RBAC](../../role-based-access-control/overview.md)<br>資料 + [ACL](data-lake-storage-access-control.md)|管理與[RBAC](../../role-based-access-control/overview.md)<br>資料 - [ACL](data-lake-storage-access-control.md), [RBAC](../../role-based-access-control/overview.md) |
|加密 = 靜態資料|伺服器端 ─ 使用[Microsoft 管理](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)或[客戶管理的](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)金鑰|伺服器端 ─ 使用[Microsoft 管理](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)或[客戶管理的](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)金鑰|
|VNET 支援|[VNET 整合](../../data-lake-store/data-lake-store-network-security.md)|[服務終結點](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json),[專用終結點](../common/storage-private-endpoints.md)|
|開發人員體驗|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md),[電源外殼](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|一般可用 - [REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>公共預覽 ─ [JavaScript](data-lake-storage-directory-file-acl-javascript.md),[電源外殼](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|診斷記錄|經典記錄<br>[整合 Azure 監視器](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[經典紀錄](../common/storage-analytics-logging.md)- 一般可用<br>Azure 監視器整合 + 時間線 TBD|
|生態系統|[HDInsight (3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure 資料磚 (3.1 及以上)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [SQL DW](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure 資料磚 (5.1 及以上)](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [SQL DW](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns" />

## <a name="gen1-to-gen2-patterns"></a>第一個第 2 代模式

選擇遷移模式,然後根據需要修改該模式。

|||
|---|---|
|**提升和換檔**|最簡單的模式。 如果數據管道能夠承受停機時間,則是理想的選擇。|
|**增量複製**|類似於*提升和換檔*,但停機時間較少。 非常適合複製時間更長的大量數據。|
|**雙管道**|非常適合承受任何停機時間的管道。|
|**雙向同步**|類似於*雙管道*,但採用更分階段的方法,適用於更複雜的管道。|

讓我們仔細看看每種模式。
 
### <a name="lift-and-shift-pattern"></a>提高和換檔模式

這是最簡單的模式。

1. 停止對 Gen1 的所有寫入。

2. 將數據從第 1 代移動到第 2 代。 我們建議[Azure 資料工廠](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 ACL 隨數據一起複製。

3. 將引入操作和工作負載指向第 2 代。

4. 停用第 1 代。

> [!div class="mx-imgBorder"]
> ![提高和換檔模式](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>使用提升和換檔模式的注意事項

:heavy_check_mark:同時將所有工作負載從第 1 代劃合到第 2 代。

:heavy_check_mark:在遷移和縮短期間預計停機。

:heavy_check_mark:非常適合提供停機時間且所有應用可以同時升級的管道。

### <a name="incremental-copy-pattern"></a>漸寫模式

1. 開始將數據從第 1 代移動到第 2 代。 我們建議[Azure 資料工廠](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 ACL 隨數據一起複製。

2. 增量複製來自Gen1的新資料。

3. 複製所有資料後,停止對 Gen1 的所有寫入,並將工作負載指向 Gen2。

4. 停用第 1 代。

> [!div class="mx-imgBorder"]
> ![漸寫模式](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>使用增量複製模式的注意事項:

:heavy_check_mark:同時將所有工作負載從第 1 代劃合到第 2 代。

:heavy_check_mark:僅在縮短期間預計停機。

:heavy_check_mark:非常適合所有應用同時升級的管道,但數據副本需要更多時間。

### <a name="dual-pipeline-pattern"></a>雙導管模式

1. 將數據從第 1 代移動到第 2 代。 我們建議[Azure 資料工廠](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 ACL 隨數據一起複製。

2. 將新數據引入第 1 代和第 2 代。

3. 將工作負載指向第 2 代。

4. 停止對第 1 代的所有寫入,然後停用第 1 代。

> [!div class="mx-imgBorder"]
> ![雙導管模式](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>使用雙導管模式的注意事項:

:heavy_check_mark:第 1 代和第二代管道並排運行。

:heavy_check_mark:支援零停機時間。

:heavy_check_mark:在工作負載和應用程式無法承受任何停機時間,並且您可以引入兩個存儲帳戶的情況下,是理想的選擇。

### <a name="bi-directional-sync-pattern"></a>雙向同步模式

1. 設置第 1 代和 Gen2 之間的雙向複製。 我們建議[萬迪斯可](https://docs.wandisco.com/bigdata/wdfusion/adls/)。 它為現有數據提供了修復功能。

3. 完成所有移動後,停止對Gen1的所有寫入並關閉雙向複製。

4. 停用第 1 代。

> [!div class="mx-imgBorder"]
> ![雙向圖案](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>使用雙向同步模式的注意事項:

:heavy_check_mark:非常適合涉及大量管道和依賴關係的複雜方案,其中分階段方法可能更有意義。  

:heavy_check_mark:遷移工作量很高,但它為第一代和第二代提供了並行支援。

## <a name="next-steps"></a>後續步驟

- 瞭解為存儲帳戶設置安全性的各個部分。 請參考[Azure 儲存安全指南](../common/storage-security-guide.md)。
- 優化數據湖存儲的性能。 有關[性能,請參閱優化 Azure 資料儲存第 2 代](data-lake-storage-performance-tuning-guidance.md)
- 查看管理數據湖存儲的最佳做法。 請參考[Azure 資料儲存第 2 代的最佳做法](data-lake-storage-best-practices.md)

