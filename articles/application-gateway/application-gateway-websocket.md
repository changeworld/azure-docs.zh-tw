---
title: Azure 應用程式閘道中的 Web 通訊端支援
description: 應用程式閘道可跨所有閘道大小對 WebSocket 提供原生支援。 沒有使用者可配置的設置。
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: baa02c4d946a121f26f421af99835ae2bea18847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130342"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>應用程式閘道中的 WebSocket 支援概觀

應用程式閘道可跨所有閘道大小對 WebSocket 提供原生支援。 使用者無法進行設定來選擇要啟用或停用 WebSocket 支援。 

以 [RFC6455](https://tools.ietf.org/html/rfc6455) 標準化的 WebSocket 通訊協定可透過長時間執行的 TCP 連線讓伺服器和用戶端之間能夠進行全雙工通訊。 此功能可讓 Web 伺服器和用戶端之間進行互動性更高的通訊，此通訊可以是雙向的，而不需要像 HTTP 型實作所要求的進行輪詢。 不同於 HTTP，WebSocket 的負荷很低，而且可以對多個要求/回應重複使用相同的 TCP 連線，進而提升資源使用效率。 WebSocket 通訊協定設計為透過傳統 HTTP 連接埠 80 和 443 進行運作。

您可以在連接埠 80 或 443 上繼續使用標準 HTTP 接聽程式來接收 WebSocket 流量。 WebSocket 流量接著會使用應用程式閘道規則中指定的適當後端集區，來導向到已啟用 WebSocket 的後端伺服器。 後端伺服器必須回應應用程式閘道探查，[健康情況探查概觀](application-gateway-probe-overview.md)一節中會有所說明。 應用程式閘道健康情況探查僅限使用 HTTP/HTTPS。 每一部後端伺服器都必須回應 HTTP 探查，應用程式閘道才能將 WebSocket 流量路由傳送至伺服器。

它用於受益於快速且即時通訊的應用程式，例如聊天、儀表板和遊戲應用程式。

## <a name="how-does-websocket-work"></a>WebSocket 的工作原理

要建立 WebSocket 連接，用戶端和伺服器之間將交換基於 HTTP 的特定握手。 如果成功，應用程式層通訊協定會從 HTTP「升級」為 WebSocket，使用先前建立的 TCP 連線。 當發生這種情形時，HTTP 會完全退場；端點使用 WebSocket 通訊協定傳送或接收資料，直到 WebSocket 連線關閉為止。 

![網筒](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>接聽程式組態元素

現有的 HTTP 接聽程式可用來支援 WebSocket 流量。 以下是來自範例範本檔案中 httpListeners 元素的程式碼片段。 您必須同時擁有 HTTP 和 HTTPS 接聽程式才能支援 WebSocket 和保護 WebSocket 流量。 同樣，您可以使用門戶或 Azure PowerShell 創建具有埠 80/443 上的攔截器的應用程式閘道，以支援 WebSocket 流量。

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool、BackendHttpSetting 和路由規則組態

若後端集區具有已啟用 WebSocket 的伺服器，使用 backendAddressPool 加以定義。 backendHttpSetting 是以後端連接埠 80 和 443 加以定義。 HTTP 設置中的請求超時值也適用于 WebSocket 會話。 路由規則不需要更改，該規則用於將相應的攔截器綁定到相應的後端位址集區。 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>已啟用 WebSocket 的後端

後端必須在設定的連接埠 (通常是 80/443) 上執行 HTTP/HTTPS Web 伺服器，WebSocket 才能運作。 此需求是因為 WebSocket 通訊協定要求初始交握必須是 HTTP，並以升級為 WebSocket 通訊協定作為標頭欄位。 以下是標題的範例：

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

另一個原因是該應用程式閘道後端健全狀態探查只支援 HTTP 和 HTTPS 通訊協定。 如果後端伺服器未回應 HTTP 或 HTTPS 探查，它被帶離後端集區。

## <a name="next-steps"></a>後續步驟

在了解 WebSocket 支援之後，請移至 [建立應用程式閘道](quick-create-powershell.md) 以開始使用已啟用 WebSocket 的 Web 應用程式。