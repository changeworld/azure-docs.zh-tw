---
title: Azure HDInsight 中 Apache Hive 的閘道深入探討和最佳作法
description: 瞭解如何流覽透過 Azure HDInsight 閘道執行 Hive 查詢的最佳作法
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 63484d882d8ccd387257c6f246c2048a09c77bc8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933114"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Hive 的閘道深入探討和最佳作法

Azure HDInsight 閘道 (閘道) 是適用于 HDInsight 叢集的 HTTPS 前端。 閘道負責：驗證、主機解析、服務探索，以及新式分散式系統所需的其他實用功能。 閘道所提供的功能會產生一些額外負荷，這份檔將說明流覽的最佳作法。 此外也會討論閘道疑難排解技術。

## <a name="the-hdinsight-gateway"></a>HDInsight 閘道

HDInsight 閘道是 HDInsight 叢集的唯一部分，可透過網際網路公開存取。 您可以使用 `clustername-int.azurehdinsight.net` 內部閘道端點來存取閘道服務，而不需要通過公用網際網路。 內部閘道端點可讓您建立閘道服務的連線，而不需要離開叢集的虛擬網路。 閘道會處理傳送至叢集的所有要求，並將這類要求轉送至正確的元件和叢集主機。

下圖提供有關閘道如何在 HDInsight 內所有不同主機解析度的可能性之前提供抽象概念的粗略說明。

![主機解析圖表](./media/gateway-best-practices/host-resolution-diagram.png "主機解析圖表")

## <a name="motivation"></a>動機

將閘道放在 HDInsight 叢集前方的動機，是提供服務探索和使用者驗證的介面。 閘道所提供的驗證機制與啟用 ESP 的叢集特別相關。

針對服務探索，閘道的優點是可將叢集內的每個元件當作閘道網站下的不同端點來存取 ( `clustername.azurehdinsight.net/hive2`) ，而不是許多 `host:port` 配對。

針對驗證，閘道可讓使用者使用認證組進行驗證 `username:password` 。 針對啟用 ESP 的叢集，此認證會是使用者的網域使用者名稱和密碼。 透過閘道對 HDInsight 叢集進行驗證時，用戶端不需要取得 kerberos 票證。 由於閘道 `username:password` 會接受認證並代表使用者取得使用者的 Kerberos 票證，因此可以從任何用戶端主機對閘道進行安全連線，包括用戶端加入不同 AA-DDS 網域的用戶端，而不是 (ESP) 叢集。

## <a name="best-practices"></a>最佳作法

閘道是單一服務 (在兩部主機之間進行負載平衡，) 負責要求轉送和驗證。 閘道可能會成為超過特定大小之 Hive 查詢的輸送量瓶頸。 當透過 ODBC 或 JDBC 在閘道上執行大型 **SELECT** 查詢時，可能會觀察到查詢效能降低。 「非常大型」表示在資料列或資料行之間組成超過 5 GB 資料的查詢。 此查詢可能包含一長串的資料列和或寬資料行計數。

因為資料必須從基礎資料存放區傳輸 (ADLS Gen2) 至： HDInsight Hive 伺服器、閘道，最後透過 JDBC 或 ODBC 驅動程式傳送給用戶端主機，所以閘道的效能會降低。

下圖說明 SELECT 查詢中所涉及的步驟。

![結果圖表](./media/gateway-best-practices/result-retrieval-diagram.png "結果圖表")

Apache Hive 是與 HDFS 相容的檔案系統之上的關聯式抽象概念。 此抽象概念表示 Hive 中的 **SELECT** 語句對應至檔案系統上的 **讀取** 作業。 **讀取** 作業會在向使用者回報之前，先轉譯成適當的架構。 此進程的延遲會增加，且需要資料大小和觸及使用者所需的總躍點。

