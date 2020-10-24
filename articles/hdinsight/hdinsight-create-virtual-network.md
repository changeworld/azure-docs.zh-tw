---
title: 建立 Azure HDInsight 叢集的虛擬網路
description: 瞭解如何建立 Azure 虛擬網路，以將 HDInsight 連線至其他雲端資源或您資料中心內的資源。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: 886e8cb1c5d86c1a61e65fa8cbd54c022f6c1c6b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92484809"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>建立 Azure HDInsight 叢集的虛擬網路

本文提供建立和設定 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)的範例和程式碼範例。 搭配 Azure HDInsight 叢集使用。 建立網路安全性群組 (Nsg) 和設定 DNS 的詳細範例。

如需搭配使用虛擬網路與 Azure HDInsight 的背景資訊，請參閱 [規劃 Azure HDInsight 的虛擬網路](hdinsight-plan-virtual-network-deployment.md)。

## <a name="prerequisites-for-code-samples-and-examples"></a>程式碼範例和範例的必要條件

在執行本文中的任何程式碼範例之前，請先瞭解 TCP/IP 網路功能。 如果您不熟悉 TCP/IP 網路功能，請在對生產網路進行修改之前，先洽詢他人。

本文中的範例的其他必要條件包括下列專案：

* 如果您使用的是 PowerShell，則必須安裝 [AZ 模組](https://docs.microsoft.com/powershell/azure/)。
* 如果您想要使用 Azure CLI 但尚未安裝，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。

> [!IMPORTANT]  
> 如果您要尋找使用 Azure 虛擬網路將 HDInsight 連線到內部部署網路的逐步指引，請參閱[將 HDInsight 連線至內部部署網路](connect-on-premises-network.md)文件。

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>範例：網路安全性群組與 HDInsight

本章節中的範例會示範如何建立網路安全性群組規則。 這些規則可讓 HDInsight 與 Azure 管理服務通訊。 使用範例之前，請調整 IP 位址，使其符合您所使用之 Azure 區域的 IP 位址。 您可以在 [HDInsight 管理 IP 位址](hdinsight-management-ip-addresses.md)中找到這項資訊。

### <a name="azure-resource-management-template"></a>Azure 資源管理範本

下列資源管理範本會建立限制輸入流量的虛擬網路，但允許來自 HDInsight 所需 IP 位址的流量。 此範本也會在虛擬網路中建立 HDInsight 叢集。

* [部署安全的 Azure 虛擬網路和 HDInsight Hadoop 叢集](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

使用下列 PowerShell 指令碼建立限制輸入流量的虛擬網路，並允許來自北歐區域之 IP 位址的流量。

> [!IMPORTANT]  
> 變更 `hdirule1` 和 `hdirule2` 在此範例中的 IP 位址，以符合您所使用的 Azure 區域。 您可以在 [HDInsight 管理 IP 位址](hdinsight-management-ip-addresses.md)中找到此資訊。

```azurepowershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

此範例示範如何新增規則，以允許所需 IP 位址上的輸入流量。 它不包含規則來限制來自其他來源的輸入存取。 下列程式碼示範如何從網際網路啟用 SSH 存取：

```azurepowershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

使用下列步驟建立限制輸入流量的虛擬網路，但允許來自 HDInsight 所需 IP 位址的流量。

1. 使用下列命令來建立名為 `hdisecure`的新網路安全性群組。 取代 `RESOURCEGROUP` 為包含 Azure 虛擬網路的資源群組。 取代 `LOCATION` 為在其中建立群組 (區域) 位置。

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    建立群組之後，您會收到新群組的相關資訊。

2. 使用下列將規則新增至新的網路安全性群組，這些規則允許從 Azure HDInsight 健康狀態和管理服務透過連接埠 443 的輸入通訊。 `RESOURCEGROUP`以包含 Azure 虛擬網路的資源組名取代。

    > [!IMPORTANT]  
    > 變更 `hdirule1` 和 `hdirule2` 在此範例中的 IP 位址，以符合您所使用的 Azure 區域。 您可以在 [HDInsight 管理 IP 位址](hdinsight-management-ip-addresses.md)中找到這項資訊。

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. 若要擷取此網路安全性群組的唯一識別碼，請使用下列命令：

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    此命令會傳回類似下列文字的值：

    ```output
    "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

4. 使用下列命令將網路安全性群組套用至子網路。 將 `GUID` 和值取代為 `RESOURCEGROUP` 上一個步驟所傳回的值。 `VNETNAME`將和取代 `SUBNETNAME` 為您想要建立的虛擬網路名稱和子網名稱。

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    此命令完成之後，您可以將 HDInsight 安裝至虛擬網路。

這些步驟只會開啟 Azure 雲端上 HDInsight 健康狀態和管理服務的存取權。 任何其他來自虛擬網路外部之對 HDInsight 叢集的存取都會遭到封鎖。 若要允許從外部虛擬網路存取，您必須新增額外的網路安全性群組規則。

下列程式碼示範如何從網際網路啟用 SSH 存取：

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a> 範例：DNS 設定

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>虛擬網路與已連線內部部署網路之間的名稱解析

此範例做出以下假設：

* 您的 Azure 虛擬網路會使用 VPN 閘道連線至內部部署網路。

* 虛擬網路中的自訂 DNS 伺服器會執行 Linux 或 Unix 作為作業系統。

* [Bind](https://www.isc.org/downloads/bind/) 安裝在自訂 DNS 伺服器上。

在虛擬網路的自訂 DNS 伺服器上：

1. 若要尋找虛擬網路的 DNS 尾碼，請使用 Azure PowerShell 或 Azure CLI：

    `RESOURCEGROUP`以包含虛擬網路的資源組名取代，然後輸入命令：

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. 在虛擬網路的自訂 DNS 伺服器上，使用下列文字作為 `/etc/bind/named.conf.local` 檔案的內容：

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    將 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` 值取代為虛擬網路的 DNS 尾碼。

    此設定會將虛擬網路 DNS 尾碼的所有 DNS 要求都路由傳送至 Azure 遞迴解析程式。

1. 在虛擬網路的自訂 DNS 伺服器上，使用下列文字作為 `/etc/bind/named.conf.options` 檔案的內容：

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * 將 `10.0.0.0/16` 值取代為虛擬網路的 IP 位址範圍。 此項目允許來自此範圍內位址的名稱解析要求。

    * 將內部部署網路的 IP 位址範圍新增至 `acl goodclients { ... }` 區段。  項目允許來自內部部署網路中資源的名稱解析要求。
    
    * 將 `192.168.0.1` 值取代為內部部署 DNS 伺服器的 IP 位址。 此項目會將所有其他 DNS 要求路由傳送至內部部署 DNS 伺服器。

1. 若要使用設定，請重新啟動 Bind。 例如，`sudo service bind9 restart`。

1. 將條件式轉寄站新增至內部部署 DNS 伺服器。 設定條件式轉寄站，以將步驟 1 中之 DNS 尾碼的要求傳送至自訂 DNS 伺服器。

    > [!NOTE]  
    > 如需如何新增條件式轉寄站的詳細資訊，請參閱 DNS 軟體的文件。

完成這些步驟之後，您可以使用完整網域名稱 (FQDN) 連線至任一虛擬網路中的資源。 您現在可以將 HDInsight 安裝至虛擬網路。

### <a name="name-resolution-between-two-connected-virtual-networks"></a>兩個已連線虛擬網路之間的名稱解析

此範例做出以下假設：

* 您的兩個 Azure 虛擬網路是使用 VPN 閘道或對等進行連線。

* 兩個網路中的自訂 DNS 伺服器會執行 Linux 或 Unix 作為作業系統。

* [Bind](https://www.isc.org/downloads/bind/) 安裝在自訂 DNS 伺服器上。

1. 若要尋找這兩個虛擬網路的 DNS 尾碼，請使用 Azure PowerShell 或 Azure CLI：

    `RESOURCEGROUP`以包含虛擬網路的資源組名取代，然後輸入命令：

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. 使用下列文字作為自訂 DNS 伺服器上 `/etc/bind/named.config.local` 檔案的內容。 在這兩個虛擬網路的自訂 DNS 伺服器上進行這項變更。

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    將 `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` 值取代為「其他」____ 虛擬網路的 DNS 尾碼。 此項目會將遠端網路 DNS 尾碼的要求路由傳送至該網路中的自訂 DNS。

3. 在這兩個虛擬網路的自訂 DNS 伺服器上，使用下列文字作為 `/etc/bind/named.conf.options` 檔案的內容：

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   將 `10.0.0.0/16` 和 `10.1.0.0/16` 值取代為虛擬網路的 IP 位址範圍。 此項目允許每個網路中的資源對 DNS 伺服器提出要求。

    針對虛擬網路的 DNS 尾碼 (的任何要求（例如，microsoft.com) ）都是由 Azure 遞迴解析程式處理。

4. 若要使用設定，請重新啟動 Bind。 例如，兩部 DNS 伺服器上的 `sudo service bind9 restart`。

完成這些步驟之後，您可以使用完整網域名稱 (FQDN) 連線至虛擬網路中的資源。 您現在可以將 HDInsight 安裝至虛擬網路。

## <a name="next-steps"></a>後續步驟

* 如需將 HDInsight 設定為連線至內部部署網路的完整範例，請參閱 [將 hdinsight 連線到內部部署網路](./connect-on-premises-network.md)。
* 若要在 Azure 虛擬網路中設定 Apache HBase 叢集，請參閱 [在 Azure 虛擬網路的 HDInsight 上建立 Apache hbase](hbase/apache-hbase-provision-vnet.md)叢集。
* 如需設定 Apache HBase 異地複寫，請參閱[設定 Azure 虛擬網路中的 Apache HBase 叢集複寫](hbase/apache-hbase-replication.md)。
* 如需 Azure 虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

* 如需網路安全性群組的詳細資訊，請參閱[網路安全性群組](../virtual-network/security-overview.md)。

* 如需使用者定義路由的詳細資訊，請參閱[使用者定義路由和 IP 轉送](../virtual-network/virtual-networks-udr-overview.md)。
