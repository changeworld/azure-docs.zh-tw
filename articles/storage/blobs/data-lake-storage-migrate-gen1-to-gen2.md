---
title: 將 Azure Data Lake Storage 從 Gen1 遷移至 Gen2
description: 將 Azure Data Lake Storage 從 Gen1 遷移至 Gen2，其建置於 Azure Blob 儲存體上，並提供一組專門用於大型資料分析的功能。
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: fcd59cb3bf42dc843387043d982e6c43d665784d
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89486836"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>將 Azure Data Lake Storage 從 Gen1 遷移至 Gen2

您可以將資料、工作負載和應用程式從 Data Lake Storage Gen1 遷移至 Data Lake Storage Gen2。

Azure Data Lake Storage Gen2 建置於 [Azure Blob 儲存體](storage-blobs-introduction.md) 上，並提供一組專門用於大型資料分析的功能。 [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) 結合了 [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index)的功能，例如檔案系統語義、目錄和檔案層級安全性，以及 [Azure Blob 儲存體](storage-blobs-introduction.md)的低成本、分層式儲存體、高可用性/嚴重損壞修復功能的調整。

> [!NOTE]
> 為了方便閱讀，本文使用「詞彙 Gen1」（ *Gen1* ）來參考 Azure Data Lake Storage Gen1，並使用*Gen2*一詞來參考 Azure Data Lake Storage Gen2。

## <a name="recommended-approach"></a>建議的方法

若要遷移至 Gen2，建議您採用下列方法。

： heavy_check_mark：步驟1：評估就緒程度

： heavy_check_mark：步驟2：準備遷移

： heavy_check_mark：步驟3：遷移資料和應用程式工作負載

： heavy_check_mark：步驟4：從 Gen1 轉換為 Gen2

> [!NOTE]
> Gen1 和 Gen2 是不同的服務，沒有任何就地升級體驗，需要刻意的遷移工作。 

### <a name="step-1-assess-readiness"></a>步驟1：評估就緒程度

1. 瞭解 [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/)的供應專案;它是優點、成本和一般架構。 

