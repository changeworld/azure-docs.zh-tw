---
title: 群集創建期間的 Apache 蜂巢庫 - Azure HDInsight
description: 瞭解如何在群集創建期間將 Apache Hive 庫（jar 檔）添加到 HDInsight 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: 0b746963cea5a950ba47d8b4dfeb074cb0910436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471018"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>建立 HDInsight 叢集時新增自訂 Apache Hive 程式庫

瞭解如何在 HDInsight 上預載入[Apache Hive](https://hive.apache.org/)庫。 本文件包含如何在叢集建立期間使用「指令碼動作」預先載入程式庫的詳細資訊。 使用本文檔中的步驟添加的庫在 Hive 中具有全域性版本 - 無需使用[ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli)來載入它們。

## <a name="how-it-works"></a>運作方式

創建群集時，可以使用腳本操作在創建叢集節點時對其進行修改。 本文件中的指令碼會接受單一參數，也就是程式庫的位置。 這個位置必須在 Azure 儲存體帳戶中，且將程式庫必須儲存為 jar 檔案。

叢集建立期間，指令碼會列舉檔案、將它們複製到前端和背景工作節點上的 `/usr/lib/customhivelibs/` 目錄，然後將它們加入至 `core-site.xml` 檔案中的 `hive.aux.jars.path` 屬性。 在以 Linux 為基礎的叢集上，它也會以檔案的位置來更新 `hive-env.sh` 檔案。

使用本文中的腳本操作使庫在使用適用于**WebHCat**的 Hive 用戶端和**HiveServer2**時可用。

## <a name="the-script"></a>指令碼

**指令碼位置**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>需求

* 指令碼必須同時套用至**前端節點**和**背景工作節點**。

* 您想要安裝的 jar 必須儲存在**單一容器**中的 Azure Blob 儲存體。

* 包含 jar 檔案程式庫的儲存體帳戶**必須**在建立期間連結至 HDInsight 叢集。 它必須是預設存儲帳戶，或者通過__存儲帳戶設置添加的帳戶__。

* 必須指定容器的 WASB 路徑做為指令碼動作的參數。 例如，如果 jar 儲存在名為 **mystorage** 的儲存體帳戶上稱為 **libs** 的容器中，則這個參數會是 `wasbs://libs@mystorage.blob.core.windows.net/`。

  > [!NOTE]  
  > 本文件假設您已建立儲存體帳戶、blob 容器，也已將檔案上傳給它。
  >
  > 如果您尚未建立儲存體帳戶，您可以透過 [Azure 入口網站](https://portal.azure.com)來建立。 接著，您可以使用 [Azure 儲存體 Explorer](https://storageexplorer.com/) 之類的公用程式，在帳戶中建立容器，並將檔案上傳至其中。

## <a name="create-a-cluster-using-the-script"></a>使用指令碼建立叢集

1. 使用[Linux 上的預配 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)中的步驟開始預配群集，但不完成預配。 您也可以使用 Azure PowerShell 或 HDInsight .NET SDK，以使用此指令碼建立叢集。 如需使用這些方法的詳細資訊，請參閱 [使用指令碼動作自訂 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。 對於 Azure 門戶，從 **"配置 + 定價**"選項卡中選擇 **"添加腳本操作**"。

1. 對於**存儲**，如果包含 jar 檔庫的存儲帳戶與群集中使用的帳戶不同，則完成**其他存儲帳戶**。

1. 對於**腳本操作**，請提供以下資訊：

    |屬性 |值 |
    |---|---|
    |指令碼類型|- 自訂|
    |名稱|程式庫 |
    |Bash 指令碼 URI|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |節點類型|頭， 工人|
    |參數|輸入包含 jar 之容器和儲存體帳戶的 WASB 位址。 例如： `wasbs://libs@mystorage.blob.core.windows.net/` 。|

    > [!NOTE]
    > 對於 Apache Spark 2.1，使用此 bash`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh`腳本 URI： 。

1. 繼續如[在 Linux 上佈建 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)中所述佈建叢集。

群集創建完成後，您可以使用通過 Hive 通過此腳本添加的 jar，而無需使用 語句`ADD JAR`。

## <a name="next-steps"></a>後續步驟

如需使用 Hive 的詳細資訊，請參閱 [搭配使用 Apache Hive 與 HDInsight](hadoop/hdinsight-use-hive.md)
