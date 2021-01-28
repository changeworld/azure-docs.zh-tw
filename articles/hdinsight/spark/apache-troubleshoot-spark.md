---
title: 針對 Azure HDInsight 中的 Apache Spark 進行疑難排解
description: 取得有關使用 Apache Spark 和 Azure HDInsight 的常見問題解答。
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: af488cd253e8a8ebedd838aa5286185ea556f69d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942498"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>使用 Azure HDInsight 為 Apache Spark 進行疑難排解

瞭解在 [Apache Ambari](https://ambari.apache.org/)中使用 Apache Spark 承載時的最大問題和解決方式。

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>如何使用 Apache Ambari 在叢集上設定 Apache Spark 應用程式？

您可以微調 Spark 設定值，以避免 Apache Spark 的應用程式 `OutofMemoryError` 例外狀況。 下列步驟顯示 Azure HDInsight 中的預設 Spark 設定值：

1. 使用您的叢集認證登入 Ambari `https://CLUSTERNAME.azurehdidnsight.net` 。 初始畫面會顯示總覽儀表板。 HDInsight 3.6 與4.0 之間有輕微的表面差異。

1. 流覽至 **Spark2** 的 [進行]  >  ****。

    ![選取 [設定] 索引標籤](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. 在設定清單中，選取並展開 [ **自訂-spark2-預設值**]。

1. 尋找需要調整的設定值，例如 **spark.executor.memory**。 在此情況下， **9728m** 的值太高。

    ![選取 [Custom-spark2-defaults]](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. 將值設為建議的設定。 這項設定的建議值為 **2048m**。

1. 儲存此值，然後儲存設定。 選取 [儲存]。

    ![將值變更為 2048m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    註記設定變更，然後選取 [儲存]。

    ![輸入所做變更的附註內容](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    如有任何設定需要注意，您會收到通知。 記下項目，然後選取 [仍要繼續]。

    ![選取 [仍要繼續]](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. 每次儲存設定時，系統都會提示您重新啟動服務。 選取 [重新啟動]。

    ![選取 [重新啟動]](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    確認重新啟動。

    ![選取 [確認全部重新啟動]](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    您可以檢閱執行中的處理序。

    ![檢閱執行中的處理序](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. 您可以新增設定。 在設定清單中選取 [Custom-spark2-defaults]，然後選取 [新增屬性]。

    ![選取 [新增屬性]](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. 定義新的屬性。 您可以使用特定設定 (例如資料類型) 的對話方塊定義單一屬性。 或者，您也可以使用一行一定義的方式定義多個屬性。

    在本例中，**spark.driver.memory** 屬性是以值 **4g** 定義。

    ![定義新的屬性](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. 儲存設定，然後重新啟動服務，如步驟 6 和 7 中所述。

這些變更為全叢集的，但可在提交 Spark 作業時予以覆寫。

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>如何? 使用叢集上的 Jupyter Notebook 來設定 Apache Spark 應用程式？

在 Jupyter Notebook 的第一個資料格中，于 **%% configure** 指示詞之後，以有效的 JSON 格式指定 Spark 設定。 請視需要變更實際值：

![新增設定](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>如何使用 Apache Livy 在叢集上設定 Apache Spark 應用程式？

使用 cURL 等 REST 用戶端，將 Spark 應用程式提交給 Livy。 使用類似下列的命令。 請視需要變更實際值：

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>如何使用 spark-submit 在叢集上設定 Apache Spark 應用程式？

使用類似下列的命令啟動 spark-shell。 請視需要變更設定的實際值：

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>延伸閱讀

[Apache Spark job submission on HDInsight clusters](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (提交 HDInsight 叢集上的 Apache Spark 作業)

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* [Spark 記憶體管理總覽](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)。

* [在 HDInsight 叢集上將 Spark 應用程式進行偵錯工具](/archive/blogs/azuredatalake/spark-debugging-101)。

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。