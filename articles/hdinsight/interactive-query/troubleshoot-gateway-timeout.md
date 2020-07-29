---
title: 從 Azure HDInsight 中的 Apache Ambari Hive View 執行查詢時發生例外狀況
description: 在 Azure HDInsight 中透過 Apache Ambari Hive View 執行 Apache Hive 查詢時的疑難排解步驟。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895041"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>從 Azure HDInsight 中的 Apache Ambari Hive View 執行查詢時發生例外狀況

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

從 Apache Ambari Hive View 執行 Apache Hive 查詢時，您會間歇地收到下列錯誤訊息：

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>原因

閘道超時。

閘道超時值為2分鐘。 來自 Ambari Hive View 的查詢會 `/hive2` 透過閘道提交至端點。 成功編譯並接受查詢之後，HiveServer 會傳回 `queryid` 。 接著，用戶端會持續輪詢查詢的狀態。 在此程式中，如果 HiveServer 未在2分鐘內傳回 HTTP 回應，HDI 閘道就會擲回呼叫端的502.3 閘道逾時錯誤。 提交查詢以進行處理（較可能），以及在 get 狀態呼叫中（較不可能）時，可能會發生錯誤。 使用者可以看到其中一個。

Http 處理常式執行緒應該會快速：準備作業並傳回 `queryid` 。 不過，由於幾個原因，所有處理常式執行緒可能會忙碌，因而導致新查詢和取得狀態呼叫的超時。

### <a name="responsibilities-of-the-http-handler-thread"></a>HTTP 處理常式執行緒的責任

當用戶端將查詢提交至 HiveServer 時，它會在前景執行緒中執行下列動作：

* 剖析要求，執行語意驗證
* 取得鎖定
* 視需要中繼存放區查閱
* 編譯查詢（DDL 或 DML）
* 準備查詢計劃
* 執行授權（在安全的叢集中執行所有適用的 ranger 原則）

## <a name="resolution"></a>解決方案

以下是改善情況的一般建議：

* 如果使用外部 hive 中繼存放區，請檢查 DB 計量，並確定資料庫未超載。 請考慮調整中繼存放區資料庫層。

* 確定平行作業已開啟（這可讓 HTTP 處理常式執行緒以平行方式執行）。 若要確認此值，請啟動[Apache Ambari](../hdinsight-hadoop-manage-ambari.md) ，並流覽至**hive 配置**的  >  **Configs**  >  **Advanced**  >  **自訂 hive-site**。 的值 `hive.server2.parallel.ops.in.session` 應該是 `true` 。

* 請確定叢集的 VM SKU 不會太小而無法載入。 請考慮在多個叢集之間分割工作。 如需詳細資訊，請參閱[選擇叢集類型](../hdinsight-capacity-planning.md#choose-a-cluster-type)。

* 如果 Ranger 安裝在叢集上，請檢查是否有太多的 Ranger 原則需要針對每個查詢進行評估。 尋找重複或不必要的原則。

* 檢查 Ambari 的 [ **HiveServer2 堆積大小**] 值。 流覽至**Hive 配置**  >  **Configs**  >  **設定**  >  **優化**。 請確定值大於 10 GB。 視需要調整以優化效能。

* 請確定 Hive 查詢已妥善調整。 如需詳細資訊，請參閱[Azure HDInsight 中的優化 Apache Hive 查詢](../hdinsight-hadoop-optimize-hive-query.md)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
