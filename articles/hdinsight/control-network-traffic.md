---
title: 控制 Azure HDInsight 中的網路流量
description: 瞭解用來控制 Azure HDInsight 叢集輸入和輸出流量的技術。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 54a55789cf867c97cf2384b48f1e5545ee54dafc
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773401"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>控制 Azure HDInsight 中的網路流量

Azure 虛擬網路中的網路流量可以使用下列方法進行控制：

* **網路安全性群組** (NSG) 可讓您篩選輸入和輸出網路流量。 如需詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](../virtual-network/security-overview.md)文件。

* **網路虛擬裝置** (NVA) 只能與輸出流量搭配使用。 NVA 會複寫裝置的功能，例如防火牆、路由器。 如需詳細資訊，請參閱[網路設備](https://azure.microsoft.com/solutions/network-appliances)文件。

HDInsight 為受控服務，需要不受限制地存取 HDInsight 健康情況與管理服務，以了解 VNET 的傳入和傳出流量。 使用 NSG 時，您必須確定這些服務仍然可以與 HDInsight 叢集進行通訊。

![圖表：在 Azure 自訂 VNET 中建立的 HDInsight 實體](./media/control-network-traffic/hdinsight-vnet-diagram.png)

## <a name="hdinsight-with-network-security-groups"></a>HDInsight 與網路安全性群組

如果您規劃使用**網路安全性群組**來控制網路流量，請先執行下列動作，再安裝 HDInsight：

1. 識別您要用於 HDInsight 的 Azure 區域。

2. 識別您的區域中 HDInsight 所需的服務標記。 如需詳細資訊，請參閱 [Azure HDInsight 的網路安全性群組 (NSG) 服務標記](hdinsight-service-tags.md)。

3. 建立或修改您要安裝 HDInsight 之子網路的網路安全性群組。

    * __網路安全性群組__：允許連接埠 __443__ 上來自 IP 位址的「輸入」流量。 這可確保 HDInsight 管理服務可從虛擬網路外部觸達叢集。 在已啟用 __Kafka REST Proxy__ 的叢集上，也允許連接埠 __9400__ 上的__輸入__流量。 這可確保可觸達 Kafka REST Proxy 伺服器。

如需有關網路安全性群組的詳細資訊，請參閱[網路安全性群組概觀](../virtual-network/security-overview.md)。

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>控制來自 HDInsight 叢集的輸出流量

如需控制來自 HDInsight 叢集輸出流量的詳細資訊，請參閱[設定 Azure HDInsight 叢集的輸出網路流量限制](hdinsight-restrict-outbound-traffic.md)。

### <a name="forced-tunneling-to-on-premises"></a>流向內部部署的強制通道

強制通道是一種使用者定義的路由設定，讓來自子網路的所有流量都會強制流向特定網路或位置，例如內部部署網路或防火牆。 由於大量的資料傳輸和潛在的效能影響，「不」建議使用強制通道將所有資料傳輸回內部部署。

有興趣設定強制通道的客戶應該使用[自訂中繼存放區](./hdinsight-use-external-metadata-stores.md)，並設定從叢集子網或內部部署網路到這些自訂中繼存放區的適當連線。

若要查看使用 Azure 防火牆進行 UDR 設定的範例，請參閱[設定 Azure HDInsight 叢集的輸出網路流量限制](hdinsight-restrict-outbound-traffic.md)。

## <a name="required-ip-addresses"></a>所需的 IP 位址

如果您使用網路安全性群組或使用者定義的路由來控制流量，請參閱 [HDInsight 管理 IP 位址](hdinsight-management-ip-addresses.md)。

## <a name="required-ports"></a>所需連接埠

如果您打算使用**防火牆**並從從外部透過特定連接埠存取叢集，則可能需要在您案例所需的連接埠上允許流量。 根據預設，只要允許上一節所述的 Azure 管理流量透過連接埠 443 到達叢集，就不需要設定任何特殊的連接埠允許清單。

如需特定服務的連接埠清單，請參閱 [HDInsight 上 Apache Hadoop 服務所使用的連接埠](hdinsight-hadoop-port-settings-for-services.md)文件。

如需虛擬設備防火牆規則的詳細資訊，請參閱[虛擬設備案例](../virtual-network/virtual-network-scenario-udr-gw-nva.md)文件。

## <a name="next-steps"></a>後續步驟

* 如需建立 Azure 虛擬網路的程式碼範例和範例，請參閱[建立 Azure HDInsight 叢集的虛擬網路](hdinsight-create-virtual-network.md)。
* 如需設定 HDInsight 連線至內部部署網路的端對端範例，請參閱[將 HDInsight 連線至內部部署網路](./connect-on-premises-network.md)。
* 如需 Azure 虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。
* 如需網路安全性群組的詳細資訊，請參閱[網路安全性群組](../virtual-network/security-overview.md)。
* 如需使用者定義路由的詳細資訊，請參閱[使用者定義路由和 IP 轉送](../virtual-network/virtual-networks-udr-overview.md)。
* 如需虛擬網路的詳細資訊，請參閱[規劃適用於 HDInsight 的 VNET](./hdinsight-plan-virtual-network-deployment.md)。
