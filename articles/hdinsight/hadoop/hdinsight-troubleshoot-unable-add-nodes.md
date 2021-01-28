---
title: 無法將節點新增至 Azure HDInsight 叢集
description: 針對無法在 Azure HDInsight 中將節點新增至 Apache Hadoop 叢集的問題進行疑難排解
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: b11d1edef2f3a6fa0fb39c76d1f25ec05ff15d07
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944318"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>案例：無法將節點新增至 Azure HDInsight 叢集

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

無法將節點新增至 Azure HDInsight 叢集。

## <a name="cause"></a>原因

原因可能會有所不同。

## <a name="resolution"></a>解決方案

使用 [叢集 [大小](../hdinsight-scaling-best-practices.md) ] 功能，計算叢集所需的其他核心數目。 這是以新背景工作角色節點中的核心總數為基礎。 然後嘗試下列一或多個步驟：

* 檢查叢集的位置是否有任何可用的核心。

* 查看位於其他位置的可用核心數目。 請考慮在具備足夠可用核心的不同位置，重新建立您的叢集。

* 若您想在特定位置增加核心配額，請針對 HDInsight 核心配額增加需求提出支援票證。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]