---
title: 控制 Azure HDInsight 中的網路流量
description: 瞭解用來控制 Azure HDInsight 叢集之輸入和輸出流量的技術。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 031dbb0e8c9b9fb8dc37b264f9ba8e1186efc832
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783587"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>控制 Azure HDInsight 中的網路流量

Azure 虛擬網路中的網路流量可以使用下列方法進行控制：

* **網路安全性群組** (NSG) 可讓您篩選輸入和輸出網路流量。 如需詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](../virtual-network/security-overview.md)文件。

* **網路虛擬裝置**（NVA）只能與輸出流量搭配使用。 Nva 會複寫防火牆和路由器等裝置的功能。 如需詳細資訊，請參閱[網路設備](https://azure.microsoft.com/solutions/network-appliances)文件。

HDInsight 是受控服務，針對來自 VNET 的傳入和傳出流量，需要不受限制地存取 HDInsight 健全狀況和管理服務。 使用 Nsg 時，您必須確定這些服務仍然可以與 HDInsight 叢集通訊。

![在 Azure 自訂 VNET 中建立的 HDInsight 實體圖表](./media/control-network-traffic/hdinsight-vnet-diagram.png)

## <a name="hdinsight-with-network-security-groups"></a>具有網路安全性群組的 HDInsight

如果您計畫使用**網路安全性群組**來控制網路流量，請在安裝 HDInsight 之前執行下列動作：

1. 識別您要用於 HDInsight 的 Azure 區域。

2. 識別 HDInsight 針對您的區域所需的服務標記。 如需詳細資訊，請參閱[Azure HDInsight 的網路安全性群組（NSG）服務標記](hdinsight-service-tags.md)。

3. 建立或修改您打算安裝 HDInsight 之子網的網路安全性群組。

    * __網路安全性群組__：允許連接埠 __443__ 上來自 IP 位址的「輸入」____ 流量。 這可確保 HDInsight 管理服務可以從虛擬網路外部連線到叢集。

如需網路安全性群組的詳細資訊，請參閱[網路安全性群組的總覽](../virtual-network/security-overview.md)。

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>控制來自 HDInsight 叢集的輸出流量

如需控制 HDInsight 叢集輸出流量的詳細資訊，請參閱[設定 Azure HDInsight 叢集的輸出網路流量限制](hdinsight-restrict-outbound-traffic.md)。

### <a name="forced-tunneling-to-on-premises"></a>強制通道至內部部署

強制通道是使用者定義的路由設定，其中子網的所有流量都會被強制到特定的網路或位置，例如您的內部部署網路或防火牆。 因為大量的資料傳輸和潛在的效能影響，所以_不_建議將所有資料傳輸的強制通道轉換回內部部署。

有興趣設定強制通道的客戶，應使用[自訂中繼存放區](./hdinsight-use-external-metadata-stores.md)，並設定從叢集子網或內部部署網路到這些自訂中繼存放區的適當連線能力。

若要查看使用 Azure 防火牆進行 UDR 設定的範例，請參閱[設定 Azure HDInsight 叢集的輸出網路流量限制](hdinsight-restrict-outbound-traffic.md)。

## <a name="required-ip-addresses"></a> 所需的 IP 位址

如果您使用網路安全性群組或使用者定義的路由來控制流量，請參閱[HDInsight 管理 IP 位址](hdinsight-management-ip-addresses.md)。

## <a name="required-ports"></a>必要的連接埠

如果您打算使用**防火牆**並從從外部透過特定連接埠存取叢集，則可能需要在您案例所需的連接埠上允許流量。 根據預設，只要上一節所述的 Azure 管理流量允許在埠443上連線到叢集，就不需要任何特殊的埠允許清單。

如需特定服務的連接埠清單，請參閱 [HDInsight 上 Apache Hadoop 服務所使用的連接埠](hdinsight-hadoop-port-settings-for-services.md)文件。

如需虛擬設備防火牆規則的詳細資訊，請參閱[虛擬設備案例](../virtual-network/virtual-network-scenario-udr-gw-nva.md)文件。

## <a name="next-steps"></a>後續步驟

* 如需建立 Azure 虛擬網路的程式碼範例和範例，請參閱[建立 Azure HDInsight 叢集的虛擬網路](hdinsight-create-virtual-network.md)。
* 如需設定 HDInsight 連線至內部部署網路的端對端範例，請參閱[將 HDInsight 連線至內部部署網路](./connect-on-premises-network.md)。
* 如需 Azure 虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。
* 如需網路安全性群組的詳細資訊，請參閱[網路安全性群組](../virtual-network/security-overview.md)。
* 如需使用者定義路由的詳細資訊，請參閱[使用者定義的路由和 IP 轉送](../virtual-network/virtual-networks-udr-overview.md)。
* 如需虛擬網路的詳細資訊，請參閱[Plan vnet For HDInsight](./hdinsight-plan-virtual-network-deployment.md)。
