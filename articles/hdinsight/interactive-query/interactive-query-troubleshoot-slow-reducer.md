---
title: 在 Azure HDInsight 中，減速器速度緩慢
description: Azure HDInsight 中由於可能的資料偏斜而降低速度緩慢
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895170"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中，減速器速度緩慢

本文介紹了在 Azure HDInsight 群集中使用互動式查詢元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

執行查詢（如查詢計劃`insert into table1 partition(a,b) select a,b,c from table2`）時，將啟動一堆縮減器，但每個分區的資料將轉到單個縮減器。 這將導致查詢與最大分區的縮減器所佔用的時間一樣慢。

## <a name="cause"></a>原因

打開[蜂線](../hadoop/apache-hadoop-use-hive-beeline.md)並驗證集`hive.optimize.sort.dynamic.partition`的值。

此變數的值旨在根據資料的性質設置為真/假。

如果輸入表中的分區小於（例如小於 10），輸出分區的數量也較少，並且變數設置為`true`，則會導致對資料進行全域排序，並使用每個分區使用單個縮減器寫入資料。 即使可用的減速器數量較大，由於資料偏斜，少數減速器可能滯後，並且無法達到最大並行性。 當更改為`false`，多個縮減器可以處理單個分區，多個較小的檔將被寫入，從而導致更快的插入速度。 這可能會影響進一步的查詢，儘管因為存在較小的檔。

當分區數`true`較大且資料不傾斜時，值有意義。 在這種情況下，將寫入映射階段的結果，以便每個分區將由單個縮減程式處理，從而提供更好的後續查詢性能。

## <a name="resolution"></a>解決方案

1. 嘗試將資料重新分區以正常化為多個分區。

1. 如果無法#1，請將配置的值設置為蜜蜂會話中的 false，然後重試查詢。 `set hive.optimize.sort.dynamic.partition=false`. 不建議將該值設置為群集級別的 false。 的值`true`是最佳的，並根據資料和查詢的性質根據需要設置參數。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
