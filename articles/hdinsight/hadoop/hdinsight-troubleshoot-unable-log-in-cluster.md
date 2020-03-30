---
title: 無法登錄到 Azure HDInsight 群集
description: 解決無法登錄到 Azure HDInsight 中的 Apache Hadoop 群集的原因
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 2099d1b7583017733498946a5866ab37de43ba9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894062"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>方案：無法登錄到 Azure HDInsight 群集

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

無法登錄到 Azure HDInsight 群集。

## <a name="cause"></a>原因

原因可能有所不同。 請記住，登錄到群集或應用儀表板時，請使用"群集登錄"或 HTTP 憑據。 從遠端連線時，請使用您的安全殼層 (SSH) 或遠端桌面認證。

## <a name="resolution"></a>解決方案

若要解決常見的問題，請嘗試下列步驟。

* 請在隱私權模式下，於新瀏覽器索引標籤中嘗試開啟 [叢集儀表板]。

* 如果無法調用 SSH 憑據，則可以[在 Ambari UI 中重置憑據](../hdinsight-administer-use-portal-linux.md#change-passwords)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
