---
title: Apache Spark 串流應用程式會在 Azure HDInsight 24 天后停止
description: Apache Spark 串流應用程式會在執行24天之後停止，而且記錄檔中沒有任何錯誤。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894425"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>案例： Apache Spark 串流應用程式會在執行24天后停止 Azure HDInsight

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和問題的可能解決方法。

## <a name="issue"></a>問題

Apache Spark 串流應用程式會在執行24天之後停止，而且記錄檔中沒有任何錯誤。

## <a name="cause"></a>原因

`livy.server.session.timeout`值控制 Apache Livy 應等候會話完成的時間長度。 一旦會話長度達到`session.timeout`值，就會自動終止 Livy 會話和應用程式。

## <a name="resolution"></a>解決方法

對於長時間執行的作業，請`livy.server.session.timeout`使用 Ambari UI 來增加的值。 您可以使用 URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`從 Ambari UI 存取 Livy 設定。

將`<yourclustername>`取代為您的 HDInsight 叢集名稱，如入口網站中所示。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
