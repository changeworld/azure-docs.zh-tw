---
title: '使用 Private Link (preview 保護和隔離 Azure HDInsight 叢集) '
description: 瞭解如何使用 Azure Private Link 隔離虛擬網路中的 Azure HDInsight 叢集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 219f8728eb90101e7437cbb82b44a1f7124ef14d
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556421"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>使用 Private Link (preview 保護和隔離 Azure HDInsight 叢集) 

在 Azure HDInsight 的 [預設虛擬網路架構](./hdinsight-virtual-network-architecture.md)中，HDInsight 資源提供者 (RP) 使用公用 IP 位址與叢集通訊。 某些案例需要完整的網路隔離，而不使用公用 IP 位址。 在本文中，您將瞭解可用來建立私用 HDInsight 叢集的 advanced 控制項。 如需有關如何在未完成網路隔離的情況下，限制進出您叢集的流量，請參閱 [在 Azure HDInsight 中控制網路流量](./control-network-traffic.md)。

您可以在 Azure Resource Manager (ARM) 範本中設定特定的網路屬性，以建立私人 HDInsight 叢集。 您可以使用兩個屬性來建立私人 HDInsight 叢集：

* 藉由設定為輸出來移除公用 IP 位址 `resourceProviderConnection` 。
* 啟用 Azure Private Link，並將設定為啟用，以使用 [私人端點](../private-link/private-endpoint-overview.md) `privateLink` 。

## <a name="remove-public-ip-addresses"></a>移除公用 IP 位址

根據預設，HDInsight RP 會使用公用 Ip 對叢集的 *輸入* 連線。 當 `resourceProviderConnection` network 屬性設定為 *輸出* 時，它會反轉與 HDInsight RP 的連線，如此一來，一律會從叢集內部起始連接至 RP。 若沒有輸入連線，就不需要輸入服務標記或公用 IP 位址。

預設虛擬網路架構中使用的基本負載平衡器會自動提供公用 NAT (的網路位址轉譯) 來存取必要的輸出相依性，例如 HDInsight RP。 如果您想要限制對公用網際網路的輸出連線能力，您可以 [設定防火牆](./hdinsight-restrict-outbound-traffic.md)，但這不是必要條件。

設定 `resourceProviderConnection` 為輸出也可讓您使用私人端點來存取叢集特定資源，例如 Azure Data Lake Storage Gen2 或外部中繼存放區。 使用這些資源的私人端點並非必要，但如果您打算讓這些資源擁有私用端點，則必須設定您建立 HDInsight 叢集的私人端點和 DNS 專案 `before` 。 我們建議您在叢集建立期間，建立並提供所需的所有外部 SQL 資料庫，例如 Apache Ranger、Ambari、Oozie 和 Hive 中繼存放區。 需求是必須能夠從叢集子網內部存取這些資源，不論是透過自己的私人端點或其他資源。

不支援針對 Azure Key Vault 使用私用端點。 如果您要使用 Azure Key Vault 進行待用加密，則必須可從沒有私人端點的 HDInsight 子網記憶體取 Azure Key Vault 端點。

下圖顯示當設定為輸出時，可能的 HDInsight 虛擬網路架構可能如下 `resourceProviderConnection` ：

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="使用輸出資源提供者連接的 HDInsight 架構圖表":::

建立叢集之後，您應該設定適當的 DNS 解析。 以下是在 Azure 管理的公用 DNS 區域上建立的標準名稱 DNS 記錄 (CNAME) ： `azurehdinsight.net` 。

```dns
<clustername>    CNAME    <clustername>-int
```

若要使用叢集 Fqdn 來存取叢集，您可以直接使用內部負載平衡器私人 Ip，或使用您自己的私人 DNS 區域，視您的需求覆寫叢集端點。 例如，您可以有私人 DNS 區域 `azurehdinsight.net` 。 並視需要新增您的私人 Ip：

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>啟用 Private Link