當執行大型資料的 **CREATE** 或 **INSERT** 語句時，可能會發生類似的行為，因為這些命令會對應到基礎檔案系統中的 **寫入** 作業。 請考慮將資料（例如 raw ORC）寫入至 filesystem/datalake，而不是使用 **INSERT** 或 **LOAD** 載入它。

在啟用企業安全性套件的叢集中，充分複雜的 Apache Ranger 原則可能會導致查詢編譯時間變慢，這可能會導致閘道超時。 如果 ESP 叢集中已注意到閘道超時，請考慮減少或合併 ranger 原則的數目。

## <a name="troubleshooting-techniques"></a>疑難排解技術

有多個場地可減輕和瞭解在上述行為中符合的效能問題。 當透過 HDInsight 閘道遇到查詢效能降低時，請使用下列檢查清單：

* 執行大型 **SELECT** 查詢時，請使用 **LIMIT** 子句。 **LIMIT** 子句將會減少回報給用戶端主機的總數據列。 **LIMIT** 子句只會影響結果產生，而不會變更查詢計劃。 若要將 **LIMIT** 子句套用至查詢計劃，請使用此設定 `hive.limit.optimize.enable` 。 **限制** 可以與使用引數表單 **限制 x，y** 的位移合併。

* 執行 **SELECT** 查詢時，請為您感興趣的資料行命名，而不是使用 **SELECT \** _。 選取較少的資料行將會降低讀取的資料量。

_ 嘗試透過 Apache Beeline 執行感興趣的查詢。 如果透過 Apache Beeline 的結果抓取需要很長一段時間，則會在透過外部工具抓取相同結果時預期會發生延遲。

* 測試基本的 Hive 查詢，以確保能夠建立與 HDInsight 閘道的連線。 嘗試從兩個或多個外部工具執行基本查詢，以確定沒有任何個別工具正在發生問題。

* 請檢查任何 Apache Ambari 警示，以確定 HDInsight 服務狀況良好。 Ambari 警示可以與 Azure 監視器整合，以進行報告和分析。

* 如果您使用的是外部 Hive 中繼存放區，請檢查 Hive 中繼存放區的 Azure SQL DB DTU 是否未達到其限制。 如果 DTU 已接近其限制，您將需要增加資料庫大小。

* 確定任何協力廠商工具（例如 PBI 或 Tableau）都使用分頁來查看資料表或資料庫。 如需有關分頁的協助，請洽詢支援合作夥伴以取得相關協助。 用於分頁的主要工具是 JDBC `fetchSize` 參數。 較小的提取大小可能會導致閘道效能降低，但提取的大小太大可能會導致閘道超時。 提取大小調整必須以工作負載為基礎進行。

* 如果您的資料管線牽涉到從 HDInsight 叢集的基礎儲存體讀取大量資料，請考慮使用直接與 Azure 儲存體（例如 Azure Data Factory）介面的工具。

* 執行互動式工作負載時，請考慮使用 Apache Hive LLAP，因為 LLAP 可能會提供快速傳回查詢結果的流暢體驗

* 請考慮增加可供 Hive 中繼存放區服務使用的執行緒數目 `hive.server2.thrift.max.worker.threads` 。 當大量並行使用者將查詢提交至叢集時，此設定特別相關

* 減少用來從任何外部工具連接閘道的重試次數。 如果使用多個重試，請考慮遵循指數反向重試原則

* 請考慮使用設定和來啟用壓縮 Hive `hive.exec.compress.output` `hive.exec.compress.intermediate` 。

## <a name="next-steps"></a>後續步驟

* [HDInsight 上的 Apache Beeline](../hadoop/apache-hadoop-use-hive-beeline.md)
* [HDInsight 閘道超時疑難排解步驟](./troubleshoot-gateway-timeout.md)
* [適用于 HDInsight 的虛擬網路](../hdinsight-plan-virtual-network-deployment.md)
* [具有 Express Route 的 HDInsight](../connect-on-premises-network.md)