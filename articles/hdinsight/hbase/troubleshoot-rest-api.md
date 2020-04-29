---
title: REST API 在 Azure HDInsight 中查詢 Apache HBase
description: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 3bf63aa08ec4c1deff2551cfcc0cf188a75261bc
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515478"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>REST API 在 Azure HDInsight 中查詢 Apache HBase

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

使用 Apache HBase REST 介面，在預設值以外的命名空間中查詢資料表會產生執行階段錯誤（HTTP 狀態500）。

## <a name="cause"></a>原因

只有在使用預設命名空間時，才支援 HBase REST API。 這是使用 HBase 命名空間的已知問題，或對具有資料行系列和 HDInsight 上的 REST 伺服器的資料行進行參考特定的呼叫。 這是因為 HDInsight 閘道的安全性問題所導致。 當您使用 API 建立具有命名空間的資料表時，透過資料行系列存取資料行時，您`:`需要指定字元，這會被視為 IIS 閘道模組中的安全性問題。

## <a name="mitigation"></a>緩和措施

將您的應用程式部署在與 HDInsight 叢集位於相同 Azure VNet 的 VM 上，以略過閘道/REST 伺服器。 然後您可以直接透過 RPC （略過 REST 伺服器），或在背景工作節點上執行的個別 REST 伺服器，略過 HDInsight 閘道來與 HBase 通訊。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
