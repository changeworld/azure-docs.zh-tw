---
title: 歸納器在 Azure HDInsight 中緩慢
description: Azure HDInsight 可能扭曲的資料，歸納器變慢
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8a9c7ed9f6b5b8ec89bfca6dd59034b11f05f9a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895170"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>案例： Azure HDInsight 中的歸納器速度緩慢

本文說明在 Azure HDInsight 叢集中使用互動式查詢元件時，針對問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

執行查詢計劃之類`insert into table1 partition(a,b) select a,b,c from table2`的查詢時，會啟動一堆歸納器，但是每個分割區中的資料會移至單一歸納器。 這會導致查詢速度變慢，因為最大資料分割的歸納器所花費的時間。

## <a name="cause"></a>原因

開啟[beeline](../hadoop/apache-hadoop-use-hive-beeline.md) ，並確認 set `hive.optimize.sort.dynamic.partition`的值。

這個變數的值應該根據資料的本質，設定為 true/false。

如果輸入資料表中的資料分割較少（例如小於10），而輸出分割區的數目是，而變數設定為`true`，這會導致資料以全域方式排序，並使用每個分割區的單一歸納器寫入。 即使可用的歸納器數目較大，還是有少數歸納器可能會因為資料扭曲而延遲，而且無法達到最大平行處理原則。 當變更為`false`時，多個歸納器可能會處理單一分割區，而且會寫出多個較小的檔案，進而加快插入速度。 這可能會影響進一步的查詢，因為有較小的檔案。

當分割區`true`數目較大，且資料不會扭曲時，值就很合理。 在這種情況下，將會寫出對應階段的結果，讓每個資料分割都由單一歸納器處理，進而提升後續的查詢效能。

## <a name="resolution"></a>解決方法

1. 嘗試重新分割資料，以正規化為多個磁碟分割。

1. 如果 #1 不可行，請將 beeline 會話中的 config 值設定為 false，然後再次嘗試查詢。 `set hive.optimize.sort.dynamic.partition=false`. 不建議在叢集層級將值設定為 false。 的值`true`是最佳的，而且會視資料和查詢的本質，視需要設定參數。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
