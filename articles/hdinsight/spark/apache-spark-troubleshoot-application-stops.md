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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894425"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>案例： Apache Spark 串流應用程式會在執行24天后停止 Azure HDInsight

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和可能的解決方案。

## <a name="issue"></a>問題

Apache Spark 串流應用程式會在執行24天之後停止，而且記錄檔中沒有任何錯誤。

## <a name="cause"></a>原因

`livy.server.session.timeout`值控制 Apache Livy 應等候會話完成的時間長度。 一旦會話長度達到 `session.timeout` 值，就會自動終止 Livy 會話和應用程式。

## <a name="resolution"></a>解決方案

對於長時間執行的作業，請使用 Ambari UI 來增加的值 `livy.server.session.timeout` 。 您可以使用 URL 從 Ambari UI 存取 Livy 設定 `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` 。

將取代為 `<yourclustername>` 您的 HDInsight 叢集名稱，如入口網站中所示。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
