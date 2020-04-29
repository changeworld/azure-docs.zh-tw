---
title: 使用 Azure Data Lake Storage Gen2 URI
description: 使用 Azure Data Lake Storage Gen2 URI
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: fa0f67e0d72ee5710a42b6de744ddae98e20220a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437137"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>使用 Azure Data Lake Storage Gen2 URI

與 Azure Data Lake Storage Gen2 相容的 [Hadoop Filesystem](https://www.aosabook.org/en/hdfs.html) 驅動程式，以其配置識別碼 `abfs` (Azure Blob 檔案系統) 而聞名。 ABFS 驅動程式和其他 Hadoop Filesystem 驅動程式一樣，都使用 URI 格式來處理 Data Lake Storage Gen2 支援帳戶中的檔案和目錄。

## <a name="uri-syntax"></a>URI 語法

Data Lake Storage Gen2 的 URI 語法取決於您的儲存體帳戶是否設定為將 Data Lake Storage Gen2 作為預設的檔案系統。

如果您希望處理的 Data Lake Storage Gen2 可使用帳戶**未**在帳戶建立期間設為預設的檔案系統，URI 語法速記為：

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **配置識別碼**：此 `abfs` 通訊協定作為配置識別碼。 您可以選擇連接或不使用傳輸層安全性（TLS），先前稱為安全通訊端層（SSL）連線。 使用`abfss`來連接 TLS 連線。

2. **檔案系統**：保存檔案和資料夾的上層位置。 這與 Azure 儲存體 Blob 服務中的容器相同。

3. **帳戶名稱**：建立期間為儲存體帳戶指定的名稱。

4. **路徑**：正斜線分隔 (`/`) 的形式表示目錄結構。

5. **檔案名稱**：個別檔案的名稱。 如果要定址目錄，可選用此參數。

但是，如果您希望處理的帳戶在帳戶建立期間已設為預設的檔案系統，則 URI 語法速記為：

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **路徑**：正斜線分隔 (`/`) 的形式表示目錄結構。

2. **檔案名**：個別檔案的名稱。


## <a name="next-steps"></a>後續步驟

- [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
