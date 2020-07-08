---
title: Jupyter 404 錯誤-「封鎖跨原始來源 API」-Azure HDInsight
description: Jupyter 伺服器404「找不到」錯誤，因為 Azure HDInsight 中的「封鎖跨來源 API」
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894418"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>案例： Jupyter 伺服器404「找不到」錯誤，因為 Azure HDInsight 中的「封鎖跨來源 API」

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和可能的解決方案。

## <a name="issue"></a>問題

當您存取 HDInsight 上的 Jupyter 服務時，您會看到錯誤方塊，指出「找不到」。 如果您檢查 Jupyter 記錄，將會看到類似下面的內容：

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

您也可能會在 Jupyter 記錄檔的 [來源] 欄位中看到 IP 位址。

## <a name="cause"></a>原因

這項錯誤可能是由下列幾個原因所造成：

- 如果您已設定網路安全性群組（NSG）規則來限制對叢集的存取權。 使用 NSG 規則來限制存取，仍然可讓您使用 IP 位址而非叢集名稱，直接存取 Apache Ambari 和其他服務。 不過，存取 Jupyter 時，您可能會看到404「找不到」錯誤。

- 如果您已為 HDInsight 閘道提供標準以外的自訂 DNS 名稱 `xxx.azurehdinsight.net` 。

## <a name="resolution"></a>解決方案

1. 修改這兩個位置中的 jupyter.py 檔案：

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. 找出顯示：的程式程式碼， `NotebookApp.allow_origin='\"https://{2}.{3}\"'` 然後將它變更為： `NotebookApp.allow_origin='\"*\"'` 。

1. 從 Ambari 重新開機 Jupyter 服務。

1. `ps aux | grep jupyter`在命令提示字元中輸入，應該會顯示它允許任何 URL 連接到它。

這比我們已備妥的設定更安全。 但是，系統會將叢集的存取權視為受限制，並允許從外部連接到叢集，因為我們已 NSG。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