Private Link （預設為停用）需要廣泛的網路知識，才能在建立叢集之前，正確地設定使用者定義的路由 (UDR) 和防火牆規則。 使用這項設定是選擇性的，但只有在 [ `resourceProviderConnection` 網路] 屬性設定為 [ *輸出* ] （如上一節所述）的情況下才可使用。

當 `privateLink` 設定為 [ *啟用*] 時，系統就會建立 (SLB) 的內部 [標準負載平衡](../load-balancer/load-balancer-overview.md) 器，並為每個 SLB 布建 Azure Private Link 服務。 Private Link 服務可讓您從私人端點存取 HDInsight 叢集。

標準負載平衡器不會自動提供 [公用輸出 NAT](../load-balancer/load-balancer-outbound-connections.md) ，例如基本負載平衡器。 您必須提供自己的 NAT 解決方案，例如 [虛擬網路 NAT](../virtual-network/nat-overview.md) 或 [防火牆](./hdinsight-restrict-outbound-traffic.md)，以提供輸出相依性。 您的 HDInsight 叢集仍然需要存取其輸出相依性。 如果不允許這些輸出相依性，叢集建立可能會失敗。

### <a name="prepare-your-environment"></a>準備您的環境

若要成功建立私人連結服務，您必須明確 [停用私人連結服務的網路原則](../private-link/disable-private-link-service-network-policy.md)。

下圖顯示建立叢集之前所需的網路設定範例。 在此範例中，會使用 UDR 將所有輸出流量 [強制](../firewall/forced-tunneling.md) 傳送至 Azure 防火牆，並在建立叢集之前，在防火牆上應「允許」所需的輸出相依性。 針對企業安全性套件叢集，可透過 VNet 對等互連來提供 Azure Active Directory Domain Services 的網路連線能力。

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="在叢集建立之前的私人連結環境圖":::

設定網路之後，您可以建立已啟用輸出資源提供者連線和私人連結的叢集，如下圖所示。 在此設定中，不會針對每個標準負載平衡器布建公用 Ip 和 Private Link 服務。

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="叢集建立後的私人連結環境圖表":::

### <a name="access-a-private-cluster"></a>存取私人叢集

若要存取私人叢集，您可以直接使用內部負載平衡器私人 Ip，也可以使用 Private Link DNS 擴充功能和私人端點。 當設定 `privateLink` 為 [已啟用] 時，您可以建立自己的私人端點，並透過私人 DNS 區域設定 DNS 解析。

在 Azure 管理的公用 DNS 區域中建立的 Private Link 專案如下所示 `azurehdinsight.net` ：

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

下圖顯示從虛擬網路存取叢集所需的私人 DNS 專案範例，該虛擬網路未對等互連，或沒有叢集負載平衡器的直接連線。 您可以使用 Azure 私用區域來覆寫 `*.privatelink.azurehdinsight.net` fqdn，並解析成您自己的私人端點 IP 位址。

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="私用連結架構的圖表":::

## <a name="how-to-create-clusters"></a>如何建立叢集？
### <a name="use-arm-template-properties"></a>使用 ARM 範本屬性

下列 JSON 程式碼片段包含您需要在 ARM 範本中設定以建立私人 HDInsight 叢集的兩個網路屬性。

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

如需包含許多 HDInsight 企業安全性功能（包括 Private Link）的完整範本，請參閱 [HDInsight 企業安全性範本](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template)。

### <a name="use-azure-powershell"></a>使用 Azure PowerShell

若要使用 PowerShell，請參閱 [這裡](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)的範例。

### <a name="use-azure-cli"></a>使用 Azure CLI
若要使用 Azure CLI，請參閱 [這裡](/cli/azure/hdinsight#az_hdinsight_create-examples)的範例。

## <a name="next-steps"></a>後續步驟

* [Azure HDInsight 的企業安全性套件](enterprise-security-package.md)
* [Azure HDInsight 中的企業安全性一般資訊與指導方針](./domain-joined/general-guidelines.md)
