---
title: Apache Spark 流式處理應用程式在 Azure HDInsight 中 24 天后停止
description: Apache Spark 流式處理應用程式在執行 24 天后停止，日誌檔中沒有錯誤。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894425"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中執行 24 天后，Apache Spark 流式處理應用程式停止

本文介紹了在 Azure HDInsight 群集中使用 Apache Spark 元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

Apache Spark 流式處理應用程式在執行 24 天后停止，日誌檔中沒有錯誤。

## <a name="cause"></a>原因

該`livy.server.session.timeout`值控制 Apache Livy 應等待會話完成的時間。 會話長度達到`session.timeout`值後，Livy 會話和應用程式將自動終止。

## <a name="resolution"></a>解決方案

對於長時間運行的`livy.server.session.timeout`作業，增加使用 Ambari UI 的值。 您可以使用 URL`https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`從 Ambari UI 訪問利維配置。

替換為`<yourclustername>`門戶中顯示的 HDInsight 群集的名稱。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
