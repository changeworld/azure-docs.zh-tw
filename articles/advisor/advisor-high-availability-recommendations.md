---
title: 使用 Advisor 改善應用程式的可靠性
description: 使用 Azure Advisor 來確保和改善業務關鍵 Azure 部署的可靠性。
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 1e256d99f8d78ddff318f963dcb21e9b4537f110
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91405185"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>使用 Azure Advisor 來改善應用程式的可靠性

Azure Advisor 可協助您確保和改善業務關鍵應用程式的持續性。 您可以在 Advisor 儀表板的 [ **可靠性** ] 索引標籤上，從 advisor 取得可靠性建議。

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>檢查 Check Point 網路虛擬裝置映射的版本

Advisor 可以識別您的虛擬機器是否正在執行某個版本的 Check Point 映射，已知在平臺服務作業期間遺失網路連線能力。 Advisor 建議將協助您升級至可解決此問題的較新版本映射。 這種檢查可透過更好的網路連線能力來確保業務持續性。

## <a name="ensure-application-gateway-fault-tolerance"></a>確保應用程式閘道的容錯

這項建議可確保由應用程式閘道提供技術支援的任務關鍵性應用程式擁有商務持續性。 Advisor 會識別未設定容錯功能的應用程式閘道實例。 接著，它會建議您可以採取的補救動作。 Advisor 會識別中型或大型單一實例的應用程式閘道，並建議至少新增一個實例。 它也會識別單一實例和多個實例的小型應用程式閘道，並建議將它們遷移至中型或大型 Sku。 Advisor 會建議這些動作，以確保您的應用程式閘道實例已設定為符合這些資源目前的 SLA 需求。

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>防止意外刪除虛擬機器的資料

設定虛擬機器備份可確保業務關鍵資料的可用性，並防止資料意外刪除或損毀。 Advisor 會識別未啟用備份的虛擬機器，並建議啟用備份。 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>確定您在需要時可以存取 Azure 專家

當您執行業務關鍵工作負載時，請務必在需要時存取技術支援。 Advisor 會識別其支援方案中未包含技術支援的潛在業務關鍵訂閱。 建議您升級至包含技術支援的選項。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>建立 Azure 服務健康狀態警示，以在 Azure 問題影響您時收到通知

