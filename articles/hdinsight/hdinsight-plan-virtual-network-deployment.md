---
title: 為 Azure HDInsight 規劃虛擬網路
description: 瞭解如何規劃 Azure 虛擬網路部署，將 HDInsight 連接到其他雲資源或資料中心中的資源。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: 30664d533215cb49fa6f436ec4cf88fa319c3300
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272262"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>為 Azure HDInsight 規劃虛擬網路

本文提供有關將[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)（VNet） 與 Azure HDInsight 一起使用的背景資訊。 它還討論了在為 HDInsight 群集實現虛擬網路之前必須做出的設計和實現決策。 規劃階段完成後，可以繼續[為 Azure HDInsight 群集創建虛擬網路](hdinsight-create-virtual-network.md)。 有關正確配置網路安全性群組 （NSG） 和使用者定義的路由所需的 HDInsight 管理 IP 位址的詳細資訊，請參閱[HDInsight 管理 IP 位址](hdinsight-management-ip-addresses.md)。

使用 Azure 虛擬網路可啟用下列案例：

* 從內部部署網路直接連線至 HDInsight。
* 將 HDInsight 連線至 Azure 虛擬網路中的資料存放區。
* 直接存取 Apache Hadoop 服務，這些服務在互聯網上無法公開提供。 例如，Apache Kafka API 或 Apache HBase Java API。

> [!IMPORTANT]
> 在 VNET 中創建 HDInsight 群集將創建多個網路資源，如 NIC 和負載等化器。 **不要**刪除這些網路資源，因為群集需要這些資源才能與 VNET 正確運行。
>
> 2019 年 2 月 28 日之後，在 VNET 中創建的新 HDInsight 群集的網路資源（如 NIC、LOB 等）將預配到同一 HDInsight 群集資源組中。 以前，這些資源是在 VNET 資源組中預配的。 當前正在運行的群集和無 VNET 創建的群集沒有更改。

## <a name="planning"></a>規劃

規劃在虛擬網路中安裝 HDInsight 時，您必須回答的問題如下：

