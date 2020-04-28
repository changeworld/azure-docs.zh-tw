---
title: 無法將節點新增至 Azure HDInsight 叢集
description: 疑難排解為何無法在 Azure HDInsight 中將節點新增至 Apache Hadoop 叢集
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894087"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>案例：無法將節點新增至 Azure HDInsight 叢集

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

無法將節點新增至 Azure HDInsight 叢集。

## <a name="cause"></a>原因

原因可能有所不同。

## <a name="resolution"></a>解決方法

使用 [叢集[大小](../hdinsight-scaling-best-practices.md)] 功能，計算叢集所需的其他核心數目。 這是以新背景工作角色節點中的核心總數為基礎。 然後嘗試下列一個或多個步驟：

* 檢查叢集位置是否有任何可用的核心。

* 查看位於其他位置的可用核心數目。 請考慮在具備足夠可用核心的不同位置，重新建立您的叢集。

* 若您想在特定位置增加核心配額，請針對 HDInsight 核心配額增加需求提出支援票證。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
