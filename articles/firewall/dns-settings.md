---
title: Azure 防火牆 DNS 設定
description: 您可以使用 DNS 伺服器和 DNS proxy 設定來設定 Azure 防火牆。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: 197d48a2f5368111ec194a18f86aedf5ad78e1b2
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565614"
---
# <a name="azure-firewall-dns-settings"></a>Azure 防火牆 DNS 設定

您可以設定自訂 DNS 伺服器，並啟用 Azure 防火牆的 DNS proxy。 當您部署防火牆時，請設定這些設定，或稍後從 [ **DNS 設定** ] 頁面進行設定。

## <a name="dns-servers"></a>DNS 伺服器

DNS 伺服器會維護並將功能變數名稱解析為 IP 位址。 根據預設，Azure 防火牆會使用 Azure DNS 來解析名稱。 **Dns 伺服器** 設定可讓您設定自己的 dns 伺服器，以進行 Azure 防火牆名稱解析。 您可以設定單一伺服器或多部伺服器。

> [!NOTE]
> 針對使用 Azure 防火牆管理員管理的 Azure 防火牆實例，DNS 設定會在相關聯的 Azure 防火牆原則中設定。

### <a name="configure-custom-dns-servers---azure-portal"></a>設定自訂 DNS 伺服器-Azure 入口網站

1. 在 [Azure 防火牆 **設定** ] 下，選取 [ **DNS 設定** ]。
2. 在 [ **DNS 伺服器** ] 下，您可以輸入或新增先前在虛擬網路中指定的現有 DNS 伺服器。
3. 選取 [儲存]。

防火牆現在會將 DNS 流量導向至指定的 DNS 伺服器，以進行名稱解析。

:::image type="content" source="media/dns-settings/dns-servers.png" alt-text="顯示 D N S 伺服器設定的螢幕擷取畫面。":::

### <a name="configure-custom-dns-servers---azure-cli"></a>設定自訂 DNS 伺服器-Azure CLI

下列範例會使用 Azure CLI 來更新具有自訂 DNS 伺服器的 Azure 防火牆。

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --dns-servers 10.1.0.4 10.1.0.5
```

> [!IMPORTANT]
> 此命令 `az network firewall` 需要安裝 Azure CLI 延伸模組 `azure-firewall` 。 您可以使用命令來安裝它 `az extension add --name azure-firewall` 。 

### <a name="configure-custom-dns-servers---azure-powershell"></a>設定自訂 DNS 伺服器-Azure PowerShell

下列範例會使用 Azure PowerShell 來更新具有自訂 DNS 伺服器的 Azure 防火牆。

```azurepowershell
$dnsServers = @("10.1.0.4", "10.1.0.5")
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSServer = $dnsServers

