---
title: 適用于 HDInsight 上 ML 服務的 azure 儲存體解決方案-Azure
description: 了解 HDInsight 上 ML 服務可用的不同儲存體選項
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: 1c79d0390a80a1358ddb09707fbabf6a5a2affdc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75660234"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure HDInsight 上適用于 ML 服務的 Azure 儲存體解決方案

HDInsight 上的 ML 服務可使用不同的儲存體解決方案來保存資料、程式碼或包含分析結果的物件。 這些解決方案包括下列選項：

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake 儲存體](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure 檔案儲存體](https://azure.microsoft.com/services/storage/files/)

您也可以使用 HDInsight 叢集存取多個 Azure 儲存體帳戶或容器。 Azure 檔案儲存體是在邊緣節點上使用的便利資料儲存選項，可讓您將 Azure 儲存體檔案共用掛接到（例如 Linux 檔案系統）。 但是，只要是擁有受支援作業系統 (例如 Windows 或 Linux) 的系統，就可以掛接和使用「Azure 檔案」共用。

當您在 HDInsight 中建立 Apache Hadoop 叢集時，您可以指定**Azure 儲存體**帳戶或**Data Lake Storage**。 來自該帳戶的特定儲存體容器，會保留您所建立叢集的檔案系統 (例如，Hadoop 分散式檔案系統)。 如需詳細資訊與指導方針，請參閱：

- [搭配 HDInsight 使用 Azure 儲存體](../hdinsight-hadoop-use-blob-storage.md)
- [搭配使用 Data Lake Storage 與 Azure HDInsight 叢集](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>搭配 ML 服務叢集使用 Azure Blob 儲存體帳戶

如果您在建立 ML 服務叢集時指定一個以上的儲存體帳戶，下列指示說明如何針對在 ML 服務叢集上的資料存取和作業使用次要帳戶。 假設下列儲存體帳戶和容器：**storage1** 和名為 **container1** 的預設容器，以及具有 **container2** 的 **storage2**。

> [!WARNING]  
> 基於效能目的，系統會在與您指定的主要儲存體帳戶相同資料中心內建立 HDInsight 叢集。 不支援在與 HDInsight 叢集不同的位置中使用儲存體帳戶。

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>搭配 HDInsight 上的 ML 服務使用預設儲存體

1. 使用 SSH 用戶端，連線到您叢集的邊緣節點。 如需搭配 HDInsight 叢集使用 SSH 的相關資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。
  
2. 在 /share 目錄建立範例檔案 mysamplefile.csv。

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. 切換至 R Studio 或其他 R 主控台，然後撰寫 R 程式碼，將名稱節點設定為**預設**和您想要存取的檔案位置。  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

所有目錄和檔案的參考會指向儲存體帳戶 `wasbs://container1@storage1.blob.core.windows.net`。 這是與 HDInsight 叢集關聯的「預設儲存體帳戶」****。

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>搭配 HDInsight 上的 ML 服務使用其他儲存體

現在，假設您想要處理名稱為 mysamplefile1.csv 的檔案，其所在位置為 **storage2** 中 **container2** 的 /private 目錄。

在 R 程式碼中，將名稱節點參考指向 **storage2** 儲存體帳戶。

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

現在，所有目錄和檔案的參考會指向儲存體帳戶 `wasbs://container2@storage2.blob.core.windows.net`。 這是您已指定的「名稱節點」****。

`/user/RevoShare/<SSH username>`在**storage2**上設定目錄，如下所示：

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-with-ml-services-cluster"></a>搭配 ML 服務叢集使用 Azure Data Lake Storage

若要搭配 HDInsight 叢集帳戶使用 Data Lake Storage，您必須為叢集提供您想要使用之每個 Azure Data Lake Storage 的存取權。 如需有關如何使用 Azure 入口網站並使用 Azure Data Lake Storage 帳戶作為預設儲存體或作為其他儲存體來建立 HDInsight 叢集的指示，請參閱 [使用 Azure 入口網站搭配 Data Lake Storage 建立 HDInsight 叢集](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)。

接著，您會以和使用次要 Azure 儲存體帳戶的方式 (如先前程序所述) 很像的方式，在 R 指令碼中使用儲存體。

### <a name="add-cluster-access-to-your-azure-data-lake-storage"></a>為 Azure Data Lake Storage 新增叢集存取權

您可以使用與 HDInsight 叢集相關聯的 Azure Active Directory (Azure AD) 服務主體來存取 Data Lake Storage。

1. 在建立 HDInsight 叢集時，從 [資料來源]**** 索引標籤中選取 [叢集 AAD 身分識別]****。

2. 在 [叢集 AAD 身分識別]**** 對話方塊的 [選取 AD 服務主體]**** 底下，選取 [新建]****。

在為服務主體命名並建立密碼後，請按一下 [管理 ADLS 存取]****，以將該服務主體與您的 Data Lake Storage 產生關聯。

您也可以在叢集建立之後，將叢集存取新增至一個或多個 Data Lake 儲存體帳戶。 為 Data Lake Storage 開啟 Azure 入口網站入口，並移至 [資料總管] > [存取] > [新增]****。

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>如何從 HDInsight 上的 ML 服務存取 Data Lake Storage Gen1

一旦您獲得 Data Lake Storage Gen1 的存取權，就可以在 HDInsight 上的 ML 服務叢集中使用儲存體，就像是次要 Azure 儲存體帳戶一樣。 唯一的差別在於前置詞**wasbs://** 會變更為**adl://** ，如下所示：

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

下列命令是用來搭配 RevoShare 目錄設定 Data Lake Storage Gen1 帳戶，並新增來自先前範例的範例 .csv 檔案：

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-file-storage-with-ml-services-on-hdinsight"></a>搭配 HDInsight 上的 ML 服務使用 Azure 檔案儲存體

另外還有一個方便的資料儲存選項，可在邊緣節點上使用，稱為[Azure 檔案儲存體](https://azure.microsoft.com/services/storage/files/)。 它可以讓您將 Azure 儲存體的檔案共用掛接至 Linux 檔案系統。 此選項對於儲存資料檔案、R 指令碼與結果物件相當便利，該結果物件在稍後可以於邊緣節點 (而不是 HDFS) 上使用原生檔案系統時需要。

Azure 檔案的主要優點是，只要是擁有受支援作業系統 (例如 Windows 或 Linux) 的系統，就可以掛接和使用檔案共用。 例如，您本人或您的小組成員所擁有的另一個 HDInsight 叢集，或是 Azure VM 甚或內部部署系統均可使用 Azure 檔案。 如需詳細資訊，請參閱：

- [如何搭配 Linux 使用 Azure 檔案儲存體](../../storage/files/storage-how-to-use-files-linux.md)
- [如何在 Windows 上使用 Azure 檔案儲存體](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>後續步驟

- [HDInsight 上的 ML 服務叢集概觀](r-server-overview.md)
- [在 HDInsight 上計算 ML 服務叢集的內容選項](r-server-compute-contexts.md)
- [搭配 Azure HDInsight 叢集使用 Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
