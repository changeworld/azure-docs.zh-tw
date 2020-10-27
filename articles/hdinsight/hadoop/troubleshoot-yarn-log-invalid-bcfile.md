---
title: 無法讀取 Azure HDInsight 中的 Apache Yarn 記錄檔
description: 針對與 Azure HDInsight 叢集互動時的問題進行疑難排解的步驟和可能的解決方式。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: b6bd7d807916ef53177b11df6ed9ce0b22f530be
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533337"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>案例：無法讀取 Azure HDInsight 中的 Apache Yarn 記錄檔

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

從儲存體帳戶找到的 Apache Yarn 記錄不是人類可讀取的。 檔案剖析器無法運作，而且會產生下列錯誤訊息：

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>原因

Apache Yarn 記錄會匯總成格式，檔案剖析器不支援此記錄檔 `IndexFile` 。

## <a name="resolution"></a>解決方案

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是叢集的名稱。

1. 從 Ambari UI 中，流覽至 **YARN**  >  **Configs**  >  **advanced advanced** advanced  >  **YARN-site** 。

1. 若為 WASB 儲存體：的預設值 `yarn.log-aggregation.file-formats` 為 `IndexedFormat,TFile` 。 將值變更為 `TFile` 。

1. 若為 ADLS 儲存體：的預設值 `yarn.nodemanager.log-aggregation.compression-type` 為 `gz` 。 將值變更為 `none` 。

1. 儲存變更並重新啟動所有受影響的服務。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。