---
title: 設定閘道以路由傳送要求
description: 了解如何為 Service Fabric Mesh 上執行的應用程式設定處理傳入流量的閘道。
author: dkkapur
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: ec408403d4baa0f211c6bfe867a15c96513693cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75461961"
---
# <a name="configure-a-gateway-resource-to-route-requests"></a>設定閘道資源以路由要求

閘道資源會用來將傳入流量路由至裝載您應用程式的網路。 設定閘道資源以指定規則，讓要求透過此規則來導向特定服務或端點 (以要求結構為依據)。 請參閱 [Service Fabric Mesh 中的網路功能簡介](service-fabric-mesh-networks-and-gateways.md)，深入了解 Mesh 中的網路與閘道。 

閘道資源必須宣告為部署範本 (JSON 或 YAML) 的一部分，而且相依於網路資源。 本文件將概述可對您閘道設定的各種屬性，並且包含閘道組態範例。

## <a name="options-for-configuring-your-gateway-resource"></a>設定閘道資源的選項

由於閘道資源是您應用程式網路與基礎結構網路 (`open` 網路) 之間的橋樑。 您應只需要設定一個 (Mesh 預覽版有每個應用程式一個閘道的限制)。 資源宣告是由兩個主要部分所組成：資源中繼資料和屬性。 

### <a name="gateway-resource-metadata"></a>閘道資源中繼資料

閘道會以下列中繼資料進行宣告：
* `apiVersion` - 必須設為 "2018-09-01-preview" (或是未來的更新版本)
* `name` - 此閘道的字串名稱
* `type` - "Microsoft.ServiceFabricMesh/gateways"
* `location` - 應該設定為您應用程式/網路的位置；通常會是您部署中位置參數的參考
* `dependsOn` - 將此閘道作為其輸入點的網路

