---
title: 無法在 Azure HDInsight 中建立 Jupyter 筆記本
description: 說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: 03b23d86bca10077ede43f3b4e5a6c5fa6f5dd28
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165458"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>無法在 Azure HDInsight 中建立 Jupyter 筆記本

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

啟動 Jupyter 筆記本時，您會收到一則錯誤訊息，其中包含：

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>原因

版本不符。

## <a name="resolution"></a>解決方案

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到您的叢集。 以您叢集的名稱取代 CLUSTERNAME，然後輸入命令，以編輯下面的命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 藉由執行下列命令來開啟 `_version.py`：

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. 將**5**變更為**4** ，讓修改過的行顯示如下：

    ```python
    version_info = (4, 0, 3)
    ```

    輸入**Ctrl + X**、 **Y**、 **Enter**以儲存變更。

1. 從網頁瀏覽器流覽至 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是叢集的名稱。

1. 選取 [ **Jupyter** ]，然後重新開機服務。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 與[@AzureSupport](https://twitter.com/azuresupport)進行連接-官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