$azFw | Set-AzFirewall
```

## <a name="dns-proxy"></a>DNS proxy

您可以設定 Azure 防火牆作為 DNS proxy。 DNS proxy 是從用戶端虛擬機器到 DNS 伺服器的 DNS 要求媒介。 如果您設定自訂 DNS 伺服器，請啟用 DNS proxy 以避免 DNS 解析不相符，並在網路規則中啟用 FQDN (完整功能變數名稱) 篩選。

如果您未啟用 DNS proxy，來自用戶端的 DNS 要求可能會在不同時間移動到 DNS 伺服器，或傳回與防火牆不同的回應。 DNS proxy 會將 Azure 防火牆放在用戶端要求的路徑中，以避免不一致。

當 Azure 防火牆是 DNS proxy 時，可能會有兩種快取功能類型：

- **正** 快取： DNS 解析成功。 防火牆會使用 TTL (存留時間) 封包或物件。 

- **負數快取** ： DNS 解析會導致沒有回應或沒有解決方法。 防火牆會在一小時內快取此資訊。

DNS proxy 會將所有已解析的 IP 位址從 Fqdn 儲存在網路規則中。 最佳做法是使用可解析為一個 IP 位址的 Fqdn。  

### <a name="dns-proxy-configuration"></a>DNS proxy 設定

DNS proxy 設定需要三個步驟：
1. 啟用 Azure 防火牆 DNS 設定中的 DNS proxy。
2. （選擇性）設定您的自訂 DNS 伺服器，或使用提供的預設值。
3. 將 Azure 防火牆私人 IP 位址設定為虛擬網路 DNS 伺服器設定中的自訂 DNS 位址。 這項設定可確保 DNS 流量會導向至 Azure 防火牆。

#### <a name="configure-dns-proxy---azure-portal"></a>設定 DNS proxy-Azure 入口網站

若要設定 DNS proxy，您必須設定虛擬網路 DNS 伺服器設定，以使用防火牆私人 IP 位址。 然後，在 Azure 防火牆 **DNS 設定** 中啟用 DNS proxy。

##### <a name="configure-virtual-network-dns-servers"></a>設定虛擬網路 DNS 伺服器 

1. 選取將透過 Azure 防火牆實例路由傳送 DNS 流量的虛擬網路。
2. 選取 [設定] 底下的 [DNS 伺服器]。
3. 選取 [DNS 伺服器] 底下的 [自訂]。
4. 輸入防火牆的私人 IP 位址。
5. 選取 [儲存]。
6. 重新開機連線到虛擬網路的 Vm，以將新的 DNS 伺服器設定指派給它們。 Vm 會繼續使用其目前的 DNS 設定，直到重新開機為止。

##### <a name="enable-dns-proxy"></a>啟用 DNS proxy

1. 選取您的 Azure 防火牆實例。
2. 在 [ **設定** ] 底下，選取 [ **DNS 設定** ]。
3. 預設會停用 **DNS Proxy** 。 啟用此設定時，防火牆會接聽埠53，並將 DNS 要求轉送至設定的 DNS 伺服器。
4. 請檢查 **DNS 伺服器** 設定，以確定設定適用于您的環境。
5. 選取 [儲存]。

:::image type="content" source="media/dns-settings/dns-proxy.png" alt-text="顯示 D N S proxy 設定的螢幕擷取畫面。":::

#### <a name="configure-dns-proxy---azure-cli"></a>設定 DNS proxy-Azure CLI

您可以使用 Azure CLI 來設定 Azure 防火牆中的 DNS proxy 設定。 您也可以使用它來更新虛擬網路，以使用 Azure 防火牆作為 DNS 伺服器。

##### <a name="configure-virtual-network-dns-servers"></a>設定虛擬網路 DNS 伺服器

下列範例會將虛擬網路設定為使用 Azure 防火牆作為 DNS 伺服器。
 
```azurecli-interactive
az network vnet update \
    --name VNetName \ 
    --resource-group VNetRG \
    --dns-servers <firewall-private-IP>
```

##### <a name="enable-dns-proxy"></a>啟用 DNS proxy

下列範例會啟用 Azure 防火牆中的 DNS proxy 功能。

```azurecli-interactive
az network firewall update \
    --name fwName \ 
    --resource-group fwRG \
    --enable-dns-proxy true
```

#### <a name="configure-dns-proxy---azure-powershell"></a>設定 DNS proxy-Azure PowerShell

您可以使用 Azure PowerShell 來設定 Azure 防火牆中的 DNS proxy 設定。 您也可以使用它來更新虛擬網路，以使用 Azure 防火牆作為 DNS 伺服器。

##### <a name="configure-virtual-network-dns-servers"></a>設定虛擬網路 DNS 伺服器

下列範例會將虛擬網路設定為使用 Azure 防火牆作為 DNS 伺服器。

```azurepowershell
$dnsServers = @("<firewall-private-IP>")
$VNet = Get-AzVirtualNetwork -Name "VNetName" -ResourceGroupName "VNetRG"
$VNet.DhcpOptions.DnsServers = $dnsServers

$VNet | Set-AzVirtualNetwork
```

##### <a name="enable-dns-proxy"></a>啟用 DNS proxy

下列範例會啟用 Azure 防火牆中的 DNS proxy 功能。

```azurepowershell
$azFw = Get-AzFirewall -Name "fwName" -ResourceGroupName "fwRG"
$azFw.DNSEnableProxy = $true

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>後續步驟

[網路規則中的 FQDN 篩選](fqdn-filtering-network-rules.md)