以下是閘道在 Azure Resource Manager (JSON) 部署範本中的樣子： 

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "myGateway",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/myNetwork"
  ],
  "properties": {
    [...]
  }
}
```

### <a name="gateway-properties"></a>閘道屬性

屬性區段會用來定義其間有閘道的網路，以及用來路由要求的規則。 

#### <a name="source-and-destination-network"></a>來源和目的地網路 

每個閘道都需要 `sourceNetwork` 和 `destinationNetwork`。 來源網路會定義為您應用程式將從中接收輸入要求的網路。 其名稱屬性應一律設定為 "Open"。 目的地網路是要求的目標網路。 其名稱值應設定為您應用程式區域網路的資源名稱 (應包含資源的完整參考)。 請參閱下方組態範例，了解在稱為 "myNetwork" 的網路中，部署應是什麼樣子。

```json 
"properties": {
  "description": "Service Fabric Mesh Gateway",
  "sourceNetwork": {
    "name": "Open"
  },
  "destinationNetwork": {
    "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'myNetwork')]"
  },
  [...]
}
```

#### <a name="rules"></a>規則 

閘道可以有多個路由規則來指定如何處理傳入流量。 路由規則會為指定應用程式定義接聽連接埠與目的地端點之間的關聯性。 針對 TCP 路由規則，連接埠：端點之間的對應是 1:1。 針對 HTTP 路由規則，您可以設定較複雜的路由規則來檢查要求路徑及選用標頭，以決定如何路由要求。 

路由規則的指定會以每個連接埠為基礎。 在閘道組態的 [屬性] 區段中，每個輸入連接埠都有自己的規則陣列。 

#### <a name="tcp-routing-rules"></a>TCP 路由規則 

TCP 路由規則中包含下列屬性： 
* `name` - 規則的參考，可以是您選擇的任何字串 
* `port` - 接聽傳入要求的連接埠 
* `destination` - 必須將要求路由到其中的端點規格，包含 `applicationName`、`serviceName` 和 `endpointName`

以下是 TCP 路由規則的範例：

```json
"properties": {
  [...]
  "tcp": [
    {
      "name": "web",
      "port": 80,
      "destination": {
        "applicationName": "myApp",
        "serviceName": "myService",
        "endpointName": "myListener"
      }
    }
  ]
}
```


#### <a name="http-routing-rules"></a>HTTP 路由規則 

HTTP 路由規則中包含下列屬性： 
* `name` - 規則的參考，可以是您選擇的任何字串 
* `port` - 接聽傳入要求的連接埠 
* `hosts` - 原則陣列，套用於傳向上述指定連接埠上各種「主機」的要求。 主機是一組可在網路中執行，並且可以處理傳入要求的應用程式和服務，也就是 Web 應用程式。 主機原則會依序進行解譯，因此您應以遞減特異性層級的方式建立下列項目
    * `name` - 主機的 DNS 名稱，下列路由規則會針對此項目來指定。 此處使用 "*"，表示會為所有主機建立路由規則。
    * `routes` - 此特定主機的原則陣列
        * `match` - 要套用此規則的傳入要求結構規格 (以 `path` 為依據)
            * `path` - 包含 `value`(傳入的 URI)、`rewrite` (轉送要求的方式) 和 `type` (目前只能是 "Prefix")
            * `header` - 標頭值的選擇性陣列，用來在要求標頭中比對要求是否符合路徑規格 (請見上方)。
              * 每個項目都包含 `name`(要比對的標頭字串名稱)、`value` (要求中標頭的字串) 和`type` (目前只能是 "Exact")
        * `destination` - 如果要求符合，就會將其路由至此目的地，這會使用 `applicationName`、`serviceName` 及 `endpointName` 來指定

以下是 HTTP 路由規則範例，這會套用到傳向連接埠 80 的要求，以傳向此網路中應用程式所提供的所有主機。 如果要求 URL 的結構符合指定路徑規格，亦即 `<IPAddress>:80/pickme/<requestContent>`，則會導向 `myListener` 端點。  

```json
"properties": {
  [...]
  "http": [
    {
      "name": "web",
      "port": 80,
      "hosts": [
        {
          "name": "*",
          "routes": [
            {
              "match": {
                "path": {
                  "value": "/pickme",
                  "rewrite": "/",
                  "type": "Prefix"
                }
              },
              "destination": {
                "applicationName": "meshApp",
                "serviceName": "myService",
                "endpointName": "myListener"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="sample-config-for-a-gateway-resource"></a>閘道資源的組態範例 

完整閘道資源組態會如下所示 (改寫自 [Mesh 範例存放庫](https://github.com/Azure-Samples/service-fabric-mesh/blob/2018-09-01-preview/templates/ingress/meshingress.linux.json)中可用的輸入範例)：

```json
{
  "apiVersion": "2018-09-01-preview",
  "name": "ingressGatewayLinux",
  "type": "Microsoft.ServiceFabricMesh/gateways",
  "location": "[parameters('location')]",
  "dependsOn": [
    "Microsoft.ServiceFabricMesh/networks/meshNetworkLinux"
  ],
  "properties": {
    "description": "Service Fabric Mesh Gateway for Linux mesh samples.",
    "sourceNetwork": {
      "name": "Open"
    },
    "destinationNetwork": {
      "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'meshNetworkLinux')]"
    },
    "http": [
      {
        "name": "web",
        "port": 80,
        "hosts": [
          {
            "name": "*",
            "routes": [
              {
                "match": {
                  "path": {
                    "value": "/hello",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "helloWorldService",
                  "endpointName": "helloWorldListener"
                }
              },
              {
                "match": {
                  "path": {
                    "value": "/counter",
                    "rewrite": "/",
                    "type": "Prefix"
                  }
                },
                "destination": {
                  "applicationName": "meshAppLinux",
                  "serviceName": "counterService",
                  "endpointName": "counterServiceListener"
                }
              }
            ]
          }
        ]
      }
    ]
  }
}
```

此閘道已設定為適用於 Linux 應用程式 "meshAppLinux"，其包含至少兩種服務 "helloWorldService" 和 "counterService"，而服務會接聽連接埠 80。 根據傳入要求的 URL 結構，閘道會將要求路由至這些服務中的其中一個。 
* " \<IPAddress> ： 80/helloWorld/ \<request\> " 會導致要求被導向 helloWorldService 中的 "helloWorldListener"。 
* " \<IPAddress> ： 80/counter/ \<request\> " 會導致要求被導向至 counterService 中的 "counterListener"。 

## <a name="next-steps"></a>後續步驟
* 部署[輸入範例](https://github.com/Azure-Samples/service-fabric-mesh/tree/2018-09-01-preview/templates/ingress)以查看作用中的閘道