2. [比較](#gen1-gen2-feature-comparison) Gen1 與 Gen2 的功能。 

3. 請查看 [已知問題](data-lake-storage-known-issues.md) 清單，以評估任何功能中的差距。

4. Gen2 支援 Blob 儲存體功能，例如 [診斷記錄](../common/storage-analytics-logging.md)、 [存取層](storage-blob-storage-tiers.md)，以及 [blob 儲存體生命週期管理原則](storage-lifecycle-management-concepts.md)。 如果您很感興趣使用這些功能，請參閱 [目前的支援層級](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-supported-blob-storage-features)。

5. 請參閱 [Azure 生態系統支援](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-multi-protocol-access) 的目前狀態，以確保 Gen2 支援您解決方案所依存的任何服務。

### <a name="step-2-prepare-to-migrate"></a>步驟2：準備遷移

1. 識別您要遷移的資料集。

   請利用這個機會清除您不再使用的資料集。 除非您打算一次遷移所有資料，否則請花這段時間找出可在階段中遷移的邏輯資料群組。
   
2. 判斷遷移對您的業務有何影響。

   例如，請考慮您是否可以在進行遷移時承受任何停機時間。 這些考慮可協助您識別適合的移轉模式，以及選擇最適當的工具。

3. 建立遷移計畫。 

   我們建議採用這些 [移轉模式](#migration-patterns)。 您可以選擇其中一個模式、將它們合併在一起，或設計自己的自訂模式。

### <a name="step-3-migrate-data-workloads-and-applications"></a>步驟3：遷移資料、工作負載和應用程式

使用您偏好的模式來遷移資料、工作負載和應用程式。 建議您以累加方式驗證案例。

1. [建立儲存體帳戶](data-lake-storage-quickstart-create-account.md) 並啟用階層命名空間功能。 

2. 遷移您的資料。 

3. 設定 [工作負載中的服務](data-lake-storage-integrate-with-azure-services.md) ，以指向您的 Gen2 端點。 
   
4. 更新應用程式以使用 Gen2 Api。 請參閱 [.net](data-lake-storage-directory-file-acl-dotnet.md)、 [JAVA](data-lake-storage-directory-file-acl-java.md)、 [Python](data-lake-storage-directory-file-acl-python.md)、 [JavaScript](data-lake-storage-directory-file-acl-javascript.md) 和 [REST](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)的指南。 
   
5. 更新腳本以使用 Data Lake Storage Gen2 [PowerShell Cmdlet](data-lake-storage-directory-file-acl-powershell.md)和 [Azure CLI 命令](data-lake-storage-directory-file-acl-cli.md)。
   
6. 搜尋 URI 參考，其中包含程式碼檔案中的字串 `adl://` ，或在 Databricks 筆記本中，APACHE HIVE HQL 檔案或作為工作負載一部分使用的任何其他檔案。 以新儲存體帳戶的 [Gen2 格式化 URI](data-lake-storage-introduction-abfs-uri.md) 取代這些參考。 例如： Gen1 URI：可能會 `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` 變成 `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` 。 

7. 在您的帳戶上設定安全性，以包含 [Azure 角色](../common/storage-auth-aad-rbac-portal.md)、檔案 [和資料夾層級安全性](data-lake-storage-access-control.md)，以及 [Azure 儲存體防火牆和虛擬網路](../common/storage-network-security.md)。

### <a name="step-4-cutover-from-gen1-to-gen2"></a>步驟4：從 Gen1 轉換為 Gen2

在您確信應用程式和工作負載在 Gen2 上穩定之後，您就可以開始使用 Gen2 來滿足您的商務案例。 關閉正在 Gen1 上執行的任何其他管線，並解除委任您的 Gen1 帳戶。 

<a id="gen1-gen2-feature-comparison"></a>

## <a name="gen1-vs-gen2-capabilities"></a>Gen1 與 Gen2 功能

下表比較 Gen1 與 Gen2 的功能。

|區域 |Gen1   |Gen2 |
|---|---|---|
|資料組織|[階層式命名空間](data-lake-storage-namespace.md)<br>檔案和資料夾支援|[階層式命名空間](data-lake-storage-namespace.md)<br>容器、檔案和資料夾支援 |
|異地備援| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage)、 [ZRS](../common/storage-redundancy.md#zone-redundant-storage)、 [GRS](../common/storage-redundancy.md#geo-redundant-storage)、 [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|驗證|[AAD 受控識別](../../active-directory/managed-identities-azure-resources/overview.md)<br>[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD 受控識別](../../active-directory/managed-identities-azure-resources/overview.md)<br>[服務主體](../../active-directory/develop/app-objects-and-service-principals.md)<br>[共用存取金鑰](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)|
|授權|管理- [RBAC](../../role-based-access-control/overview.md)<br>資料- [acl](data-lake-storage-access-control.md)|管理– [RBAC](../../role-based-access-control/overview.md)<br>資料-  [acl](data-lake-storage-access-control.md)、 [RBAC](../../role-based-access-control/overview.md) |
|加密-待用資料|伺服器端–具有 [Microsoft 管理](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 或 [客戶管理的](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 金鑰|伺服器端–具有 [Microsoft 管理](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 或 [客戶管理的](../common/encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 金鑰|
|VNET 支援|[VNET 整合](../../data-lake-store/data-lake-store-network-security.md)|[服務端點](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)， [私人端點](../common/storage-private-endpoints.md)|
|開發人員體驗|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md)、 [.net](../../data-lake-store/data-lake-store-data-operations-net-sdk.md)、 [JAVA](../../data-lake-store/data-lake-store-get-started-java-sdk.md)、 [Python](../../data-lake-store/data-lake-store-data-operations-python.md)、 [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)、 [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|正式推出- [REST](/rest/api/storageservices/data-lake-storage-gen2)、 [.net](data-lake-storage-directory-file-acl-dotnet.md)、 [JAVA](data-lake-storage-directory-file-acl-java.md)、 [Python](data-lake-storage-directory-file-acl-python.md)<br>公開預覽- [JavaScript](data-lake-storage-directory-file-acl-javascript.md)、 [PowerShell](data-lake-storage-directory-file-acl-powershell.md)、 [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|資源記錄|傳統記錄檔<br>[Azure 監視器整合式](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[傳統記錄](../common/storage-analytics-logging.md) -正式推出<br>Azure 監視器整合–時程表 TBD|
|生態系統|[HDInsight (3.6) ](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)、 [Azure Databricks (3.1 和 ](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html)更新版本) 、 [Azure Synapse Analytics](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)、 [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3.6、4.0) ](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)、 [Azure Databricks (5.1 和 ](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2)更新版本) 、 [Azure Synapse Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md)、 [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns"></a>

## <a name="gen1-to-gen2-patterns"></a>Gen1 至 Gen2 模式

選擇移轉模式，然後視需要修改該模式。

|||
|---|---|
|**隨即轉移**|最簡單的模式。 適用于您的資料管線可以承受停機時間。|
|**增量複製**|類似于隨即 *轉移*，但停機時間較少。 適用于需要較長時間才能複製的大量資料。|
|**雙重管線**|適用于無法承受任何停機時間的管線。|
|**雙向同步處理**|類似于 *雙重管線*，但使用更分階段的方法，適用于更複雜的管線。|

讓我們仔細看看每個模式。
 
### <a name="lift-and-shift-pattern"></a>隨即轉移模式

這是最簡單的模式。

1. 停止所有寫入 Gen1。

2. 將資料從 Gen1 移至 Gen2。 建議 [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 Acl 會與資料一起複製。

3. 將內嵌作業和工作負載指向 Gen2。

4. 解除委任 Gen1。

> [!div class="mx-imgBorder"]
> ![隨即轉移模式](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>使用增益和 shift 模式的考慮

： heavy_check_mark：同時針對所有工作負載從 Gen1 轉換為 Gen2。

： heavy_check_mark：在遷移期間和轉換期間都預期會停機。

： heavy_check_mark：適用于可承受停機時間的管線，以及一次可升級的所有應用程式。

### <a name="incremental-copy-pattern"></a>增量複製模式

1. 開始將資料從 Gen1 移至 Gen2。 建議 [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 Acl 會與資料一起複製。

2. 以累加方式從 Gen1 複製新資料。

3. 複製所有資料之後，請停止所有寫入 Gen1，並將工作負載指向 Gen2。

4. 解除委任 Gen1。

> [!div class="mx-imgBorder"]
> ![增量複製模式](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>使用增量複製模式的考慮：

： heavy_check_mark：同時針對所有工作負載從 Gen1 轉換為 Gen2。

： heavy_check_mark：只預期在轉換期間發生停機。

： heavy_check_mark：適用于一次升級所有應用程式的管線，但資料複製需要更多時間。

### <a name="dual-pipeline-pattern"></a>雙重管線模式

1. 將資料從 Gen1 移至 Gen2。 建議 [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)。 Acl 會與資料一起複製。

2. 將新資料內嵌至 Gen1 和 Gen2。

3. 將工作負載指向 Gen2。

4. 停止所有寫入 Gen1，然後解除委任 Gen1。

> [!div class="mx-imgBorder"]
> ![雙重管線模式](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>使用雙重管線模式的考慮：

： heavy_check_mark： Gen1 和 Gen2 管線並存執行。

： heavy_check_mark：支援零停機。

： heavy_check_mark：適用于您的工作負載和應用程式無法承受任何停機時間的情況，而且您可以內嵌至這兩個儲存體帳戶。

### <a name="bi-directional-sync-pattern"></a>雙向同步處理模式

1. 設定 Gen1 與 Gen2 之間的雙向複寫。 我們建議 [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)。 它提供現有資料的修復功能。

3. 當所有移動都完成時，請停止所有寫入 Gen1 並關閉雙向複寫。

4. 解除委任 Gen1。

> [!div class="mx-imgBorder"]
> ![雙向模式](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>使用雙向同步處理模式的考慮：

： heavy_check_mark：適用于涉及大量管線和相依性的複雜案例，而分階段方法可能更有意義。  

： heavy_check_mark：遷移工作很高，但它提供對 Gen1 和 Gen2 的並存支援。

## <a name="next-steps"></a>接下來的步驟

- 瞭解設定儲存體帳戶安全性的各個部分。 請參閱 [Azure 儲存體安全性指南](../common/storage-security-guide.md)。
- 將您 Data Lake Store 的效能優化。 查看效能 [優化 Azure Data Lake Storage Gen2](data-lake-storage-performance-tuning-guidance.md)
- 複習管理 Data Lake Store 的最佳做法。 請參閱 [使用 Azure Data Lake Storage Gen2 的最佳作法](data-lake-storage-best-practices.md)
