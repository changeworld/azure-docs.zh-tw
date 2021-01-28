---
title: Azure HDInsight 中的 Apache Kafka 常見問題
description: 取得有關 Apache Kafka on Azure HDInsight （受控 Hadoop 雲端服務）的常見問題解答。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 851d7ceb44d2466ed31e26c1442fde9acea9fd22
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939106"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache Kafka 常見問題集

本文說明在 Azure HDInsight 上使用 Apache Kafka 的一些常見問題。

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>HDInsight 支援哪些 Kafka 版本？

如需 hdinsight 正式支援元件版本的詳細資訊，請查看 [hdinsight 提供的 Apache Hadoop 元件和版本有哪些？](../hdinsight-component-versioning.md#supported-hdinsight-versions)。 建議您一律使用最新版本，以確保最佳的效能和使用者體驗。

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>HDInsight Kafka 叢集中提供哪些資源，以及我要支付哪些資源？

HDInsight Kafka 叢集包含下列資源：

* 前端節點
* Zookeeper 節點
* Broker (背景工作) 節點 
* 附加至 broker 節點的 Azure 受控磁碟
* 閘道節點

除了閘道節點以外，所有這些資源都會根據我們的 [HDInsight 定價模型](https://azure.microsoft.com/pricing/details/hdinsight/)收費。 閘道節點不需要付費。

如需各種不同節點類型的詳細說明，請參閱 [Azure HDInsight 虛擬網路架構](../hdinsight-virtual-network-architecture.md)。 定價是以每分鐘節點的使用量為基礎。 價格會依節點大小、節點數目、使用的受控磁片類型和區域而有所不同。

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Apache Kafka Api 適用于 HDInsight 嗎？

是，HDInsight 會使用原生 Kafka Api。 您的用戶端應用程式程式碼不需要變更。 請參閱 [教學課程：使用 Apache Kafka 生產者和取用者 api](./apache-kafka-producer-consumer-api.md) 來瞭解如何搭配您的叢集使用以 JAVA 為基礎的生產者/取用者 api。

## <a name="can-i-change-cluster-configurations"></a>我可以變更叢集設定嗎？

是，透過 Ambari 入口網站。 入口網站中的每個元件都有一個 **配置區段，** 可用來變更元件設定。 某些變更可能需要重新開機代理程式。

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>HDInsight 對 Apache Kafka 支援何種類型的驗證？

使用 [企業安全性套件 (ESP) ](../domain-joined/apache-domain-joined-architecture.md)，您可以取得其 Kafka 叢集的主題層級安全性。 如需詳細資訊，請參閱 [教學課程：在 HDInsight 中使用企業安全性套件 (Preview) 設定 Apache Kafka 原則 ](../domain-joined/apache-domain-joined-run-kafka.md)。

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>我的資料是否加密？ 我可以使用自己的金鑰嗎？

受控磁片上的所有 Kafka 訊息都會使用 [Azure 儲存體的服務加密 (SSE) ](../../storage/common/storage-service-encryption.md)進行加密。 傳輸中資料 (例如，從用戶端傳輸至訊息代理程式的資料，以及根據預設，) 不會加密的資料。 [您可以自行設定 TLS](./apache-kafka-ssl-encryption-authentication.md)來加密這類流量。 此外，HDInsight 還可讓您管理自己的金鑰來加密待用資料。 如需詳細資訊，請參閱 [客戶管理的金鑰磁片加密](../disk-encryption.md)。

## <a name="how-do-i-connect-clients-to-my-cluster"></a>如何? 將用戶端連線至我的叢集？

為了讓 Kafka 用戶端與 Kafka 訊息代理程式通訊，他們必須能夠透過網路連線到代理程式。 針對 HDInsight 叢集，虛擬網路 (VNet) 是安全性界限。 因此，將用戶端連線到 HDInsight 叢集的最簡單方式，就是在與叢集相同的 VNet 內建立用戶端。 其他案例包括：

* 連接不同 Azure VNet 中的用戶端–對等互連叢集 VNet 和用戶端 VNet，並設定叢集以進行 [IP 公告](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising)。 使用 IP 公告時，Kafka 用戶端必須使用 Broker IP 位址來與訊息代理程式進行連線，而不是 (Fqdn) 的完整功能變數名稱。

* 連接內部部署用戶端–使用 VPN 網路和設定自訂 DNS 伺服器，如 [規劃虛擬網路以進行 Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md)所述。

* 為您的 Kafka 服務建立公用端點–如果您的企業安全性需求允許，您可以為 Kafka 訊息代理程式或自我管理的開放原始碼 REST 端點（具有公用端點）部署公用端點。

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>我可以在現有的叢集上新增更多磁碟空間嗎？

若要增加 Kafka 訊息的可用空間量，您可以增加節點數目。 目前不支援將更多磁片新增至現有的叢集。

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Kafka 叢集是否可以與 Databricks 搭配運作？ 

是的，只要 Kafka 叢集在相同的 VNet 中，就可以使用 Databricks。 若要使用具有 Databricks 的 Kafka 叢集，請在其中建立具有 HDInsight Kafka 叢集的 VNet，然後在您建立 Databricks 工作區並使用 VNet 插入時指定該 VNet。 如需詳細資訊，請參閱[在 Azure 虛擬網路中部署 Azure Databricks (VNet 插入)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)。 建立 Databricks 工作區時，您必須提供 Kafka 叢集的啟動程式 broker 名稱。 如需有關抓取 Kafka broker 名稱的詳細資訊，請參閱 [取得 Apache Zookeeper 和訊息代理程式主機資訊](./apache-kafka-get-started.md#getkafkainfo)。

## <a name="how-can-i-have-maximum-data-durability"></a>如何擁有最大的資料耐久性？

資料耐久性可讓您達成最低的訊息遺失風險。 為了達到最大的資料耐久性，建議您執行下列設定：

* 在大部分區域中使用最小複寫因數3
* 在只有兩個容錯網域的區域中使用最小複寫因數4
* 停用 unclean 領導人選舉
* 將 **同步 insync** 設定為2或以上-這會變更必須與領導者完全同步的複本數目，才能繼續進行寫入
* 將 [ **ack** ] 屬性設定為 [ **全部** ]-此屬性需要所有複本才能認可所有訊息

針對較高的資料一致性設定 Kafka，會影響訊息代理程式的可用性以產生要求。

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>我可以將我的資料複寫到多個叢集嗎？

是，您可以使用 Kafka MirrorMaker 將資料複寫到多個叢集。 如需有關設定 MirrorMaker 的詳細資訊，請參閱 [鏡像 Apache Kafka 主題](apache-kafka-mirroring.md)。 此外，還有其他自我管理的開放原始碼技術和廠商，可協助您達到 [Brooklin](https://github.com/linkedin/Brooklin/)等多個叢集的複寫。

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>我可以升級叢集嗎？ 如何升級我的叢集？

我們目前不支援就地叢集版本升級。 若要將叢集更新為較高的 Kafka 版本，請使用您想要的版本建立新的叢集，並遷移 Kafka 用戶端以使用新的叢集。

## <a name="how-do-i-monitor-my-kafka-cluster"></a>如何? 監視我的 Kafka 叢集嗎？

使用 Azure 監視器來分析您的 [Kafka 記錄](./apache-kafka-log-analytics-operations-management.md)。

## <a name="next-steps"></a>後續步驟

* [在 Azure HDInsight 中設定適用于 Apache Kafka 的 TLS 加密與驗證](./apache-kafka-ssl-encryption-authentication.md)
* [使用 MirrorMaker，透過 HDInsight 上的 Kafka 來複寫 Apache Kafka 主題](./apache-kafka-mirroring.md)