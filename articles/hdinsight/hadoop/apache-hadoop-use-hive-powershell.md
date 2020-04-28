---
title: 在 HDInsight 中搭配使用 Apache Hive 與 PowerShell - Azure
description: 使用 PowerShell 在 Azure HDInsight 中的 Apache Hadoop 中執行 Apache Hive 查詢
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: deaa934b257fab74830d75e308a283e7608dc590
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75552588"
---
# <a name="run-apache-hive-queries-using-powershell"></a>使用 PowerShell 執行 Apache Hive 查詢

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

本檔提供使用 Azure PowerShell 在 HDInsight 叢集上的 Apache Hadoop 中執行 Apache Hive 查詢的範例。

> [!NOTE]  
> 本文件不提供範例中使用的 HiveQL 陳述式所執行的工作詳細的描述。 如需此範例中使用的 HiveQL 的相關資訊，請參閱 [在 HDInsight 上搭配 Apache Hadoop 使用 Apache Hive](hdinsight-use-hive.md)。

## <a name="prerequisites"></a>先決條件

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[開始在 Linux 上使用 HDInsight](./apache-hadoop-linux-tutorial-get-started.md)。

* 安裝 PowerShell [Az 模組](https://docs.microsoft.com/powershell/azure/overview)。

## <a name="run-a-hive-query"></a>執行 HIVE 查詢

Azure PowerShell 提供 *Cmdlet* ，可讓您從遠端在 HDInsight 上執行 Hive 查詢。 就內部而言，Cmdlet 會對 HDInsight 叢集上的 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) 進行 REST 呼叫。

在遠端 HDInsight 叢集中執行 Hive 查詢時，會使用下列 Cmdlet：

* `Connect-AzAccount`：向您的 Azure 訂用帳戶驗證 Azure PowerShell。
* `New-AzHDInsightHiveJobDefinition`：使用指定的 HiveQL 陳述式建立「作業定義」**。
* `Start-AzHDInsightJob`：將作業定義傳送至 HDInsight，並啟動作業。 系統會傳回「作業」** 物件。
* `Wait-AzHDInsightJob`：使用作業物件來檢查作業的狀態。 它會等到工作完成，或等到等候時間超過。
* `Get-AzHDInsightJobOutput`：用來擷取作業的輸出。
* `Invoke-AzHDInsightHiveJob`：用來執行 HiveQL 陳述式。 這個 Cmdlet 會阻止查詢完成，然後傳回結果。
* `Use-AzHDInsightCluster`：設定要用於 `Invoke-AzHDInsightHiveJob` 命令的現行叢集。

下列步驟示範如何使用這些 Cmdlet，在您的 HDInsight 叢集中執行工作：

1. 使用編輯器，將下列程式碼儲存為 `hivejob.ps1`。

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. 開啟新的 **Azure PowerShell** 命令提示字元。 將目錄切換至 `hivejob.ps1` 檔案的位置，然後使用下列命令執行指令碼：

        .\hivejob.ps1

    當腳本執行時，系統會提示您輸入叢集名稱和 HTTPS/叢集系統管理員帳號憑證。 系統可能也會提示您登入您的 Azure 訂用帳戶。

3. 作業完成時，應該會傳回類似下列文字的資訊：

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. 如前所述，`Invoke-Hive` 可用來執行查詢，並等候回應。 使用下列指令碼，查看 Invoke-Hive 如何運作：

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    輸出看起來會像下列文字：

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > 如果 HiveQL 查詢的時間很長，您可以使用 Azure PowerShell **Here-Strings** Cmdlet 或 HiveQL 指令碼檔案。 下列程式碼片段說明如何使用 `Invoke-Hive` Cmdlet 來執行 HiveQL 指令碼檔案。 必須將 HiveQL 指令碼檔案上傳至 wasb://。
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > 如需 **Here-Strings** 的詳細資訊，請參閱<a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">使用 Windows PowerShell Here-Strings</a>。

## <a name="troubleshooting"></a>疑難排解

如果作業完成時未傳回任何資訊，請檢視錯誤記錄。 若要檢視這項作業的錯誤資訊，請將下列內容新增至 `hivejob.ps1` 檔案的結尾並加以儲存，然後重新執行。

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

這個 Cmdlet 會傳回作業處理期間寫入到 STDERR 的資訊。

## <a name="summary"></a>摘要

如您所見，Azure PowerShell 提供簡單的方法，在 HDInsight 叢集中執行 Hive 查詢、監視工作狀態，以及擷取輸出。

## <a name="next-steps"></a>後續步驟

如需 HDInsight 中 Hive 的一般資訊：

* [在 HDInsight 上將 Apache Hive 與 Apache Hadoop 搭配使用](hdinsight-use-hive.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [搭配 MapReduce 與 HDInsight 上的 Apache Hadoop](hdinsight-use-mapreduce.md)
