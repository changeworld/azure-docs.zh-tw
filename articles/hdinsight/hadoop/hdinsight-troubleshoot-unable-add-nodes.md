---
title: 無法向 Azure HDInsight 群集添加節點
description: 解決無法將節點添加到 Azure HDInsight 中的 Apache Hadoop 群集的原因
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 97d7f34fff324a9959292460e534c15110c3e532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894087"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>方案：無法向 Azure HDInsight 群集添加節點

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

無法向 Azure HDInsight 群集添加節點。

## <a name="cause"></a>原因

原因可能有所不同。

## <a name="resolution"></a>解決方案

使用[群集大小](../hdinsight-scaling-best-practices.md)功能計算群集所需的其他內核數。 這是以新背景工作角色節點中的核心總數為基礎。 然後嘗試以下一個或多個步驟：

* 檢查群集位置中是否有任何可用內核。

* 查看位於其他位置的可用核心數目。 請考慮在具備足夠可用核心的不同位置，重新建立您的叢集。

* 若您想在特定位置增加核心配額，請針對 HDInsight 核心配額增加需求提出支援票證。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
