---
title: Apache Ambari Tez View Azure HDInsight 載入緩慢
description: Apache Ambari Tez View 可能載入速度很慢，或根本不會載入 Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 4fe66b3104be0351a9b0e1df6b6545f71ff276ab
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930758"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>案例： Apache Ambari Tez View Azure HDInsight 載入緩慢

本文說明使用 Azure HDInsight 叢集中的 Interactive Query 元件時，所發生之問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

Apache Ambari Tez View 可能載入速度很慢或根本不會載入。 載入 Ambari Tez View 時，您可能會看到前端節點上的進程變得沒有回應。

## <a name="cause"></a>原因

存取 Yarn ATS Api 有時可能會在叢集上有大量的 Hive 作業執行時，于2017年10月之前建立的叢集上發生效能不佳的情況。

## <a name="resolution"></a>解決方案

這是在2017年10月修正的問題。 重新建立叢集之後，就不會再次遇到這個問題。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]