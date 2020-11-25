---
title: 將 Azure HDInsight 連接到您的內部部署網路
description: 了解如何在 Azure 虛擬網路中建立 HDInsight 叢集，然後將它連線到您的內部部署網路。 了解如何使用自訂的 DNS 伺服器來設定 HDInsight 與內部部署網路之間的解析名稱。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 71ef902e909e552ade5174196f291630bc242ca0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005362"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>將 HDInsight 連線至內部部署網路

了解如何使用 Azure 虛擬網路和 VPN 閘道，將 HDInsight 連線到內部部署網路。 本文件提供下列規劃資訊：

* 在連線至內部部署網路的 Azure 虛擬網路中使用 HDInsight。
* 設定虛擬網路與內部部署網路之間的 DNS 名稱解析。
* 設定網路安全性群組來限制網際網路存取 HDInsight。
* HDInsight 在虛擬網路上提供的連接埠。

## <a name="overview"></a>概觀

若要允許聯結網路中的 HDInsight 和資源依名稱進行通訊，您必須執行下列動作：

1. 建立 Azure 虛擬網路。
1. 在 Azure 虛擬網路中建立自訂的 DNS 伺服器。
1. 將虛擬網路設定為使用自訂的 DNS 伺服器，而不使用預設的 Azure 遞迴解析程式。
1. 設定自訂的 DNS 伺服器與您的內部部署 DNS 伺服器之間的轉送。

這些設定會啟用下列行為：

* 對於完整網域名稱包含 __虛擬網路__ 之 DNS 尾碼的要求會轉寄到自訂的 DNS 伺服器。 然後，自訂的 DNS 伺服器會將這些要求轉寄到 Azure 遞迴解析程式，以傳回 IP 位址。
* 所有其他要求都會轉寄到內部部署 DNS 伺服器。 即使是公用網際網路資源 (例如 microsoft.com) 的要求也會轉寄到內部部署 DNS 伺服器進行名稱解析。

在下列圖表中，綠線表示的資源要求會以虛擬網路的 DNS 尾碼結尾。 藍線表示在內部部署網路或公用網際網路上的資源要求。

![如何在設定中解析 DNS 要求的圖表](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Prerequisites

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](./hdinsight-hadoop-linux-use-ssh-unix.md)。
* 如果使用 PowerShell，您將需要 [AZ 模組](/powershell/azure/)。
* 如果您想要使用 Azure CLI，但尚未安裝，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-virtual-network-configuration"></a>建立虛擬網路設定

使用下列文件可了解如何建立已連線到內部部署網路的 Azure 虛擬網路：

