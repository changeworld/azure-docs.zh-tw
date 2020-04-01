---
title: 關於 Azure HDInsight 中的阿帕契卡夫卡常見問題解答
description: 在 Azure HDInsight(託管 Hadoop 雲服務)上獲取有關 Apache Kafka 的常見問題的解答。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: d27c648980338b3a9e12bd618eb4620fe9988fd7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436893"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache Kafka 常見問題集

本文討論了有關在 Azure HDInsight 上使用 Apache Kafka 的一些常見問題。

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>HDInsight 支援哪些卡夫卡版本?

在[HDInsight 提供的 Apache Hadoop 元件和版本中尋找](../hdinsight-component-versioning.md#supported-hdinsight-versions)有關 HDInsight 官方支援的元件版本的詳細資訊。 我們建議始終使用最新版本,以確保最佳性能和用戶體驗。

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>HDInsight Kafka 群集中提供了哪些資源,我收取哪些資源?

HDInsight Kafka 群集包括以下資源:

* 前端節點
* Zookeeper 節點
* 代理(輔助)節點 
* 連線到代理節點的 Azure 託管磁碟
* 閘道節點

所有這些資源都根據我們的[HDInsight 定價模型](https://azure.microsoft.com/pricing/details/hdinsight/)收費,網關節點除外。 網關節點不向您收費。

有關各種節點類型的更詳細的說明,請參閱 Azure [HDInsight 虛擬網路體系結構](../hdinsight-virtual-network-architecture.md)。 定價基於每分鐘節點使用方式。 價格因節點大小、節點數量、使用的託管磁碟類型和地區而異。

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>阿帕契卡夫卡 API 是否與 HDInsight 配合使用?

是的,HDInsight使用原生卡夫卡 API。 用戶端應用程式代碼不需要更改。 請參閱[教程:使用 Apache Kafka 生產者和消費者 API](./apache-kafka-producer-consumer-api.md)查看如何使用基於 Java 的生產者/消費者 API 與群集。

## <a name="can-i-change-cluster-configurations"></a>我可以更改群集配置嗎?

是的,通過安巴里門戶。 門戶中的每個元件都有一個**配置**部分,可用於更改元件配置。 某些更改可能需要重新啟動代理。

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>HDInsight 支援阿帕契卡夫卡的身份驗證類型?

使用[企業安全包 (ESP),](../domain-joined/apache-domain-joined-architecture.md)您可以為其 Kafka 叢集獲取主題級安全性。 有關詳細資訊[,請參閱教程:使用企業安全包(預覽)在 HDInsight 中設定 Apache Kafka 策略](../domain-joined/apache-domain-joined-run-kafka.md)。

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>我的數據是否加密? 我可以使用自己的密鑰嗎?

託管磁碟上的所有 Kafka 消息都使用[Azure 儲存服務加密 (SSE)](../../storage/common/storage-service-encryption.md)進行加密。 傳輸中的數據(例如,從用戶端傳輸到代理的數據,以及相反的數據)默認情況下不加密。 可以通過[自行設置 TLS](./apache-kafka-ssl-encryption-authentication.md)來加密此類流量。 此外,HDInsight 允許您管理自己的密鑰以加密靜態數據。 有關詳細資訊[,請參閱客戶管理的關鍵磁碟加密](../disk-encryption.md)。

## <a name="how-do-i-connect-clients-to-my-cluster"></a>如何將用戶端連接到群集?

對於卡夫卡客戶與卡夫卡經紀人溝通,他們必須能夠通過網路聯繫經紀人。 對於 HDInsight 群集,虛擬網路 (VNet) 是安全邊界。 因此,將用戶端連接到 HDInsight 群集的最簡單方法是在群集相同的 VNet 中創建用戶端。 其他專案包括:

* 將用戶端連接到其他 Azure VNet 中 - 對群集 VNet 和用戶端 VNet 進行對等,並為[IP 播發](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising)配置群集。 使用 IP 廣告時,Kafka 客戶端必須使用 Broker IP 位址與代理連接,而不是完全限定功能變數名稱 (FQDN)。

* 連接本地用戶端 – 使用 VPN 網路並設置自訂 DNS 伺服器,如[為 Azure HDInsight 規劃虛擬網路](../hdinsight-plan-virtual-network-deployment.md)時所述。

* 為 Kafka 服務創建公共終結點 - 如果企業安全要求允許,則可以為 Kafka 代理部署公共終結點,或者使用公共終結點部署自管理的開源 REST 終結點。

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>我可以在現有群集上添加更多磁碟空間嗎?

要增加 Kafka 消息的可用空間量,可以增加節點數。 目前,不支援向現有群集添加更多磁碟。

## <a name="can-a-kafka-cluster-work-with-databricks"></a>卡夫卡群集能否使用數據磚塊? 

是的,Kafka 群集可以使用 Databricks,只要它們位於同一個 VNet 中。 要將 Kafka 群集與 Databricks 一起使用,請建立一個 VNet,其中包含 HDInsight Kafka 群集,然後在創建 DataBRICKS 工作區並使用 VNet 注入時指定該 VNet。 如需詳細資訊，請參閱[在 Azure 虛擬網路中部署 Azure Databricks (VNet 插入)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)。 建立 DataBRICKS 工作區時,您需要提供 Kafka 叢集的引導代理名稱。 有關檢索卡夫卡經紀人名稱的資訊,請參閱獲取 Apache[動物園管理員和代理主機資訊](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo)。

## <a name="how-can-i-have-maximum-data-durability"></a>如何獲得最大的數據持久性?

數據持久性使您能夠實現消息丟失的最低風險。 為了實現最大的數據持久性,我們建議使用以下設置:

* 在大多數區域使用最小複製因數 3
* 在只有兩個容錯網域的區域中使用最小複製因數 4
* 停用不乾淨的領導者選舉
* 將**min.insync.replicas**設定為 2 個或更多 - 這將改變必須與引線完全同步的複本數,然後才能繼續寫入
* 將**acks**屬性設定為**所有**- 這個屬性要求所有複本確認所有訊息

配置 Kafka 以更高的數據一致性會影響代理生成請求的可用性。

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>我可以將數據複製到多個群集嗎?

是的,可以使用 Kafka 鏡像製造商將數據複製到多個群集。 有關設定鏡像製造商的詳細資訊,請參閱[鏡像阿帕契卡夫卡主題](apache-kafka-mirroring.md)。 此外,還有其他自我管理的開源技術和供應商可以幫助實現對多個群集(如[Brooklin)](https://github.com/linkedin/Brooklin/)的複製。

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>我可以升級我的群集嗎? 如何升級群集?

我們目前不支援就地群集版本升級。 要將群集更新為更高的 Kafka 版本,請建立一個包含所需版本的新群集,並遷移 Kafka 用戶端以使用新群集。

## <a name="how-do-i-monitor-my-kafka-cluster"></a>如何監視我的卡夫卡群集?

使用 Azure 監視器分析[Kafka 紀錄](./apache-kafka-log-analytics-operations-management.md)。

## <a name="next-steps"></a>後續步驟

* [在 Azure HDInsight 中為 Apache Kafka 設定 TLS 加密與身分驗證](./apache-kafka-ssl-encryption-authentication.md)
* [使用 MirrorMaker，透過 HDInsight 上的 Kafka 來複寫 Apache Kafka 主題](./apache-kafka-mirroring.md)