* 您需要將 HDInsight 安裝到現有虛擬網路嗎？ 或者，您要建立新的網路嗎？

    如果您使用的是現有虛擬網路，則可能需要修改網路設定，然後才能安裝 HDInsight。 如需詳細資訊，請參閱[將 HDInsight 新增至現有虛擬網路](#existingvnet)一節。

* 您要將包含 HDInsight 的虛擬網路連線至另一個虛擬網路或內部部署網路嗎？

    若要輕鬆地跨網路使用資源，您可能需要建立自訂 DNS，並設定 DNS 轉送。 如需詳細資訊，請參閱[連線多個網路](#multinet)一節。

* 您要將輸入或輸出流量限制/重新導向至 HDInsight 嗎？

    HDInsight 必須具有 Azure 資料中心內特定 IP 位址的無限制通訊。 另外還有數個連接埠必須允許通過防火牆才能進行用戶端通訊。 如需詳細資訊，請參閱[控制網路流量](#networktraffic)一節。

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>將 HDInsight 新增至現有虛擬網路

使用本節中的步驟，以了解如何將新的 HDInsight 新增至現有 Azure 虛擬網路。

> [!NOTE]  
> 您無法在虛擬網路中新增現有的 HDInsight 叢集。

1. 您要使用虛擬網路的傳統或 Resource Manager 部署模型嗎？

    HDInsight 3.4 和更新版本需要 Resource Manager 虛擬網路。 舊版 HDInsight 需要傳統虛擬網路。

    如果您的現有網路是傳統虛擬網路，則必須建立 Resource Manager 虛擬網路，然後連線兩者。 [將傳統 VNet 連線至新的 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

    加入之後，Resource Manager 網路中所安裝的 HDInsight 就可以與傳統網路中的資源互動。

2. 您使用網路安全性群組、使用者定義路由或虛擬網路設備來限制傳入或傳出虛擬網路的流量嗎？

    HDInsight 是一個受控服務，需要 Azure 資料中心內數個 IP 位址的無限制存取權。 若要允許與這些 IP 位址的通訊，請更新任何現有網路安全性群組或使用者定義路由。

    HDInsight 會裝載多個使用各種連接埠的服務。 不要阻止到這些埠的流量。 如需允許通過虛擬設備防火牆的連接埠清單，請參閱「安全性」一節。

    若要尋找現有安全性設定，請使用下列 Azure PowerShell 或 Azure CLI 命令：

    * 網路安全性群組

        替換為`RESOURCEGROUP`包含虛擬網路的資源組的名稱，然後輸入命令：

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        如需詳細資訊，請參閱[為網路安全性群組疑難排解](../virtual-network/diagnose-network-traffic-filter-problem.md)文件。

        > [!IMPORTANT]  
        > 會根據規則優先順序依序套用網路安全性群組規則。 會套用第一個符合流量模式的規則，而且未針對該流量套用其他規則。 排序從最寬鬆到最嚴格權限的規則。 如需詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](../virtual-network/security-overview.md)文件。

    * 使用者定義的路由

        替換為`RESOURCEGROUP`包含虛擬網路的資源組的名稱，然後輸入命令：

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        如需詳細資訊，請參閱[為路由疑難排解](../virtual-network/diagnose-network-routing-problem.md)文件。

3. 建立 HDInsight 叢集，並在設定期間選擇 Azure 虛擬網路。 使用下列文件中的步驟，以了解叢集建立程序：

    * [使用 Azure 入口網站建立 HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [使用 Azure PowerShell 建立 HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [使用 Azure 傳統 CLI 建立 HDInsight](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [使用 Azure Resource Manager 範本建立 HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > 將 HDInsight 新增至虛擬網路是選擇性的設定步驟。 請務必在設定叢集時選取虛擬網路。

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>連線多個網路

多網路設定的最大挑戰是網路之間的名稱解析。

Azure 會針對安裝於虛擬網路中的 Azure 服務提供名稱解析。 這個內建名稱解析允許 HDInsight 使用完整網域名稱 (FQDN) 連線至下列資源：

* 網際網路上的任何可用資源。 例如，microsoft.com、windowsupdate.com。

* 相同 Azure 虛擬網路中的任何資源，方法是使用資源的「內部 DNS 名稱」____。 例如，在使用預設名稱解析時，以下是分配給 HDInsight 工作節點的內部 DNS 名稱的示例：

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    這些節點可以使用內部 DNS 名稱彼此直接通訊，以及與 HDInsight 中的其他節點通訊。

預設名稱解析「不」____ 允許 HDInsight 解析加入虛擬網路之網路中的資源名稱。 例如，將本地網路加入虛擬網路很常見。 僅使用預設名稱解析，HDInsight 無法按名稱訪問本地網路中的資源。 事實正好相反，本地網路中的資源無法按名稱訪問虛擬網路中的資源。

> [!WARNING]  
> 您必須建立自訂 DNS 伺服器，並設定虛擬網路使用它，再建立 HDInsight 叢集。

若要啟用虛擬網路與所加入網路中資源之間的名稱解析，您必須執行下列動作：

1. 在要安裝 HDInsight 的 Azure 虛擬網路中建立自訂 DNS 伺服器。

2. 將虛擬網路設定為使用自訂 DNS 伺服器。

3. 尋找虛擬網路的 Azure 指派 DNS 尾碼。 此值與 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` 類似。 如需尋找 DNS 尾碼的資訊，請參閱[範例：自訂 DNS](hdinsight-create-virtual-network.md#example-dns) 一節。

4. 設定 DNS 伺服器之間的轉送。 設定取決於遠端網路類型。

   * 如果遠端網路是內部部署網路，請設定 DNS，如下所示：

     * 「自訂 DNS」____(在虛擬網路中)：

         * 將虛擬網路 DNS 尾碼的要求轉送至 Azure 遞迴解析程式 (168.63.129.16)。 Azure 會處理虛擬網路中資源的要求

         * 將所有其他要求轉送至內部部署 DNS 伺服器。 內部部署 DNS 會處理所有其他名稱解析要求，即使是網際網路資源 (例如 Microsoft.com) 的要求也是一樣。

     * 內部部署 DNS____：將虛擬網路 DNS 尾碼的要求轉送至自訂 DNS 伺服器。 自訂 DNS 伺服器接著會轉送至 Azure 遞迴解析程式。

       此設定會將完整網域名稱包含虛擬網路 DNS 尾碼的要求路由傳送至自訂 DNS 伺服器。 內部部署 DNS 伺服器會處理所有其他要求 (即使是針對公用網際網路位址)。

   * 如果遠端網路是另一個 Azure 虛擬網路，請設定 DNS，如下所示：

     * 「自訂 DNS」____(在每個虛擬網路中)：

         * 將虛擬網路 DNS 尾碼的要求轉送至自訂 DNS 伺服器。 每個虛擬網路中的 DNS 會負責解析其網路內的資源。

         * 將所有其他要求轉送至 Azure 遞迴解析程式。 遞迴解析程式負責解析本機和網際網路資源。

       根據 DNS 尾碼，每個網路的 DNS 伺服器都會將要求轉送至另一個。 其他要求是使用 Azure 遞迴解析程式進行解析。

     如需每個設定的範例，請參閱[範例：自訂 DNS](hdinsight-create-virtual-network.md#example-dns) 一節。

如需詳細資訊，請參閱 [VM 和角色執行個體的名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)文件。

## <a name="directly-connect-to-apache-hadoop-services"></a>直接連線至 Apache Hadoop 服務

您可以連線至位於 `https://CLUSTERNAME.azurehdinsight.net` 的叢集。 此位址會使用公用 IP，如果您已使用 NSG 限制來自網際網路的傳入流量，則可能無法觸達該 IP。 此外，當您在 VNet 中部署叢集時，可以使用私人端點 `https://CLUSTERNAME-int.azurehdinsight.net` 來存取該叢集。 此端點會解析至 VNet 內的私人 IP，以便存取叢集。

若要透過虛擬網路連線至 Apache Ambari 和其他網頁，請使用下列步驟：

1. 若要探索 HDInsight 叢集節點的內部完整網域名稱 (FQDN)，請使用下列其中一種方法：

    替換為`RESOURCEGROUP`包含虛擬網路的資源組的名稱，然後輸入命令：

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    在所傳回的節點清單中，尋找前端節點的 FQDN 並使用這些 FQDN 來連線至 Ambari 和其他 Web 服務。 例如，使用 `http://<headnode-fqdn>:8080` 存取 Ambari。

    > [!IMPORTANT]  
    > 前端節點上裝載的某些服務一次只會在一個節點上作用。 如果您嘗試在一個前端節點上存取服務，但傳回 404 錯誤，請切換至其他的前端節點。

2. 若要判斷可提供服務的節點和連接埠，請參閱 [Hadoop 服務在 HDInsight 上所使用的連接埠](./hdinsight-hadoop-port-settings-for-services.md)文件。

## <a name="controlling-network-traffic"></a><a id="networktraffic"></a> 控制網路流量

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>控制到 HDInsight 群集的入站和出站流量的技術

Azure 虛擬網路中的網路流量可以使用下列方法進行控制：

* **網路安全性群組** (NSG) 可讓您篩選輸入和輸出網路流量。 如需詳細資訊，請參閱[使用網路安全性群組來篩選網路流量](../virtual-network/security-overview.md)文件。

* **網路虛擬裝置**（NVA） 只能與出站流量一起使用。 NVA 複製防火牆和路由器等設備的功能。 如需詳細資訊，請參閱[網路設備](https://azure.microsoft.com/solutions/network-appliances)文件。

作為託管服務，HDInsight 要求不受限制地訪問 HDInsight 運行狀況和管理服務，以便從 VNET 傳入和傳出流量。 使用 NSG 時，必須確保這些服務仍可與 HDInsight 群集通信。

![在 Azure 自訂 VNET 中創建的 HDInsight 實體圖](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>帶網路安全性群組的 HDInsight

如果您計畫使用**網路安全性群組**來控制網路流量，則在安裝 HDInsight 之前執行以下操作：

1. 識別您要用於 HDInsight 的 Azure 區域。

2. 標識 HDInsight 為您的地區所需的服務標記。 有關詳細資訊，請參閱[Azure HDInsight 的網路安全性群組 （NSG） 服務標記](hdinsight-service-tags.md)。

3. 創建或修改計畫安裝 HDInsight 的子網的網路安全性群組。

    * __網路安全性群組__：允許連接埠 __443__ 上來自 IP 位址的「輸入」____ 流量。 這將確保 HDInsight 管理服務可以從虛擬網路外部到達群集。

有關網路安全性群組的詳細資訊，請參閱[網路安全性群組概述](../virtual-network/security-overview.md)。

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>控制來自 HDInsight 群集的出站流量

有關從 HDInsight 群集控制出站流量的詳細資訊，請參閱[為 Azure HDInsight 群集配置出站網路流量限制](hdinsight-restrict-outbound-traffic.md)。

#### <a name="forced-tunneling-to-on-premises"></a>強制隧道到本地

強制通道是一種使用者定義路由設定，其中來自子網路的所有流量都會強制流向特定網路或位置，例如內部部署網路。 HDInsight__不支援__強制隧道將流量隧道到本地網路。

## <a name="required-ip-addresses"></a><a id="hdinsight-ip"></a> 所需的 IP 位址

如果使用網路安全性群組或使用者定義的路由來控制流量，請參閱[HDInsight 管理 IP 位址](hdinsight-management-ip-addresses.md)。

## <a name="required-ports"></a><a id="hdinsight-ports"></a> 所需連接埠

如果您打算使用**防火牆**並從從外部透過特定連接埠存取叢集，則可能需要在您案例所需的連接埠上允許流量。 根據預設，只要允許上一節中所述的 Azure 管理流量透過連接埠 443 到達叢集，就不需要設定任何特殊的連接埠允許清單。

如需特定服務的連接埠清單，請參閱 [HDInsight 上 Apache Hadoop 服務所使用的連接埠](hdinsight-hadoop-port-settings-for-services.md)文件。

如需虛擬設備防火牆規則的詳細資訊，請參閱[虛擬設備案例](../virtual-network/virtual-network-scenario-udr-gw-nva.md)文件。

## <a name="load-balancing"></a>負載平衡

創建 HDInsight 群集時，也創建負載等化器。 此負載等化器的類型處於[基本 SKU 級別](../load-balancer/concepts-limitations.md#skus)，具有一定的約束。 其中一個約束是，如果在不同的區域有兩個虛擬網路，則無法連接到基本負載等化器。 有關詳細資訊，請參閱[虛擬網路常見問題解答：全域 vnet 對等互連的約束](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。

## <a name="transport-layer-security"></a>Transport Layer Security (傳輸層安全性)

通過公共群集終結點`https://<clustername>.azurehdinsight.net`與群集的連接通過群集閘道節點進行接近。 這些連接使用稱為 TLS 的協定進行保護。 在閘道上強制實施更高版本的 TLS 可提高這些連接的安全性。 有關為什麼要使用較新版本 TLS 的詳細資訊，請參閱[解決 TLS 1.0 問題](https://docs.microsoft.com/security/solving-tls1-problem)。

預設情況下，Azure HDInsight 群集接受公共 HTTPS 終結點上的 TLS 1.2 連接，以及舊版本，以便向後相容。 在群集創建期間，可以使用 Azure 門戶或資源管理器範本控制閘道節點上支援的最小 TLS 版本。 對於門戶，在群集創建期間從 **"安全 + 網路**"選項卡中選擇 TLS 版本。 對於部署時資源管理器範本，請使用 **"最小支援 TlsVersion"** 屬性。 有關示例範本，請參閱[HDInsight 最小 TLS 1.2 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)。 此屬性支援三個值："1.0"、"1.1"和"1.2"，分別對應于 TLS 1.0°、TLS 1.1+ 和 TLS 1.2+。

> [!IMPORTANT]
> 從 2020 年 6 月 30 日開始，Azure HDInsight 將對所有 HTTPS 連接強制實施 TLS 1.2 或更高版本。 我們建議您確保所有用戶端都準備好處理 TLS 1.2 或更高版本。 有關詳細資訊，請參閱[Azure HDInsight TLS 1.2 強制](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/)。

## <a name="next-steps"></a>後續步驟

* 有關創建 Azure 虛擬網路的代碼示例和示例，請參閱[為 Azure HDInsight 群集創建虛擬網路](hdinsight-create-virtual-network.md)。
* 如需設定 HDInsight 連線至內部部署網路的端對端範例，請參閱[將 HDInsight 連線至內部部署網路](./connect-on-premises-network.md)。
* 有關在 Azure 虛擬網路中配置 Apache HBase 群集，請參閱[在 Azure 虛擬網路中的 HDInsight 上創建 Apache HBase 群集](hbase/apache-hbase-provision-vnet.md)。
* 如需設定 Apache HBase 異地複寫，請參閱[設定 Azure 虛擬網路中的 Apache HBase 叢集複寫](hbase/apache-hbase-replication.md)。
* 如需 Azure 虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。
* 如需網路安全性群組的詳細資訊，請參閱[網路安全性群組](../virtual-network/security-overview.md)。
* 有關使用者定義的路由的詳細資訊，請參閱[使用者定義的路由和 IP 轉發](../virtual-network/virtual-networks-udr-overview.md)。
