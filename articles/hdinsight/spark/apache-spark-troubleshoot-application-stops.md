---
title: Apache Spark 串流應用程式會在 Azure HDInsight 24 天后停止
description: Apache Spark 串流應用程式會在執行24天后停止，且記錄檔中沒有任何錯誤。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 83bdb4a9913ae817204fb37320f5bdb8174d5baf
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288023"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>案例： Apache Spark 串流應用程式在執行24天后停止 Azure HDInsight

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和可能的解決方案。

## <a name="issue"></a>問題

Apache Spark 串流應用程式會在執行24天后停止，且記錄檔中沒有任何錯誤。

## <a name="cause"></a>原因

此 `livy.server.session.timeout` 值會控制 Apache Livy 應等候會話完成的時間長度。 一旦會話長度達到此 `session.timeout` 值，就會自動終止 Livy 會話和應用程式。

## <a name="resolution"></a>解決方法

針對長時間執行的作業，請增加 `livy.server.session.timeout` 使用 AMBARI UI 的價值。 您可以使用 URL 從 Ambari UI 存取 Livy 設定 `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` 。

以 `<yourclustername>` 您的 HDInsight 叢集名稱取代，如入口網站中所示。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]