---
title: 包含檔案
description: 包含檔案
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70ac106995324c758bde942d12191a01e3457e6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67173713"
---
> [!NOTE]
> 這些範例不會套用到 S2S/ExpressRoute 並存組態。
> 如需在並存設定中使用閘道的詳細資訊，請參閱[設定共存的連接。](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>新增閘道

當您使用傳統資源模型將閘道新增至虛擬網路時，需先修改網路組態檔，接著立即建立閘道。 以下範例中的值必須出現在檔案中，才能建立閘道。 如果您的虛擬網路先前已有與其相關聯的閘道，其中的一些值將會出現。 請修改檔案以反映下方的值。

### <a name="download-the-network-configuration-file"></a>下載網路組態檔案

1. 使用[網路組態檔](../articles/virtual-network/virtual-networks-using-network-configuration-file.md)一文中的步驟，下載網路組態檔。 使用文字編輯器開啟檔案。
2. 將區域網路站台新增至檔案中。 您可以使用任何有效的位址前置詞。 您可以為 VPN 閘道新增任何有效的 IP 位址。 本區段中的位址值並非用於 ExpressRoute 作業，但對檔案驗證而言是必要的值。 在範例中，"branch1" 是站台的名稱。 您可以使用不同的名稱，但務必使用與檔案中 [Gateway] 區段中相同的值。

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. 請瀏覽至 VirtualNetworkSites 並修改欄位。

   * 確認您的虛擬網路是否有閘道子網路存在。 如果不存在，可以現在新增子網路。 名稱必須是 "GatewaySubnet"。
   * 確認檔案的「Gateway」區段是否存在。 如果不存在，請新增。 將虛擬網路與區域網路站點 (代表您正在連線的網路) 建立關聯時，會需要用到。
   * 確認 connection type = Dedicated。 ExpressRoute 連線需要如此。

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. 請儲存檔案，並將檔案上傳到 Azure。

### <a name="create-the-gateway"></a>建立閘道

使用以下的命令建立閘道器。 用您自己的值取代所有的值。

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>確認已建立閘道

使用下列命令，確認已建立閘道。 這個命令也會擷取閘道器識別碼，您在其他作業會需要它。

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>調整閘道器大小

有幾個 [閘道 SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)。 您可以使用下列命令隨時變更閘道器 SKU。

> [!IMPORTANT]
> 此命令不適用於 UltraPerformance 閘道。 若要將您的閘道變更為 UltraPerformance 閘道，請先移除現有的 ExpressRoute 閘道，然後建立新的 UltraPerformance 閘道。 若要從 UltraPerformance 閘道降級您的閘道，請先移除 UltraPerformance 閘道，然後建立新的閘道。
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>移除閘道器

使用以下的命令移除閘道器

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```