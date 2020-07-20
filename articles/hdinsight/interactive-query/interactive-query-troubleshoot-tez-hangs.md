---
title: Apache Tez 應用程式在 Azure HDInsight 中停止回應
description: Apache Tez 應用程式在 Azure HDInsight 中停止回應
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: ec5a0d6e8c0a5236ae3929560e81033d983d4dfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895120"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>案例： Apache Tez 應用程式在 Azure HDInsight 中停止回應

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

提交 Apache Hive 作業之後，來自 Tez view 的作業狀態為「執行中」，但不會顯示任何進度

## <a name="cause"></a>原因

提交的作業過多;長 Yarn 佇列。

## <a name="resolution"></a>解決方案

相應增加叢集，或只是等到 Yarn 的佇列清空為止。

根據預設 `yarn.scheduler.capacity.maximum-applications` ，會控制正在執行或擱置中的應用程式數目上限，並預設為 `10000` 。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
