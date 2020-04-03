---
title: Azure HDInsight 中 Apache Hive 的閘道深度潛水和最佳實務
description: 瞭解如何導航透過 Azure HDInsight 閘道執行 Hive 查詢的最佳做法
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586973"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Hive 的閘道深度潛水和最佳實務

Azure HDInsight 閘道(閘道)是HDInsight群集的 HTTPS 前端。 閘道負責:身份驗證、主機解析、服務發現以及現代分散式系統所需的其他有用功能。 閘道提供的功能會導致一些開銷,本文檔將介紹要導航的最佳做法。 還討論了閘道故障排除技術。

## <a name="the-hdinsight-gateway"></a>HDInsight 閘道

HDInsight 閘道是 HDInsight 群集中唯一可在網路上公開存取的部分。 可以使用`clustername-int.azurehdinsight.net`內部閘道終結點存取閘道服務,而無需透過公共 Internet 訪問。 內部閘道終結點允許在不退出群集的虛擬網路的情況下建立到閘道服務的連接。 閘道處理發送到群集的所有請求,並將此類請求轉發到正確的元件和群集主機。

下圖粗略說明了閘道如何在 HDInsight 中的所有不同主機解析可能性面前提供抽象。

![主機解析圖](./media/gateway-best-practices/host-resolution-diagram.png "主機解析圖")

## <a name="motivation"></a>動機

將閘道置於 HDInsight 群集的前面的動機是為服務發現和使用者身份驗證提供介面。 閘道提供的身份驗證機制與啟用ESP的群集特別相關。

對於服務發現,閘道的優點是群集中的每個元件都可以在閘道網站 ()`clustername.azurehdinsight.net/hive2`下作為不同的終結點進行訪問,而不是`host:port`大量 配對。

對於身份驗證,閘道允許使用者使用憑據對進行`username:password`身份驗證。 對於啟用 ESP 的群集,此憑據將是使用者的域使用者名稱和密碼。 通過閘道對 HDInsight 群集進行身份驗證不需要客戶端獲取 kerberos 票證。 由於閘道接受`username:password`認證並代表使用者獲取使用者的 Kerberos 票證,因此可以從任何用戶端主機(包括連接到與 (ESP) 群集不同的 AA-DDS 網域的用戶端)與閘道建立安全連線。

## <a name="best-practices"></a>最佳作法

閘道是負責請求轉發和身份驗證的單個服務(跨兩個主機平衡負載)。 閘道可能成為超過特定大小的 Hive 查詢的輸送量瓶頸。 當通過 ODBC 或 JDBC 在閘道上執行非常大的**SELECT**查詢時,可能會觀察到查詢性能下降。 "非常大的"是指跨行或列占數據超過 5 GB 的查詢。 此查詢可以包括行和或寬列計數的長清單。

閘道圍繞大規模的查詢的效能下降是因為資料必須從基礎資料儲存 (ADLS Gen2) 傳輸到:HDInsight Hive 伺服器、閘道,最後透過 JDBC 或 ODBC 驅動程式傳輸到用戶端主機。

下圖說明瞭 SELECT 查詢中涉及的步驟。

![結果圖](./media/gateway-best-practices/result-retrieval-diagram.png "結果圖")

Apache Hive 是相容 HDFS 的檔案系統之上的關係抽象。 此抽象意味著 Hive 中的**SELECT**語句對應於檔案系統上的**READ**操作。 在向使用者報告之前 **,READ**操作將轉換為相應的架構。 此過程的延遲隨著數據大小和到達最終使用者所需的總躍點而增加。

在執行大型資料的**CREATE**或**INSERT**語句時,可能會出現類似的行為,因為這些命令將對應於基礎文件系統中的**WRITE**操作。 請考慮將資料(如原始 ORC)寫入檔案系統/資料湖,而不是使用**INSERT**或**LOAD**載入資料庫。

在啟用企業安全包的群集中,足夠複雜的 Apache Ranger 策略可能會導致查詢編譯時間變慢,從而導致網關超時。 如果在 ESP 群集中注意到閘道超時,請考慮減少或組合遊俠策略的數量。

## <a name="troubleshooting-techniques"></a>容損排除技術

作為上述行為的一部分,有多個緩解和理解績效問題的場所。 在 HDInsight 閘道上遇到查詢性能下降時,請使用以下檢查表:

* 在執行大型**SELECT**查詢時,請使用**LIMIT**子句。 **LIMIT**子句將減少報告給用戶端主機的總行數。 **LIMIT**子句僅影響結果生成,不會更改查詢計劃。 要將**LIMIT**子句套用於查詢計畫`hive.limit.optimize.enable`,請使用 設定 。 **LIMIT**可以使用參數形式**LIMIT x、y**與偏移組合。

* 在運行**SELECT**查詢而不是使用**SELECT\*** 時,請命名感興趣的欄。 選擇較少的列將降低數據讀取量。

* 嘗試透過 Apache Beeline 運行感興趣的查詢。 如果通過 Apache Beeline 檢索結果需要較長時間,則通過外部工具檢索相同結果時會出現延遲。

* 測試基本的 Hive 查詢,以確保可以建立與 HDInsight 閘道的連接。 嘗試從兩個或多個外部工具運行基本查詢,以確保沒有單個工具遇到問題。

* 查看任何阿帕契巴里警報,以確保HDInsight服務是健康的。 Ambari 警報可以與 Azure 監視器整合以進行報告和分析。

* 如果使用外部 Hive Metastore,請檢查 Hive Metastore 的 Azure SQL DB DTU 是否未達到其限制。 如果 DTU 接近其限制,則需要增加資料庫大小。

* 確保任何第三方工具(如 PBI 或 Tableau)都使用分頁來查看表或資料庫。 有關應用方面的説明,請諮詢您的支援合作夥伴。 用於分形的主要工具是 JDBC`fetchSize`參數。 較小的提取大小可能會導致閘道性能下降,但獲取大小過大可能會導致閘道超時。 提取大小調整必須基於工作負載完成。

* 如果資料導管涉及從 HDInsight 叢集的基礎儲存中讀取大量資料,請考慮使用直接與 Azure 儲存(如 Azure 資料工廠)介面的工具

* 在執行互動式工作負載時考慮使用 Apache Hive LLAP,因為 LLAP 可能會為快速返回查詢結果提供更流暢的體驗

* 請考慮增加使用`hive.server2.thrift.max.worker.threads`的 Hive Metastore 服務的可用線程數。 當大量併發使用者向群集提交查詢時,此設置尤其重要

* 減少從任何外部工具到達閘道的重試次數。 如果使用多個重試,請考慮遵循指數級回退重試策略

* 請考慮使用`hive.exec.compress.output`配置`hive.exec.compress.intermediate`和 啟用壓縮配置。

## <a name="next-steps"></a>後續步驟

* [在 HDInsight 上阿帕奇蜜蜂](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [HDInsight 閘道超時故障排除步驟](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [高清洞察虛擬網路](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [高清洞察與快速路線](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)