---
title: 在 Azure 應用程式閘道上裝載多個網站
description: 本文提供「Azure 應用程式閘道」多站台支援的概觀。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: 4d945a255dacd35c61c3c80574b7d46b56de4aab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257405"
---
# <a name="application-gateway-multiple-site-hosting"></a>應用程式閘道多站台裝載

多個網站託管使您能夠在應用程式閘道的同一端口上配置多個 Web 應用程式。 此功能可讓您將最多 100 個網站新增到一個應用程式閘道，為您的部署設定更有效率的拓撲。 每個網站都可以導向到自己的後端集區。 在下面的示例中，應用程式閘道為兩個後端`contoso.com`伺服器`fabrikam.com`池（稱為 ContosoServerPool 和 FabrikamServerPool）提供流量。

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> 規則按在 v1 SKU 門戶中列出的順序進行處理。 對於 v2 SKU，精確匹配具有更高的優先順序。 強烈建議纖設定多站台接聽程式，再設定基本接聽程式。  這可確保流量路由傳送到右邊後端。 如果先列出了基本接聽程式，且該接聽程式符合傳入的要求，就會由該接聽程式處理。

對 `http://contoso.com` 的要求會路由傳送至 ContosoServerPool，而對 `http://fabrikam.com` 的要求則會路由傳送至 FabrikamServerPool。

同樣，您可以在同一應用程式閘道部署上承載同一父系網域的多個子域。 例如，您可以託管`http://blog.contoso.com`單個應用程式閘道`http://app.contoso.com`部署和單個應用程式閘道部署。

## <a name="host-headers-and-server-name-indication-sni"></a>主機標頭和伺服器名稱指示 (SNI)

有三個常見的機制可允許在相同的基礎結構上進行多站台裝載。

1. 將多個 Web 應用程式分別裝載在一個唯一的 IP 位址上。
2. 使用主機名稱將多個 Web 應用程式裝載在相同的 IP 位址上。
3. 使用不同的連接埠將多個 Web 應用程式裝載在相同的 IP 位址上。

目前，應用程式閘道支援單個公共 IP 位址，在其中偵聽流量。 因此，目前不支援多個應用程式，每個應用程式都有自己的 IP 位址。 

應用程式閘道支援多個應用程式，每個應用程式偵聽在不同的埠上，但此方案要求應用程式接受非標準埠上的流量。 這通常不是您想要的配置。

「應用程式閘道」需依賴 HTTP 1.1 主機標頭，才能在相同的公用 IP 位址和連接埠上裝載多個網站。 應用程式閘道上託管的網站還可以通過伺服器名稱指示 （SNI） TLS 擴展支援 TLS 卸載。 此案例表示用戶端瀏覽器和後端 Web 伺服陣列必須支援 RFC 6066 中所定義的 HTTP/1.1 和 TLS 擴充功能。

## <a name="listener-configuration-element"></a>接聽程式組態元素

對現有 HTTPListener 配置元素進行增強，以支援主機名稱和伺服器名稱指示元素。 它由應用程式閘道用於將流量路由到相應的後端池。 

以下代碼示例是範本檔中 HttpListeners 元素的程式碼片段：

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

您可以瀏覽[使用多站台裝載的 Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting)，以了解以範本為基礎的端對端部署。

## <a name="routing-rule"></a>路由規則

路由規則不需要更改。 應該繼續選擇路由規則 'Basic'，以將適當的站台接聽程式繫結到對應的後端位址集區。

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>後續步驟

了解多站台裝載之後，請移至 [使用多站台裝載建立應用程式閘道](tutorial-multiple-sites-powershell.md) ，以建立能夠支援多個 Web 應用程式的應用程式閘道。

