---
title: Azure VPN 閘道：設定封包捕獲
description: 瞭解您可以在 VPN 閘道上使用的封包捕獲功能，以協助縮小問題的原因。
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: radwiv
ms.openlocfilehash: 3be01f6d8e1fb1f6ba541f8d1cb0c92d2a43b0da
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073099"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>設定 VPN 閘道的封包捕獲

連線能力和效能相關問題通常很複雜。 這可能需要花費很多時間和精力來縮減問題的原因。 封包捕獲可協助您將問題範圍縮小至網路的特定部分。 它可協助您判斷問題是在網路的用戶端、網路的 Azure 端，還是介於其間的某個位置。 在您縮小問題的範圍之後，就可以更有效率地進行 debug 和採取補救動作。

有一些常用的封包捕獲工具可供使用。 使用這些工具取得相關的封包捕獲可能會很麻煩，尤其是在高容量的流量案例中。 Azure VPN 閘道封包捕獲提供的篩選功能是一項主要的區別。 您可以使用 VPN 閘道封包捕獲搭配常用的封包捕獲工具。

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN 閘道封包捕獲篩選功能

您可以根據您的需求，在閘道或特定連接上執行 VPN 閘道封包捕獲。 您也可以同時在多個通道上執行封包捕獲。 您可以透過 VPN 閘道上的篩選，來捕捉單向或雙向流量、IKE 和 ESP 流量，以及內部封包。

當您隔離大量流量中的問題時，使用五個元組篩選器 (來源子網、目的地子網、來源埠、目的地埠、通訊協定) 和 TCP 旗標 (SYN、ACK、FIN、URG、PSH、RST) 會很有説明。

下列 JSON 範例和 JSON 架構會提供每個屬性的說明。 以下是當您執行封包捕獲時，要記住的一些限制：
- 在此處所示的架構中，篩選是一個陣列，但目前每次只能使用一個篩選準則。
- 您無法同時執行多個全閘道的封包捕捉。
- 您無法同時在單一連接上執行多個封包捕獲。 您可以同時在不同的連接上執行多個封包捕獲。
- 每個閘道最多可以平行執行五個封包捕獲。 這些封包捕捉可以是全閘道的封包捕獲和每個連線封包捕獲的組合。

### <a name="example-json"></a>範例 JSON
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>JSON 結構描述
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="set-up-packet-capture-by-using-powershell"></a>使用 PowerShell 設定封包捕獲

下列範例顯示啟動和停止封包捕獲的 PowerShell 命令。 如需參數選項的詳細資訊，請參閱 [AzVirtualnetworkGatewayPacketCapture](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)。

### <a name="start-packet-capture-for-a-vpn-gateway"></a>啟動 VPN 閘道的封包捕獲

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

您可以使用選擇性參數套用 `-FilterData` 篩選。

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>停止 VPN 閘道的封包捕獲

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>啟動 VPN 閘道連線的封包捕獲

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

您可以使用選擇性參數套用 `-FilterData` 篩選。

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>在 VPN 閘道連線上停止封包捕獲

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>主要考量

- 執行封包捕獲可能會影響效能。 如果您不需要封包捕獲，請記得將它停止。
- 建議的最小封包捕獲持續時間為600秒。 由於路徑上多個元件之間的同步問題，較短的封包捕獲可能無法提供完整的資料。
- 封包捕獲資料檔案會以 PCAP 格式產生。 使用 Wireshark 或其他常用的應用程式來開啟 PCAP 檔案。
- 以原則為基礎的閘道不支援封包捕獲。
- 如果 `SASurl` 未正確設定參數，追蹤可能會因儲存錯誤而失敗。 如需如何正確產生參數的範例 `SASurl` ，請參閱 [AzVirtualNetworkGatewayPacketCapture](https://docs.microsoft.com/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture)。



## <a name="next-steps"></a>後續步驟

如需 VPN 閘道的詳細資訊，請參閱 [什麼是 Vpn 閘道？](vpn-gateway-about-vpngateways.md)。
