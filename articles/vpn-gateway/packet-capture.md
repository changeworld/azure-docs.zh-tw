---
title: Azure VPN 閘道：設定封包捕獲
description: 瞭解您可以在 VPN 閘道上使用的封包捕捉功能。
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75353509"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>設定 VPN 閘道的封包捕獲

連線性和效能相關的問題通常會很複雜，而且需要大量的時間和投入量來縮小問題的原因。 封包捕獲的功能可大幅降低將問題範圍縮小到網路特定部分的時間，例如問題是在網路的用戶端、網路的 Azure 端，或介於其間的某個位置。 一旦問題縮小後，就能更有效率地進行偵錯工具並採取補救動作。

有一些常用的封包捕獲工具可供使用。 不過，使用這些工具來取得相關的封包捕捉通常會冗長，特別是在處理高容量的流量案例時。 VPN 閘道封包捕獲所提供的篩選功能會成為主要的區別。 除了常用的封包捕獲工具之外，您也可以使用 VPN 閘道封包捕獲。

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN 閘道封包捕獲篩選功能

VPN 閘道封包捕獲可以根據客戶需求，在閘道或特定連線上執行。 您也可以同時在多個通道上執行封包捕獲。 您可以使用 VPN 閘道上的篩選，來捕捉單一或雙向流量、IKE 和 ESP 流量，以及內部封包。

使用5個元組篩選（來源子網、目的地子網、來源埠、目的地埠、通訊協定）和 TCP 旗標（SYN、ACK、FIN、URG、PSH、RST）在將大量流量的問題隔離時，會很有説明。

執行封包捕獲時，每個屬性只能使用一個選項。

## <a name="setup-packet-capture-using-powershell"></a>使用 PowerShell 設定封包捕獲

如需啟動和停止封包捕獲的 PowerShell 命令，請參閱下列範例。 如需參數選項（例如如何建立篩選器）的詳細資訊，請參閱此 PowerShell[檔](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)。

### <a name="start-packet-capture-for-a-vpn-gateway"></a>啟動 VPN 閘道的封包捕獲

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

選擇性參數 **-FilterData**可以用來套用篩選。

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>停止 VPN 閘道的封包捕獲

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>啟動 VPN 閘道連線的封包捕獲

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

選擇性參數 **-FilterData**可以用來套用篩選。

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>在 VPN 閘道連線上停止封包捕獲

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>主要考量

- 執行封包捕獲可能會影響效能。 請記得在不需要封包捕獲時，將它停止。
- 建議的最小封包捕獲持續時間為600秒。 較短的封包捕捉持續時間可能無法提供完整的資料，因為該路徑上的多個元件之間的同步問題。
- 封包捕獲資料檔案會以 PCAP 格式產生。 使用 Wireshark 或其他常用的應用程式來開啟 PCAP 檔案。

## <a name="next-steps"></a>後續步驟

如需 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道](vpn-gateway-about-vpngateways.md)
