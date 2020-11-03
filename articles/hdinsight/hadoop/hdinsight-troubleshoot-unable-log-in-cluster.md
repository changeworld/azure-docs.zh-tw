---
title: 無法登入 Azure HDInsight 叢集
description: 針對無法登入 Azure HDInsight 中 Apache Hadoop 叢集的原因進行疑難排解
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 222c48ab2b9b718bc373e710f37f906e68bd31d4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289021"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>案例：無法登入 Azure HDInsight 叢集

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

無法登入 Azure HDInsight 叢集中。

## <a name="cause"></a>原因

原因可能會有所不同。 請記住，當登入叢集或應用程式儀表板時，請使用您的「叢集登入」或 HTTP 認證。 從遠端連線時，請使用您的安全殼層 (SSH) 或遠端桌面認證。

## <a name="resolution"></a>解決方法

若要解決常見的問題，請嘗試下列步驟。

* 請在隱私權模式下，於新瀏覽器索引標籤中嘗試開啟 [叢集儀表板]。

* 如果您不記得您的 SSH 認證，您可以在 [AMBARI UI 中重設認證](../hdinsight-administer-use-portal-linux.md#change-passwords)。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]