建議您設定 Azure 服務健康狀態警示，讓您在 Azure 服務問題影響您時收到通知。 [Azure 服務健康狀態](https://azure.microsoft.com/features/service-health/) 是一項免費服務，可在您受到 Azure 服務問題影響時，提供個人化的指導和支援。 Advisor 會識別未設定警示的訂閱，並建議進行設定。

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>設定流量管理員端點以供復原

如果任何指定的端點失敗，則具有多個端點體驗的 Azure 流量管理員設定檔會有更高的可用性。 將端點放在不同的區域能進一步改善服務可靠性。 Advisor 會識別流量管理員設定檔，其中只有一個端點，建議您在另一個區域中至少新增一個端點。

如果流量管理員設定檔中設定為近接路由的所有端點都位於相同區域中，則其他區域的使用者可能會遇到連線延遲。 如果一個區域中的所有端點都失敗，則將端點新增或移動到另一個區域會改善整體效能，並提供更佳的可用性。 Advisor 會識別設定為近接路由 (其中所有的端點都位於相同區域) 的流量管理員設定檔。 建議將端點新增或移動到另一個 Azure 區域。

如果流量管理員設定檔設定為地理路由，流量會根據定義的區域路由傳送至端點。 如果區域失敗，則沒有預先定義的容錯移轉。 如果您有一個端點，其中區域群組已設定為 **所有 (世界) **，您可以避免中斷的流量並改善服務可用性。 Advisor 會識別設定為地理路由的流量管理員設定檔，其中沒有任何端點設定為 **所有 (世界) **的區域群組。 建議您變更設定，讓端點 **全部 (世界) **。

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>在您的 Azure 儲存體帳戶上使用虛刪除，以在意外覆寫或刪除後儲存及復原資料

在儲存體帳戶上啟用[虛刪除](../storage/blobs/soft-delete-overview.md)，可讓刪除的 Blob 轉換為虛刪除狀態，而不是永久刪除。 當資料遭到覆寫時，系統會產生虛刪除的快照集，以儲存覆寫資料的狀態。 使用虛刪除可讓您從意外刪除或覆寫復原。 Advisor 會識別未啟用虛刪除的 Azure 儲存體帳戶，並建議您加以啟用。

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>將您的 VPN 閘道設定為主動-主動以得到連線復原能力

在主動-主動設定中，VPN 閘道的兩個實例會建立 S2S VPN 通道給您的內部部署 VPN 裝置。 當一個閘道實例發生預定的維修事件或未規劃的事件時，流量會自動切換到其他作用中的 IPsec 通道。 Azure Advisor 識別未設定為主動-主動的 VPN 閘道，並建議您將其設定為高可用性。

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>使用生產 VPN 閘道來執行生產工作負載

Azure Advisor 會檢查是否有任何使用基本 SKU 的 VPN 閘道，並建議您改為使用生產 SKU。 基本 SKU 是針對開發和測試所設計。 生產 Sku 供應專案：
- 更多通道。 
- BGP 支援。 
- 主動-主動設定選項。 
- 自訂 Ipsec/IKE 原則。 
- 更高的穩定性和可用性。

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>確定 (暫時停用虛擬機器容錯) 

若要為您的應用程式提供冗余，建議您將兩部或多部虛擬機器組成一個可用性設定組。 Advisor 會識別不屬於可用性設定組的虛擬機器，並建議將它們移至其中。 這項設定可確保在規劃或未規劃的維護期間，至少有一部虛擬機器可供使用，且符合 Azure 虛擬機器 SLA。 您可以選擇為虛擬機器建立可用性設定組，或是將虛擬機器新增至現有的可用性設定組。

> [!NOTE]
> 如果您選擇建立可用性設定組，您需要在其中新增至少一部虛擬機器。 我們建議讓兩部或多部虛擬機器組成一個可用性設定組，以確保至少有一部機器可用於中斷期間。

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>確定可用性設定組容錯 (暫時停用) 

若要為您的應用程式提供冗余，建議您將兩部或多部虛擬機器組成一個可用性設定組。 Advisor 會識別包含單一虛擬機器的可用性設定組，並建議將一部或多部虛擬機器新增至該可用性設定組。這項設定可確保在規劃或未規劃的維護期間，至少有一部虛擬機器可供使用，且符合 Azure 虛擬機器 SLA。您可以選擇建立虛擬機器，或是將現有的虛擬機器新增至可用性設定組。  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>使用受控磁片來改善 (暫時停用的資料可靠性) 

可用性設定組中的虛擬機器若有共用儲存體帳戶或儲存體縮放單位的磁片，就無法在中斷時復原到單一儲存體縮放單位失敗。 Advisor 會識別這些可用性設定組，並建議遷移至 Azure 受控磁片。 這項遷移可確保可用性設定組中虛擬機器的磁片充分隔離，以避免單一失敗點。 

## <a name="repair-invalid-log-alert-rules"></a>修復不正確記錄警示規則

Azure Advisor 會偵測在其條件區段中指定了無效查詢的記錄警示規則。 Azure 監視器記錄警示規則會以指定的頻率執行查詢，並根據結果引發警示。 由於參考的資源、資料表或命令的變更，查詢可能會在一段時間後變成無效。 Advisor 建議針對警示查詢進行修正，以防止規則自動停用，並確保監視涵蓋範圍。 如需詳細資訊，請參閱 [疑難排解警示規則](../azure-monitor/platform/alerts-troubleshoot-log.md#query-used-in-a-log-alert-isnt-valid)

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>在 Azure Cosmos DB 集合上設定一致的索引模式

使用延遲編制索引模式設定 Azure Cosmos DB 的容器可能會影響查詢結果的有效性。 Advisor 會偵測以這種方式設定的容器，並建議切換至一致模式。 [深入瞭解 Azure Cosmos DB 中的編制索引原則。](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>使用分割區索引鍵來設定 Azure Cosmos DB 容器

Azure Advisor 識別已接近其布建儲存體配額 Azure Cosmos DB 非資料分割的集合。 建議您使用分割區索引鍵定義將這些集合遷移至新的集合，讓服務可以自動相應放大。 [深入瞭解如何選擇資料分割索引鍵。](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>將您的 Azure Cosmos DB .NET SDK 從 NuGet 升級至最新版本

Azure Advisor 識別使用舊版 .NET SDK 的 Azure Cosmos DB 帳戶。 建議您從 NuGet 升級至最新版本，以取得最新修正、效能改進和功能功能。 [深入瞭解 Azure Cosmos DB .NET SDK。](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>從 Maven 將 Azure Cosmos DB Java SDK 升級為最新版本

Azure Advisor 識別使用舊版 JAVA SDK 的 Azure Cosmos DB 帳戶。 建議您從 Maven 升級至最新版本，以取得最新修正、效能改進和功能功能。 [深入瞭解 Azure Cosmos DB JAVA SDK。](https://aka.ms/cosmosdb/sql-api-sdk-async-java)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>從 Maven 將您的 Azure Cosmos DB Spark 連接器升級為最新版本

Azure Advisor 識別使用舊版 Azure Cosmos DB Spark 連接器的 Azure Cosmos DB 帳戶。 建議您從 Maven 升級至最新版本，以取得最新修正、效能改進和功能功能。 [深入瞭解 Azure Cosmos DB Spark 連接器。](https://aka.ms/cosmosdb/spark-connector)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>考慮移至 HDInsight 4.0 上的 Kafka 2。1

從2020年7月1日開始，您將無法在 Azure HDInsight 4.0 上使用 Kafka 1.1 來建立新的 Kafka 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在2.1 年6月 2020 30 日之前移至 Kafka on HDInsight 4.0，以避免潛在的系統/支援中斷。

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>考慮升級 HDInsight Spark 叢集中的較舊 Spark 版本

從2020年7月1日開始，您將無法使用 Spark 2.1 或 2.2 on HDInsight 3.6 來建立新的 Spark 叢集。 您將無法使用 HDInsight 4.0 上的 Spark 2.3 來建立新的 Spark 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 

## <a name="enable-virtual-machine-replication"></a>啟用虛擬機器複寫
未啟用複寫到另一個區域的虛擬機器不會因區域中斷而復原。 複寫虛擬機器可降低 Azure 區域中斷期間任何不利的業務衝擊。 Advisor 會偵測未啟用複寫的 Vm，並建議啟用複寫。 當您啟用複寫時，如果發生中斷，您可以在遠端 Azure 區域中快速啟動您的虛擬機器。 [深入瞭解虛擬機器複寫。](../site-recovery/azure-to-azure-quickstart.md)

## <a name="upgrade-to-the-latest-version-of-the-azure-connected-machine-agent"></a>升級至最新版的 Azure Connected Machine 代理程式
[Azure Connected Machine 的代理程式](https://docs.microsoft.com/azure/azure-arc/servers/manage-agent)會定期更新，並提供錯誤修正、穩定性增強功能和新功能。 我們已識別出無法在最新版電腦代理程式上執行的資源，而此 Advisor 建議建議您將代理程式升級至最新版本，以獲得最佳的 Azure Arc 體驗。

## <a name="do-not-override-hostname-to-ensure-website-integrity"></a>請勿覆寫主機名稱以確保網站完整性
建議程式在設定應用程式閘道時，建議您不要嘗試覆寫主機名稱。 除了用來存取後端的應用程式閘道前端之外，可能會導致 cookie 或重新導向 url 中斷。 請注意，這在所有情況下可能不會發生這種情況，而且某些後端 (例如 REST API 的) 通常會較不敏感。 請確定後端能夠處理此情況，或更新應用程式閘道設定，讓主機名稱不需要針對後端覆寫。 搭配 App Service 使用時，請將自訂功能變數名稱附加至 Web 應用程式，並避免對 *後端使用 azurewebsites.net 主機名稱。* [深入瞭解自訂網域](https://aka.ms/appgw-advisor-usecustomdomain)。

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>如何存取 Advisor 中的高可用性建議

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後開啟 [Advisor](https://aka.ms/azureadvisordashboard)。

2.  在 Advisor 儀表板上，選取 [ **高可用性** ] 索引標籤。

## <a name="next-steps"></a>後續步驟

如需 Advisor 建議的詳細資訊，請參閱：
* [Advisor 簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 分數](azure-advisor-score.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [Advisor 效能建議](advisor-performance-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [Advisor 操作卓越建議](advisor-operational-excellence-recommendations.md)
