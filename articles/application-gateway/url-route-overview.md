---
title: Azure 應用程式閘道 URL 型內容路由概觀
description: 本文概要說明 Azure 應用程式閘道 URL 型內容路由、UrlPathMap 設定和 PathBasedRouting 規則。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 09/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: a9b2e8148586ec58ea6a7a033099e726920857b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84987940"
---
# <a name="url-path-based-routing-overview"></a>URL 路徑型路由概觀

URL 路徑型路由可讓您根據要求的 URL 路徑，將流量路由傳送至後端伺服器集區。 

有一個案例是將對於不同內容類型的要求路由傳送至不同的後端伺服器集區。

在下列範例中，應用程式閘道會針對 contoso.com 從三個後端伺服器集區提供流量，例如：VideoServerPool、ImageServerPool 和 DefaultServerPool。

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

對 http\://contoso.com/video/* 的要求會路由傳送至 VideoServerPool，而 http\://contoso.com/images/* 則會路由傳送至 ImageServerPool。 如果沒有任何路徑模式相符，則會選取 DefaultServerPool。

> [!IMPORTANT]
> 針對 v1 SKU，規則會依其在入口網站中列出的順序進行處理。 如果先列出了基本接聽程式，且該接聽程式符合傳入的要求，就會由該接聽程式處理。 若為 v2 SKU，完全相符會具有較高的優先順序。 不過，強烈建議您先設定多網站接聽程式，再設定基本接聽程式。 這可確保流量路由傳送到右邊後端。

## <a name="urlpathmap-configuration-element"></a>UrlPathMap 組態元素

urlPathMap 元素是用來指定與後端伺服器集區對應的路徑模式。 下列程式碼範例是來自範本檔案的 urlPathMap 元素程式碼片段。

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

### <a name="pathpattern"></a>PathPattern

PathPattern 是要比對的路徑模式清單。 每個字串都必須以 / 開始，而且唯一允許出現 "*" 的地方是緊接在 "/" 之後的結尾處。 傳送給路徑比對器的字串未在第一個 ? 或 # 之後包含任何文字，而那些字元在這裡是不允許的。 否則，PathPattern 中會允許 URL 中允許的任何字元。

支援的模式取決於您是部署應用程式閘道 v1 或 v2：

#### <a name="v1"></a>v1

路徑規則不區分大小寫。

|v1 路徑模式  |是否支援？  |
|---------|---------|
|`/images/*`     |是|
|`/images*`     |是|
|`/images/*.jpg`     |否|
|`/*.jpg`     |否|
|`/Repos/*/Comments/*`     |否|
|`/CurrentUser/Comments/*`     |是|

#### <a name="v2"></a>v2

路徑規則不區分大小寫。

|v2 路徑模式  |是否支援？  |
|---------|---------|
|`/images/*`     |是|
|`/images*`     |是|
|`/images/*.jpg`     |否|
|`/*.jpg`     |否|
|`/Repos/*/Comments/*`     |否|
|`/CurrentUser/Comments/*`     |是|

如需詳細資訊，您可以查看 [使用 URL 型路由的 Resource Manager 範本](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) 。

## <a name="pathbasedrouting-rule"></a>PathBasedRouting 規則

類型 PathBasedRouting 的 RequestRoutingRule 可用來將接聽程式繫結至 urlPathMap。 針對此接聽程式接收到的所有要求都會根據 urlPathMap 中指定的原則進行路由傳送。
PathBasedRouting 規則的程式碼片段：

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}"
    }

}
    }
]
```

## <a name="next-steps"></a>接下來的步驟

了解 URL 型內容路由之後，請移至 [使用 URL 型路由建立應用程式閘道](create-url-route-portal.md) ，利用 URL 路由規則來建立應用程式閘道。
