---
title: 適用於 Azure Data Lake Storage Gen2 的 Azure Blob 檔案系統驅動程式
description: ABFS Hadoop 檔案系統驅動程式
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3db039d39ef532ea51143dc9cbdb6bd5f29d6225
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75970284"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Azure Blob 檔案系統驅動程式 (ABFS)：Hadoop 專用的 Azure 儲存體驅動程式

Azure Data Lake Storage Gen2 中資料的主要存取方法之一是透過 [Hadoop 檔案系統](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html) \(英文\)。 Data Lake Storage Gen2 可讓 Azure Blob 儲存體的使用者存取新的驅動程式，也就是 Azure Blob 檔案系統驅動程式，也稱為 `ABFS`。 ABFS 是 Apache Hadoop 的一部分，隨附於許多 Hadoop 的商業發行版本中。 使用此驅動程式，許多應用程式和架構都可存取 Azure Blob 儲存體中的資料，而不需任何明確參考 Data Lake Storage Gen2 的程式碼。

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>先前的功能：Windows Azure 儲存體 Blob 驅動程式

Windows Azure 儲存體 Blob 驅動程式，也稱為 [WASB 驅動程式](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) \(英文\)，能提供 Azure 儲存體 Blob 的原始支援。 此驅動程式所執行的複雜工作是將檔案系統語意 (如 Hadoop 檔案系統介面所要求) 對應到 Azure Blob 儲存體所公開物件存放區樣式介面的檔案系統語意。 此驅動程式會繼續支援此模型，提供對儲存在 blob 中之資料的高效能存取，但包含大量執行此對應的程式碼，因此難以維護。 此外，某些作業 (例如 [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) \(英文\) 和 [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) \(英文\)) 在套用到目錄時需要此驅動程式來執行大量作業 (由於物件存放區不支援目錄)，這通常會導致效能降低。 ABFS 驅動程式的設計訴求，是要克服 WASB 固有的缺點。

## <a name="the-azure-blob-file-system-driver"></a>Azure Blob 檔案系統驅動程式

[Azure Data Lake Storage REST 介面](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) \(英文\) 是設計來透過 Azure Blob 儲存體支援檔案系統語意。 假設 Hadoop 檔案系統也是設計來支援相同的語意，則在驅動程式中不需要複雜對應。 因此，Azure Blob 檔案系統驅動程式 (或 ABFS) 就僅是 REST API 的用戶端填充碼。

不過，驅動程式仍必須執行某些功能：

### <a name="uri-scheme-to-reference-data"></a>URI 配置到參考資料

與 Hadoop 內的其他檔案系統實作一致，ABFS 驅動程式會定義自己的 URI 配置，如此可能會明確地處理資源 (目錄和檔案)。 URI 配置記載於[使用 Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md) 中。 URI 的結構為：`abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

使用上述的 URI 格式，標準的 Hadoop 工具和架構可用來參考這些資源：

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/
```

ABFS 驅動程式會在內部將 URI 中指定的資源轉譯為檔案和目錄，並使用那些參考來呼叫 Azure Data Lake Storage REST API。

### <a name="authentication"></a>驗證

ABFS 驅動程式支援兩種形式的驗證，讓 Hadoop 應用程式可以安全地存取具有 Data Lake Storage Gen2 功能的帳戶內所含的資源。 [Azure 儲存體安全性指南](security-recommendations.md)中有提供可用驗證配置的完整詳細資料。 其中包括：

- **共用金鑰：** 這允許使用者存取帳戶中的所有資源。 金鑰會加密並儲存於 Hadoop 設定中。

- **Azure Active Directory OAuth 持有人權杖：** 驅動程式會使用終端使用者的身分識別或是已設定的服務主體，來取得和重新整理 Azure AD 持有人權杖。 使用此驗證模型，則可以使用與所提供權杖相關聯的身分識別對個別呼叫授權所有存取權，並針對所指派的 POSIX 存取控制清單 (ACL) 進行評估。

   > [!NOTE]
   > Azure Data Lake Storage Gen2 僅支援 Azure AD v1.0 端點。

### <a name="configuration"></a>組態

適用於 ABFS 驅動程式的所有設定均儲存於 <code>core-site.xml</code> 設定檔中。 在以 [Ambari](https://ambari.apache.org/) \(英文\) 為特色的 Hadoop 發行版本中，也可以使用 Web 入口網站或 Ambari REST API 來管理設定。

所有支援設定項目的詳細資料均指定於 [Hadoop 官方文件](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) \(英文\) 中。

### <a name="hadoop-documentation"></a>Hadoop 文件

ABFS 驅動程式完整記載於 [Hadoop 官方文件](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) \(英文\) 中。

## <a name="next-steps"></a>後續步驟

- [建立 Azure Databricks 叢集](./data-lake-storage-quickstart-create-databricks-account.md)
- [使用 Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md)
