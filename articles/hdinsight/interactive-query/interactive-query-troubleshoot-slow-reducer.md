---
title: Azure HDInsight 中的歸納器緩慢
description: 歸納器在可能的資料扭曲 Azure HDInsight 變慢
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895170"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>案例：歸納器在 Azure HDInsight 中的速度很慢

本文說明使用 Azure HDInsight 叢集中的 Interactive Query 元件時，所發生之問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

執行查詢計劃之類的查詢時，會 `insert into table1 partition(a,b) select a,b,c from table2` 啟動一堆歸納器，但是每個資料分割的資料會移至單一歸納器。 這會導致查詢的速度與最大資料分割的歸納器所花費的時間慢。

## <a name="cause"></a>原因

開啟 [beeline](../hadoop/apache-hadoop-use-hive-beeline.md) ，並確認設定的值 `hive.optimize.sort.dynamic.partition` 。

根據資料的本質，此變數的值應設定為 true/false。

如果輸入資料表中的資料分割小於 (表示小於 10) ，因此輸出分割區的數目和變數設為 `true` ，這會導致資料全域排序，並使用每個分割區的單一歸納器寫入資料。 即使歸納器可用的數目較大，可能會因為資料扭曲而延遲幾個歸納器，而且無法達到最大平行處理原則。 當變更為時 `false` ，有一個以上的歸納器可能會處理單一分割區，而且會寫出多個較小的檔案，進而加快插入速度。 因為有較小的檔案，所以這可能會影響進一步的查詢。

`true`當資料分割數目較大且資料不是扭曲時，的值就有意義。 在這種情況下，將會寫出對應階段的結果，讓每個分割區都由單一歸納器處理，進而提升後續查詢效能。

## <a name="resolution"></a>解決方案

1. 請嘗試重新分割資料，以正規化為多個資料分割。

1. 如果無法 #1，請在 beeline 會話中將設定的值設定為 false，然後再試一次查詢。 `set hive.optimize.sort.dynamic.partition=false`. 不建議在叢集層級將值設定為 false。 的值 `true` 是最理想的，而且根據資料和查詢的本質，視需要設定參數。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