* [使用 Azure 入口網站](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [使用 Azure PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [使用 Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>建立自訂 DNS 伺服器

> [!IMPORTANT]  
> 您必須先建立和設定 DNS 伺服器，然後再將 HDInsight 安裝到虛擬網路中。

下列步驟會使用 [Azure 入口網站](https://portal.azure.com)來建立 Azure 虛擬機器。 如需其他建立虛擬機器的方式，請參閱[建立 VM - Azure CLI](../virtual-machines/linux/quick-create-cli.md) 和[建立 VM - Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)。  若要建立使用[繫結](https://www.isc.org/downloads/bind/) DNS 軟體的 Linux VM，請使用下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
  
1. 在頂端功能表中，選取 [+ 建立資源]  。

    ![建立 Ubuntu 虛擬機器](./media/connect-on-premises-network/azure-portal-create-resource.png)

1. 選取 [**計算**  >  **虛擬機器**] 以移至 [**建立虛擬機器**] 頁面。

1. 在 [基本] 索引標籤中，輸入下列資訊：  
  
    | 欄位 | 值 |
    | --- | --- |
    |訂用帳戶 |選取適當的訂用帳戶。|
    |資源群組 |選取包含先前建立之虛擬網路的資源群組。|
    |虛擬機器名稱 | 輸入此虛擬機器的易記名稱。 此範例使用 **DNSProxy**。|
    |區域 | 選取與先前建立之虛擬網路相同的區域。  並非所有 VM 大小在所有區域都可供使用。  |
    |可用性選項 |  選取所需的可用性層級。  Azure 提供各種選項以管理應用程式的可用性和復原。  建立解決方案的架構，以在「可用性區域」或「可用性設定組」中使用複寫的虛擬機器來保護您的應用程式和資料避免發生資料中心中斷，並維護事件。 此範例使用 [不需要基礎結構備援]。 |
    |映像 | 離開 **Ubuntu Server 18.04 LTS**。 |
    |驗證類型 | __密碼__ 或 __SSH 公開金鑰__： ssh 帳戶的驗證方法。 我們建議使用公開金鑰，因為它們更為安全。 此範例會使用 **密碼**。  如需詳細資訊，請參閱[建立及使用適用於 Linux VM 的 SSH 金鑰](../virtual-machines/linux/mac-create-ssh-keys.md)文件。|
    |使用者名稱 |輸入虛擬機器的系統管理員使用者名稱。  此範例使用 **sshuser**。|
    |密碼或 SSH 公開金鑰 | 可用的欄位取決於您選擇的 **驗證類型** 而定。  輸入適當的值。|
    |公用輸入連接埠|選取 [允許選取的連接埠]。 然後從 [**選取輸入埠**] 下拉式清單中選取 **SSH (22)** 。|

    ![虛擬機器基本設定](./media/connect-on-premises-network/virtual-machine-basics.png)

    將其他項目保留在預設值，然後選取 [網路] 索引標籤。

4. 在 [網路] 索引標籤中，輸入下列資訊：

    | 欄位 | 值 |
    | --- | --- |
    |虛擬網路 | 選取您先前建立的虛擬網路。|
    |子網路 | 為您稍早建立的虛擬網路選取預設子網路。 請 __勿__ 選取 VPN 閘道使用的子網路。|
    |公用 IP | 使用自動填入的值。  |

    ![HDInsight 虛擬網路設定](./media/connect-on-premises-network/virtual-network-settings.png)

    將其他項目保留在預設值，然後選取 [檢閱 + 建立]。

5. 在 [檢閱 + 建立] 索引標籤中，選取 [建立] 以建立虛擬機器。

### <a name="review-ip-addresses"></a>檢閱 IP 位址

一旦建立虛擬機器之後，您將會收到 **部署成功** 的通知，其中包含 [ **移至資源** ] 按鈕。  選取 [移至資源] ，以移至新的虛擬機器。  在新虛擬機器的預設檢視中，遵循下列步驟來找出相關聯的 IP 位址：

1. 從 [設定] 中，選取 [屬性]。

2. 請記下 **PUBLIC IP ADDRESS/DNS NAME LABEL** 和 **PRIVATE IP ADDRESS** 的值，以供稍後使用。

   ![公用和私人 IP 位址](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>安裝並設定 Bind (DNS 軟體)

1. 使用 SSH 連線至虛擬機器的 __公用 IP 位址__。 將取代 `sshuser` 為您在建立 VM 時所指定的 SSH 使用者帳戶。 下列範例會連線到 40.68.254.142 的虛擬機器：

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. 若要安裝 Bind，使用下列 SSH 工作階段中的命令：

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. 若要設定系結以將名稱解析要求轉寄到內部部署 DNS 伺服器，請使用下列文字做為檔案的內容 `/etc/bind/named.conf.options` ：

    ```DNS Zone file
    acl goodclients {
        10.0.0.0/16; # Replace with the IP address range of the virtual network
        10.1.0.0/16; # Replace with the IP address range of the on-premises network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            192.168.0.1; # Replace with the IP address of the on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

    > [!IMPORTANT]  
    > 將 `goodclients` 區段中的值取代為虛擬網路與內部部署網路的 IP 位址範圍。 本章節會定義此 DNS 伺服器接受要求的來源位址。
    >
    > 將 `forwarders` 區段中的 `192.168.0.1` 項目取代為內部部署 DNS 伺服器的 IP 位址。 此項目會將 DNS 要求路由到您的內部部署 DNS 伺服器以進行解析。

    若要編輯這個檔案，請使用下列命令：

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    若要儲存檔案，請使用 __Ctrl+X__、__Y__ 和 __Enter__ 鍵。

4. 在 SSH 工作階段中，使用下列命令：

    ```bash
    hostname -f
    ```

    此命令會傳回類似下列文字的值：

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` 文字是此虛擬網路的 __DNS 尾碼__。 儲存此值，因為稍後會用到。

5. 若要將 Bind 設定為解析虛擬網路內資源的 DNS 名稱，請使用下列文字作為 `/etc/bind/named.conf.local` 檔案的內容：

    ```DNS Zone file
    // Replace the following with the DNS suffix for your virtual network
    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > 您必須將 `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` 取代為您先前擷取的 DNS 尾碼。

    若要編輯這個檔案，請使用下列命令：

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    若要儲存檔案，請使用 __Ctrl+X__、__Y__ 和 __Enter__ 鍵。

6. 若要啟動 Bind，請使用下列命令：

    ```bash
    sudo service bind9 restart
    ```

7. 若要確認該 Bind 可以解析內部部署網路中的資源名稱，請使用下列命令：

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > 將 `dns.mynetwork.net` 取代為內部部署網路中的資源完整網域名稱 (FQDN)。
    >
    > 將 `10.0.0.4` 取代為虛擬網路中自訂 DNS 伺服器的 __內部 IP 位址__。

    回應看起來類似下列文字：

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>將虛擬網路設定為使用自訂 DNS 伺服器

若要將虛擬網路設定為使用自訂的 DNS 伺服器，而不使用 Azure 遞迴解析程式，請從 [Azure 入口網站](https://portal.azure.com)使用下列步驟：

1. 從左側功能表中，流覽至 [**所有服務**  >  **網路**  >  **虛擬網路**]。

2. 從清單中選取虛擬網路，將會為您的虛擬網路開啟預設檢視。  

3. 從預設檢視中，在 [設定] 下方，選取 [DNS 伺服器]。  

4. 選取 [自訂]，並輸入自訂 DNS 伺服器的 **私人 IP 位址**。

5. 選取 [儲存]。  <br />  

    ![設定網路的自訂 DNS 伺服器](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>設定內部部署 DNS 伺服器

在上一節中，您已將自訂 DNS 伺服器設定為將要求轉寄到內部部署 DNS 伺服器。 接下來，您必須將內部部署 DNS 伺服器設定為將要求轉寄到自訂 DNS 伺服器。

如需有關如何設定您 DNS 伺服器的特定步驟，請參閱您 DNS 伺服器軟體的文件。 尋找如何設定 __條件式轉寄站__ 的步驟。

條件式轉寄只會轉寄特定 DNS 尾碼的要求。 在此情況下，您必須設定虛擬網路 DNS 尾碼的轉寄站。 應將這個尾碼的要求轉寄到自訂 DNS 伺服器的 IP 位址。 

下列文字是 **Bind** DNS 軟體條件式轉寄站設定的範例：

```DNS Zone file
zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
    type forward;
    forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
};
```

如需有關在 **Windows Server 2016** 上使用 DNS 的資訊，請參閱 [Add-DnsServerConditionalForwarderZone](/powershell/module/dnsserver/add-dnsserverconditionalforwarderzone) 文件...

設定內部部署 DNS 伺服器之後，您就可以 `nslookup` 從內部部署網路使用，以確認您可以在虛擬網路中解析名稱。 下列範例 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

這個範例會使用 196.168.0.4 的內部部署 DNS 伺服器來解析自訂 DNS 伺服器的名稱。 將 IP 位址取代為內部部署 DNS 伺服器的 IP 位址。 將 `dnsproxy` 位址取代為自訂 DNS 伺服器的完整網域名稱。

## <a name="optional-control-network-traffic"></a>選擇性：控制網路流量

您可以使用網路安全性群組 (NSG) 或使用者定義路由 (UDR) 來控制網路流量。 NSG 可讓您篩選輸入和輸出流量，並允許或拒絕流量。 UDR 可讓您控制虛擬網路、網際網路及內部部署網路的資源之間流量流動的方式。

> [!WARNING]  
> HDInsight 需要 Azure 雲端中來自特定 IP 位址的輸入存取，以及不受限制的輸出存取。 使用 NSG 或 UDR 來控制流量時，您必須執行下列步驟：

1. 尋找包含您虛擬網路之位置的 IP 位址。 如需依位置的必要 IP 清單，請參閱[必要的 IP 位址](./hdinsight-management-ip-addresses.md)。

2. 針對步驟 1 中所識別的 IP 位址，允許來自那些 IP 位址的輸入流量。

   * 如果您使用的是 __NSG__：允許埠 __443__ 上的 __輸入__ 流量作為 IP 位址。
   * 如果您是使用 __UDR__：將 IP 位址的路由的 __下一個躍點__ 類型設定為 __網際網路__ 。

如需使用 Azure PowerShell 或 Azure CLI 建立 NSG 的範例，請參閱[使用 Azure 虛擬網路擴充 HDInsight](hdinsight-create-virtual-network.md#hdinsight-nsg) 文件。

## <a name="create-the-hdinsight-cluster"></a>建立 HDInsight 叢集

> [!WARNING]  
> 您必須先設定自訂 DNS 伺服器，然後再將 HDInsight 安裝到虛擬網路中。

使用[使用 Azure 入口網站建立 HDInsight 叢集](./hdinsight-hadoop-create-linux-clusters-portal.md)文件中的步驟來建立 HDInsight 叢集。

> [!WARNING]  
> * 叢集建立期間，您必須選擇包含您虛擬網路的位置。
> * 在設定的 __進階設定__ 部分，您必須選取稍早建立的虛擬網路與子網路。

## <a name="connecting-to-hdinsight"></a>連線至 HDInsight

HDInsight 上大部分的文件都假設您透過網際網路擁有叢集存取權。 例如，您可以連線至位於 `https://CLUSTERNAME.azurehdinsight.net` 的叢集。 此位址會使用公用網關，如果您已使用 Nsg 或 Udr 來限制來自網際網路的存取，則無法使用。

某些文件在從 SSH 工作階段連線到叢集時也會參考 `headnodehost`。 此位址僅適用于叢集中的節點，而且無法在透過虛擬網路連線的用戶端上使用。

若要透過虛擬網路直接連線至 HDInsight，請使用下列步驟：

1. 若要探索 HDInsight 叢集節點的內部完整網域名稱，請使用下列方法之一：

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. 若要判斷可提供服務的連接埠，請參閱 [Apache Hadoop 服務在 HDInsight 上所使用的連接埠](./hdinsight-hadoop-port-settings-for-services.md)文件。

    > [!IMPORTANT]  
    > 前端節點上裝載的某些服務一次只會在一個節點上作用。 如果您嘗試在一個前端節點上存取服務但卻失敗，請切換至其他的前端節點。
    >
    > 例如，Apache Ambari 一次只在一個前端節點上作用。 如果您嘗試在一個前端節點上存取 Ambari 而傳回 404 錯誤，表示它正在其他前端節點上執行中。

## <a name="next-steps"></a>後續步驟

* 如需在虛擬網路中使用 HDInsight 的詳細資訊，請參閱 [規劃 Azure HDInsight 叢集的虛擬網路部署](./hdinsight-plan-virtual-network-deployment.md)。

* 如需 Azure 虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

* 如需網路安全性群組的詳細資訊，請參閱[網路安全性群組](../virtual-network/network-security-groups-overview.md)。

* 如需使用者定義路由的詳細資訊，請參閱[使用者定義路由和 IP 轉送](../virtual-network/virtual-networks-udr-overview.md)。