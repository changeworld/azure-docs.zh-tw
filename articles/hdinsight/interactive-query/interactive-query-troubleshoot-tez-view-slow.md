---
title: 阿帕奇·安巴里·特斯視圖在 Azure HDInsight 中緩慢載入
description: Apache Ambari Tez 視圖可能在 Azure HDInsight 中載入緩慢或根本不載入
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f4b1ffbc1e5b8147279d1e0320bd5f55aec90ea2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895100"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>方案：Apache Ambari Tez 視圖在 Azure HDInsight 中載入緩慢

本文介紹了在 Azure HDInsight 群集中使用互動式查詢元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

阿帕奇安巴里特斯視圖可能載入緩慢或可能根本不載入。 載入 Ambari Tez 視圖時，您可能會看到頭節點上的進程變得無回應。

## <a name="cause"></a>原因

在 2017 年 10 月之前創建的群集上，訪問 Yarn ATS API 時，當群集上運行大量 Hive 作業時，有時性能不佳。

## <a name="resolution"></a>解決方案

此問題已在 2017 年 10 月修復。 重新創建群集將使它不再遇到此問題。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
