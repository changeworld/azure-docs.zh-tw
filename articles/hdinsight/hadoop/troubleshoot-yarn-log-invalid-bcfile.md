---
title: 無法讀取 Azure HDInsight 中的 Apache Yarn 記錄檔
description: 針對與 Azure HDInsight 叢集互動時的問題進行疑難排解的步驟和可能的解決方式。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776192"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>案例：無法讀取 Azure HDInsight 中的 Apache Yarn 記錄檔

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

從儲存體帳戶找到的 Apache Yarn 記錄不是人們可讀取的。 檔案剖析器無法使用，並且會產生下列錯誤訊息：

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>原因

Apache Yarn 記錄檔會匯總成 `IndexFile` 格式，但檔案剖析器並不支援。

## <a name="resolution"></a>解析度

1. 從網頁瀏覽器流覽至 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是叢集的名稱。

1. 從 Ambari UI，流覽至**YARN** >  ** > ** [ **advanced** ] > **advanced YARN-site**。

1. 針對 WASB 儲存體： `yarn.log-aggregation.file-formats` 的預設值為 `IndexedFormat,TFile`。 將值變更為 `TFile`。

1. 針對 ADLS 儲存體： `yarn.nodemanager.log-aggregation.compression-type` 的預設值為 `gz`。 將值變更為 `none`。

1. 儲存變更並重新啟動所有受影響的服務。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 與[@AzureSupport](https://twitter.com/azuresupport)進行連接-官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
