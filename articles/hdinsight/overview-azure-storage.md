---
title: HDInsight 中的 Azure 儲存體總覽
description: HDInsight 中的 Azure 儲存體總覽。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: fc0af331dc6cb604847be9173c836e0b46ca40ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195174"
---
# <a name="azure-storage-overview-in-hdinsight"></a>HDInsight 中的 Azure 儲存體總覽

Azure 儲存體是強大的一般用途儲存體解決方案，其完美整合了 HDInsight。 HDInsight 可以使用 Azure 儲存體中的 Blob 容器做為叢集的預設檔案系統。 透過 HDFS 介面，HDInsight 中的完整元件集可直接處理儲存為 Blob 的結構化或非結構化資料。

我們建議針對您的預設叢集儲存體和您的商務資料使用個別的儲存體容器。 不同之處在于將 HDInsight 記錄檔和暫存檔案與您自己的商務資料分開。 我們也建議您刪除預設的 blob 容器，其中包含應用程式和系統記錄檔，然後在每次使用之後，以降低儲存成本。 請務必先擷取記錄再刪除容器。

如果您選擇使用**所選網路**上的**防火牆和虛擬網路**限制來保護您的儲存體帳戶，請務必啟用 [**允許信任的 Microsoft 服務**] 例外狀況。例外狀況是讓 HDInsight 能夠存取您的儲存體帳戶。

## <a name="hdinsight-storage-architecture"></a>HDInsight 儲存架構

下圖提供 Azure 儲存體的 HDInsight 架構摘要檢視：

![「HDInsight 儲存體架構」](./media/overview-azure-storage/storage-architecture.png "HDInsight 儲存體架構")

HDInsight 可以存取本機連接至計算節點的分散式檔案系統。 可使用完整 URI 來存取此檔案系統，例如：

    hdfs://<namenodehost>/<path>

透過 HDInsight，您也可以存取 Azure 儲存體中的資料。 語法如下所示：

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

使用 Azure 儲存體帳戶搭配 HDInsight 叢集時，請考量下列原則：

* **儲存體帳戶中連線至叢集的容器：** 因為在建立期間帳戶名稱和金鑰會與叢集相關聯，所以您對這些容器中的 Blob 具有完整存取權。

* **儲存體帳戶中未連線至叢集的公用容器或公用 blob：** 您對容器中的 blob 具有唯讀許可權。
  
  > [!NOTE]  
  > 公用容器可讓您取得該容器中所有可用的 Blob 清單，並取得容器中繼資料。 公用 Blob 只在您知道確切的 URL 時才可讓您存取 Blob。 如需詳細資訊，請參閱 [管理對容器與 Blob 的匿名讀取權限](../storage/blobs/storage-manage-access-to-resources.md)。

* **儲存體帳戶中未連線至叢集的私人容器：** 除非您在提交 WebHCat 工作時定義儲存體帳戶，否則無法存取容器中的 blob。

建立程序及其金鑰中定義的儲存體帳戶會儲存在叢集節點的 %HADOOP_HOME%/conf/core-site.xml 中。 根據預設，HDInsight 會使用 core-site.xml 檔案中定義的儲存體帳戶。 您可以使用 [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) 來修改此設定。

多個 WebHCat 作業，包括 Apache Hive。 和 MapReduce、Apache Hadoop 串流和 Apache Pig，都會提供儲存體帳戶和中繼資料的描述。 （這個層面目前適用于具有儲存體帳戶的 Pig，但不適用於中繼資料）。如需詳細資訊，請參閱搭配[使用 HDInsight 叢集與替代儲存體帳戶和中繼存放區](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)。

Blob 可使用於結構化和非結構化資料。 Blob 容器會將資料儲存為索引鍵/值組，沒有目錄階層。 但是，索引鍵名稱可以包含斜線字元 ( / )，使檔案變成好像儲存在目錄結構中一樣。 例如，Blob 的機碼可以是 `input/log1.txt`。 實際上，`input` 目錄並不存在，但是因為索引碼名稱中有斜線字元，索引碼才會看起來像是檔案路徑。

## <a name="benefits-of-azure-storage"></a>Azure 儲存體的優點

未共置的計算叢集和儲存體資源具有隱含的效能成本。 讓所建立的計算叢集靠近 Azure 區域中的儲存體帳戶資源，即可降低這些成本。 在此區域中，計算節點可透過高速網路有效地存取 Azure 儲存體內的資料。

將資料儲存在 Azure 儲存體而非 HDFS 有許多優點：

* **資料重複使用和共用：** HDFS 中的資料位於計算叢集內。 只有可存取計算叢集的應用程式，才能利用 HDFS API 來使用資料。 相較之下，可以透過 HDFS API 或 Blob 儲存體 REST API 存取 Azure 儲存體中的資料。 因為這種安排，許多應用程式 (包括其他 HDInsight 叢集) 和工具都可用來產生和取用資料。

* **資料封存：** 當資料儲存在 Azure 儲存體中時，可安全地刪除用於計算的 HDInsight 叢集，而不會遺失使用者資料。

* **資料儲存成本：** 長期將資料儲存在 DFS 中的成本高於將資料儲存在 Azure 儲存體中。 因為計算叢集的成本高於 Azure 儲存體的成本。 此外，因為不需要每次產生計算叢集時都重新載入資料，也能節省資料載入成本。

* **彈性向外延展：** 雖然HDFS 提供向外延展的檔案系統，但延展程度取決於您建立給叢集的節點數目。 變更調整規模可能會比您在 Azure 儲存體中自動取得的彈性調整功能來得複雜。

* **異地複寫：** 您的 Azure 儲存體可以進行異地複寫。 雖然異地複寫可提供地理位置復原和資料備援性，但容錯移轉至異地複寫的位置會嚴重影響效能，且可能產生額外的成本。 因此，請謹慎選擇異地複寫，且最好在資料的價值大於額外成本時才這樣做。

某些 MapReduce 工作和封裝可能會產生中繼結果，但您不會想要將這些結果儲存在 Azure 儲存體中。 在此情況下，您仍可選擇將資料儲存在本機 HDFS。 在 Hive 工作和其他程序中，HDInsight 會使用 DFS 來儲存許多這些中繼結果。

> [!NOTE]  
> 大部分 HDFS 命令 (例如，`ls`、`copyFromLocal` 和 `mkdir`) 可在 Azure 儲存體中正常運作。 只有原生 HDFS 實作 (稱為 DFS) 的特定命令 (例如 `fschk` 和 `dfsadmin`) 才會在 Azure 儲存體上出現不同的行為。

## <a name="next-steps"></a>後續步驟

* [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)
* [Azure 儲存體簡介](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen1 概觀](./overview-data-lake-storage-gen1.md)