---
title: Azure HDInsight 中 Apache Hive 的閘道深入探討和最佳作法
description: 瞭解如何在 Azure HDInsight 閘道上流覽執行 Hive 查詢的最佳作法
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80586973"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Hive 的閘道深入探討和最佳作法

Azure HDInsight 閘道（閘道）是適用于 HDInsight 叢集的 HTTPS 前端。 閘道負責：驗證、主機解析、服務探索，以及新式分散式系統所需的其他有用功能。 閘道所提供的功能會產生一些額外負荷，這份檔會說明流覽的最佳做法。 也會討論閘道疑難排解技術。

## <a name="the-hdinsight-gateway"></a>HDInsight 閘道

HDInsight 閘道是可透過網際網路公開存取之 HDInsight 叢集的唯一部分。 您可以使用`clustername-int.azurehdinsight.net`內部閘道端點來存取閘道服務，而不需要透過公用網際網路。 內部閘道端點可讓您建立閘道服務的連線，而不需要離開叢集的虛擬網路。 閘道會處理傳送至叢集的所有要求，並將這類要求轉送到正確的元件和叢集主機。

下圖提供如何在 HDInsight 內的所有不同主機解析可能性前面提供抽象概念的粗略說明。

![主機解析度圖表](./media/gateway-best-practices/host-resolution-diagram.png "主機解析度圖表")

## <a name="motivation"></a>動機

將閘道放在 HDInsight 叢集前方的動機，是提供服務探索和使用者驗證的介面。 閘道所提供的驗證機制與啟用 ESP 的叢集特別相關。

針對服務探索，閘道的優點是可以將叢集中的每個元件當作閘道網站（ `clustername.azurehdinsight.net/hive2`）下的不同端點來存取，而不是使用許多`host:port`配對。

若為驗證，閘道可讓使用者使用`username:password`認證組進行驗證。 對於已啟用 ESP 的叢集，此認證會是使用者的網域使用者名稱和密碼。 透過閘道向 HDInsight 叢集進行驗證，不需要用戶端取得 kerberos 票證。 因為閘道會`username:password`接受認證，並代表使用者取得使用者的 Kerberos 票證，所以可以從任何用戶端主機對閘道進行安全連線，包括加入至不同于（ESP）叢集之 AA-DDS 網域的用戶端。

## <a name="best-practices"></a>最佳作法

閘道是單一服務（兩部主機之間的負載平衡），負責要求轉送和驗證。 如果 Hive 查詢超過特定大小，閘道可能會變成輸送量瓶頸。 當透過 ODBC 或 JDBC 在閘道上執行非常大型的**SELECT**查詢時，可能會觀察到查詢效能降低。 「非常大」表示在多個資料列或資料行上組成 5 GB 以上資料的查詢。 此查詢可能包含一長串的資料列和，或寬資料行計數。

因為資料必須從基礎資料存放區（ADLS Gen2）傳輸到： HDInsight Hive 伺服器、閘道，最後透過 JDBC 或 ODBC 驅動程式傳送到用戶端主機，所以閘道效能降低的情況是因為查詢大小過大。

下圖說明與 SELECT 查詢相關的步驟。

![結果圖表](./media/gateway-best-practices/result-retrieval-diagram.png "結果圖表")

Apache Hive 是在 HDFS 相容檔案系統之上的關聯式抽象概念。 此抽象概念表示 Hive 中的**SELECT**語句對應至檔案系統上的**讀取**作業。 **讀取**作業會先轉譯為適當的架構，然後才向使用者回報。 此程式的延遲會隨著使用者所需的資料大小和總躍點數而增加。

執行大型資料的**CREATE**或**INSERT**語句時，可能會發生類似的行為，因為這些命令會對應到基礎檔案系統中的**寫入**作業。 請考慮將資料（例如原始 ORC）寫入 filesystem/datalake，而不是使用**INSERT**或**LOAD**來載入它。

在已啟用企業安全性套件的叢集中，足夠複雜的 Apache Ranger 原則可能會導致查詢編譯時間變慢，這可能會導致閘道超時。 如果 ESP 叢集中已注意到閘道超時，請考慮減少或合併 ranger 原則的數目。

## <a name="troubleshooting-techniques"></a>疑難排解技術

有多個地點可減輕及瞭解上述行為中所符合的效能問題。 當透過 HDInsight 閘道遇到查詢效能降低時，請使用下列檢查清單：

* 執行大型**SELECT**查詢時，請使用**LIMIT**子句。 **LIMIT**子句會減少回報給用戶端主機的總數據列數。 **LIMIT**子句只會影響產生的結果，而且不會變更查詢計劃。 若要將**LIMIT**子句套用到查詢計劃，請使用設定`hive.limit.optimize.enable`。 **限制**可以與使用引數表單**限制 x，y**的位移結合。

* 在執行**select**查詢而非使用**select \*** 時，為您感的資料行命名。 選取較少的資料行將會降低讀取的資料量。

* 嘗試透過 Apache Beeline 執行感對的查詢。 如果透過 Apache Beeline 的結果抓取需要相當長的時間，則透過外部工具來抓取相同的結果時，預期會延遲。

* 測試基本 Hive 查詢，以確保可以建立與 HDInsight 閘道的連線。 請嘗試從兩個或多個外部工具執行基本查詢，以確定沒有任何個別工具遇到問題。

* 請檢查任何 Apache Ambari 警示，以確定 HDInsight 服務狀況良好。 Ambari 警示可以與 Azure 監視器整合，以進行報告和分析。

* 如果您使用的是外部 Hive 中繼存放區，請確認 Hive 中繼存放區的 Azure SQL DB DTU 未達到其限制。 如果 DTU 已接近其限制，您將需要增加資料庫大小。

* 確定任何協力廠商工具（例如 PBI 或 Tableau）都使用分頁來查看資料表或資料庫。 請向您的支援合作夥伴尋求這些工具，以取得分頁的協助。 用於分頁的主要工具是 JDBC `fetchSize`參數。 較小的提取大小可能會導致閘道效能降低，但提取大小過大可能會導致閘道超時。 提取大小調整必須以工作負載為基礎進行。

* 如果您的資料管線牽涉到從 HDInsight 叢集的基礎儲存體讀取大量資料，請考慮使用與 Azure 儲存體直接介面的工具，例如 Azure Data Factory

* 執行互動式工作負載時，請考慮使用 Apache Hive LLAP，因為 LLAP 可能會提供快速傳回查詢結果的更流暢體驗

* 請考慮使用`hive.server2.thrift.max.worker.threads`增加 Hive 中繼存放區服務可用的執行緒數目。 當大量的並行使用者提交查詢到叢集時，此設定特別相關

* 減少用來從任何外部工具連接到閘道的重試次數。 如果使用多個重試，請考慮遵循指數後重試原則

* 請考慮使用設定`hive.exec.compress.output`和`hive.exec.compress.intermediate`啟用壓縮 Hive。

## <a name="next-steps"></a>後續步驟

* [HDInsight 上的 Apache Beeline](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [HDInsight 閘道超時疑難排解步驟](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [HDInsight 的虛擬網路](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [具有 Express Route 的 HDInsight](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)