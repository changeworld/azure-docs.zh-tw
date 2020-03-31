---
title: 搭配使用 MapReduce 和 PowerShell 與 Apache Hadoop - Azure HDInsight
description: 了解如何使用 PowerShell 從遠端搭配執行 MapReduce 工作與 HDInsight 上的 Apache Hadoop。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: b3c1abb7bff54e3e2d294b073b867c6c0e06f482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830066"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>使用 PowerShell 搭配執行 MapReduce 工作與 HDInsight 上的 Apache Hadoop

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

本文件提供使用 Azure PowerShell 在 HDInsight 叢集的 Hadoop 中執行 MapReduce 工作的範例。

## <a name="prerequisites"></a>Prerequisites

* HDInsight 上的 Apache Hadoop 叢集。 請參閱[使用 Azure 門戶創建 Apache Hadoop 群集](../hdinsight-hadoop-create-linux-clusters-portal.md)。

* 安裝 PowerShell [Az 模組](https://docs.microsoft.com/powershell/azure/overview)。

## <a name="run-a-mapreduce-job"></a>執行 MapReduce 作業

Azure PowerShell 提供 *Cmdlet* ，可讓您從遠端在 HDInsight 上執行 MapReduce 工作。 在內部，PowerShell 會對 HDInsight 叢集上執行的 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (先前稱為 Templeton) 發出 REST 呼叫。

在遠端 HDInsight 叢集中執行 MapReduce 工作時，會使用下列 Cmdlet。

|Cmdlet | 描述 |
|---|---|
|Connect-AzAccount|向您的 Azure 訂用帳戶驗證 Azure PowerShell。|
|新-AzHDInsightMap 減少作業定義|使用指定的 MapReduce 資訊來建立新的「作業定義」**。|
|啟動-阿茲德洞察工作|將作業定義傳送給 HDInsight，並啟動作業。 系統會傳回「作業」** 物件。|
|等待-阿茲德洞察工作|使用作業物件來檢查作業的狀態。 它會等到工作完成，或等到等候時間超過。|
|獲取阿茲德洞察作業輸出|用來擷取作業的輸出。|

下列步驟示範如何使用這些 Cmdlet，在您的 HDInsight 叢集中執行工作。

1. 使用編輯器，將以下代碼另存為**mapreducejob.ps1**。

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. 開啟新的 **Azure PowerShell** 命令提示字元。 將目錄變更至 **mapreducejob.ps1** 檔案的位置，然後使用下列命令來執行指令碼：

        .\mapreducejob.ps1

    運行腳本時，系統會提示您獲得 HDInsight 群集的名稱和群集登錄名。 系統可能也會提示您驗證 Azure 訂用帳戶。

3. 在作業完成時，您會收到類似下列文字的輸出：

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    此輸出表示工作已順利完成。

    > [!NOTE]  
    > 如果 **ExitCode** 的值不是 0，請參閱 [疑難排解](#troubleshooting)。

    此範例也會將下載的檔案儲存到您執行指令碼所在目錄中的 **output.txt** 檔案。

### <a name="view-output"></a>檢視輸出

若要查看作業所產生的單字和計數，請使用文字編輯器開啟 **output.txt** 檔案。

> [!NOTE]  
> MapReduce 工作的輸出檔是固定不變的。 因此，如果您重新執行此範例，則需要變更輸出檔的名稱。

## <a name="troubleshooting"></a>疑難排解

如果作業完成時未傳回任何資訊，請檢視作業的錯誤。 要查看此作業的錯誤資訊，請向**mapreducejob.ps1**檔的末尾添加以下命令。 然後保存檔並重新運行腳本。

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

這個 Cmdlet 會傳回作業執行時寫入到 STDERR 的資訊。

## <a name="next-steps"></a>後續步驟

如您所見，Azure PowerShell 提供簡單的方法，在 HDInsight 叢集上執行 MapReduce 工作、監視工作狀態，以及擷取輸出。 如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊：

* [在 HDInsight Hadoop 上使用 MapReduce](hdinsight-use-mapreduce.md)
* [在 HDInsight 上將 Apache Hive 與 Apache Hadoop 搭配使用](hdinsight-use-hive.md)
