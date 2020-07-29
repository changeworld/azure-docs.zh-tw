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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894087"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>案例：無法將節點新增至 Azure HDInsight 叢集

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

無法將節點新增至 Azure HDInsight 叢集。

## <a name="cause"></a>原因

原因可能有所不同。

## <a name="resolution"></a>解決方案

使用 [叢集[大小](../hdinsight-scaling-best-practices.md)] 功能，計算叢集所需的其他核心數目。 這是以新背景工作角色節點中的核心總數為基礎。 然後嘗試下列一個或多個步驟：

* 檢查叢集位置是否有任何可用的核心。

* 查看位於其他位置的可用核心數目。 請考慮在具備足夠可用核心的不同位置，重新建立您的叢集。

* 若您想在特定位置增加核心配額，請針對 HDInsight 核心配額增加需求提出支援票證。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
