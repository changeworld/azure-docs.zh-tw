---
title: Azure VPN 閘道：配置資料包捕獲
description: 瞭解可在 VPN 閘道上使用的資料包捕獲功能。
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353509"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>為 VPN 閘道配置資料包捕獲

連線性和性能相關問題通常非常複雜，需要大量時間和精力才能縮小問題的原因。 資料包捕獲的能力大大有助於縮短將問題範圍縮小到網路某些部分的時間，例如問題是否位於網路的用戶端、網路的 Azure 端或介於兩者之間的某處。 一旦問題縮小，調試和採取補救措施會更有效。

有一些常用的資料包捕獲工具。 但是，使用這些工具獲取相關的資料包捕獲通常很麻煩，尤其是在處理大容量流量方案時。 VPN 閘道資料包捕獲提供的篩選功能成為主要區別。 除了常用的資料包捕獲工具外，您還可以使用 VPN 閘道資料包捕獲。

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN 閘道資料包捕獲篩選功能

VPN 閘道資料包捕獲可以在閘道上或特定連接上運行，具體取決於客戶需求。 您還可以同時在多個隧道上運行資料包捕獲。 您可以捕獲單方向或雙向流量、IKE 和 ESP 流量以及內部資料包以及 VPN 閘道上的篩選。

使用 5 個元聯篩選器（源子網、目標子網、源埠、目標埠、協定）和 TCP 標誌（SYN、ACK、FIN、URG、PSH、RST）在隔離高容量流量上的問題時非常有用。

運行資料包捕獲時，每個屬性只能使用一個選項。

## <a name="setup-packet-capture-using-powershell"></a>使用 PowerShell 設置資料包捕獲

有關 PowerShell 命令啟動和停止資料包捕獲的示例，請參閱下面的示例。 有關參數選項（如如何創建篩選器）的詳細資訊，請參閱此 PowerShell[文檔](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)。

### <a name="start-packet-capture-for-a-vpn-gateway"></a>啟動 VPN 閘道的資料包捕獲

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

可選參數 **-FilterData**可用於應用篩選器。

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>停止 VPN 閘道的資料包捕獲

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>啟動 VPN 閘道連接的資料包捕獲

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

可選參數 **-FilterData**可用於應用篩選器。

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>停止 VPN 閘道連接上的資料包捕獲

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>主要考量

- 運行資料包捕獲可能會影響性能。 請記住，在不需要資料包捕獲時停止捕獲資料包。
- 建議的最小資料包捕獲持續時間為 600 秒。 由於路徑上的多個元件之間同步問題，資料包捕獲持續時間較短可能無法提供完整的資料。
- 資料包捕獲資料檔案以 PCAP 格式生成。 使用 Wireshark 或其他常用的應用程式打開 PCAP 檔。

## <a name="next-steps"></a>後續步驟

有關 VPN 閘道的詳細資訊，請參閱[關於 VPN 閘道的資訊](vpn-gateway-about-vpngateways.md)
