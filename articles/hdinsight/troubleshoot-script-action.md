---
title: 在 Azure HDInsight 中排除文稿操作故障
description: Azure HDInsight 中腳本操作的一般故障排除步驟。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/21/2020
ms.openlocfilehash: b1e6b674edc155e0aa6c88ad360eb59864eebee4
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771972"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>在 Azure HDInsight 中排除文稿操作故障

本文介紹了與 Azure HDInsight 群集互動時問題的故障排除步驟和可能的解決方案。

## <a name="viewing-logs"></a>檢視記錄

您可以使用 Apache Ambari Web UI 查看文稿操作記錄的資訊。 如果腳本在群集創建期間失敗,日誌位於預設群集存儲帳戶中。 本節提供有關如何使用這兩個選項來擷取記錄的資訊。

### <a name="apache-ambari-web-ui"></a>阿帕奇·安巴里網路用戶介面

1. 從 Web 瀏覽器`https://CLUSTERNAME.azurehdinsight.net`導航`CLUSTERNAME`到 , 群集的名稱在哪裡。

1. 在頁面頂端的列中，選取 **ops** 項目。 系統會顯示一個清單，當中包含透過 Ambari 在叢集上執行的目前和先前作業。

    ![Ambari Web UI 列與選取的 ops](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. 尋找在 [作業]**** 欄位中有 **run\_customscriptaction** 的項目。 這些項目是在執行指令碼動作時建立的。

    ![阿帕奇·安巴里腳本操作操作](./media/troubleshoot-script-action/ambari-script-action.png)

    若要檢視 **STDOUT** 和 **STDERR** 輸出，請選取 **run\customscriptaction** 項目，然後向下鑽研連結。 這是指令碼執行時所產生的輸出，其中可能包含實用的資訊。

### <a name="default-storage-account"></a>預設儲存體帳戶

如果叢集建立因指令碼錯誤而失敗，記錄會保存在叢集儲存體帳戶中。

* 儲存體記錄位於 `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`。

    ![文稿操作紀錄](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    在此目錄底下，記錄會個別針對**前端節點**、**背景工作節點**及 **Zookeeper 節點**進行組織。 請參閱下列範例：

    * **頭節點**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **次要節點**:`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **動物園管理員節點**:`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* 相對應主機的所有 **stdout** 和 **stderr** 都會上傳到儲存體帳戶。 每個指令碼動作都有一個 **output-\*.txt** 和 **errors-\*.txt**。 **output-*.txt** 檔案包含在主機上執行之指令碼的 URI 相關資訊。 以下文字是此資訊的範例：

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* 您有可能重複建立具有相同名稱的指令碼動作叢集。 在該情況下，您可以根據 **DATE** 資料夾名稱來區分相關的記錄。 例如，在不同日期建立之叢集 (**mycluster**) 的資料夾結構會類似下列記錄項目：

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 如果您在同一天建立具有相同名稱的指令碼動作叢集，您可以使用唯一的前置詞來識別相關的記錄檔。

* 如果您在接近上午 12:00 (午夜) 時建立叢集，則記錄檔可能會橫跨兩天。 在該情況下，您會看到同一個叢集有兩個不同日期的資料夾。

* 將記錄檔上傳到預設容器最多可能需要 5 分鐘的時間，特別是針對大型叢集。 因此，如果您想要存取記錄，就不應在指令碼動作失敗時立即刪除叢集。

## <a name="ambari-watchdog"></a>Ambari 看門狗

請勿變更 Linux 型 HDInsight 叢集上的 Ambari 看門狗 (hdinsightwatchdog) 密碼。 密碼更改會破壞在 HDInsight 群集上運行新文本操作的能力。

## <a name="cant-import-name-blobservice"></a>無法匯入名稱 BlobService

__症狀__。 指令碼動作失敗。 在 Ambari 中檢視作業時，會顯示類似下列錯誤的文字：

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__原因__。 如果您升級 HDInsight 叢集隨附的「Python Azure 儲存體」用戶端，就會發生此錯誤。 HDInsight 需要 Azure 儲存體用戶端 0.20.0。

__決議__. 若要解決此錯誤，請使用 `ssh`來手動連線至每個叢集節點。 請執行下列命令來重新安裝正確的儲存體用戶端版本：

```bash
sudo pip install azure-storage==0.20.0
```

如需有關使用 SSH 來連線到叢集的資訊，請參閱[使用 SSH 連線到 HDInsight (Apache Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>歷程記錄不會顯示在叢集建立期間使用的指令碼

如果您的叢集是在 2016 年 3 月 15 日之前建立的，您在指令碼動作歷程記錄中就可能不會看到項目。 調整叢集大小會導致指令碼顯示在指令碼動作歷程記錄中。

有兩種例外狀況：

* 您的叢集是在 2015 年 9 月 1 日之前建立的。 此日期是引進指令碼動作的日期。 任何叢集只要是在此日期之前建立的，都不可能是使用指令碼動作來建立叢集。

* 您在叢集建立期間使用了多個指令碼動作。 或是您將相同名稱用於多個指令碼，或將相同名稱、相同 URI 但不同參數用於多個指令碼。 在這些情況下，您會得到下列錯誤：

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源:答案、支持和專家。

* 如果需要更多説明,可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從選單列中選擇 **「支援」** 或打開 **「説明 + 支援**中心」。 關於詳細資訊,請檢視[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問,並且通過[Azure 支援計劃](https://azure.microsoft.com/support/plans/)之一提供技術支援。