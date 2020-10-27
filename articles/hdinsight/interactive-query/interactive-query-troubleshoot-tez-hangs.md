---
title: Apache Tez 應用程式在 Azure HDInsight 中停止回應
description: Apache Tez 應用程式在 Azure HDInsight 中停止回應
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 4e4d1ec77d989fd354e8b3938a052be94ff4ba34
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539627"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>案例： Azure HDInsight 中的 Apache Tez 應用程式停止回應

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

提交 Apache Hive 作業之後，Tez view 中的作業狀態為 [執行中]，但似乎沒有任何進度

## <a name="cause"></a>原因

提交的作業太多;長 Yarn 佇列。

## <a name="resolution"></a>解決方案

擴大叢集，或只等到 Yarn 佇列耗盡為止。

依預設 `yarn.scheduler.capacity.maximum-applications` ，會控制正在執行或擱置中的應用程式數目上限，而且預設為 `10000` 。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。