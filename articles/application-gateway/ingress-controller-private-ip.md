---
title: 將私人 IP 位址用於輸入端點的內部路由
description: 本文提供有關如何將私人 Ip 用於內部路由的資訊，進而將叢集內的輸入端點公開給其餘的 VNet。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 33b70ba8ab7ffef90c42f53e58a2d27e619862f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84806789"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>將私人 IP 用於輸入端點的內部路由 

這項功能可讓使用私人 IP 公開中的輸入端點 `Virtual Network` 。

## <a name="pre-requisites"></a>必要條件  
具有[私人 IP](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)設定的應用程式閘道

有兩種方式可將控制器設定為使用私人 IP 進行輸入，

## <a name="assign-to-a-particular-ingress"></a>指派給特定輸入
若要透過私人 IP 公開特定的輸入，請使用輸入中的注釋 [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) 。

### <a name="usage"></a>使用方式
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

對於沒有私人 IP 的應用程式閘道，將會忽略以標注的會輸入 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 。 這會在輸入事件和 AGIC pod 記錄中指出。

* 輸入事件中所指出的錯誤

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* AGIC 記錄中所指出的錯誤

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>全域指派
在此情況下，需求是限制所有要透過私人 IP 公開的會輸入，並 `appgw.usePrivateIP: true` 在 config 中使用 `helm` 。

### <a name="usage"></a>使用方式
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

這會讓輸入控制器在應用程式閘道上設定前端接聽程式時，篩選私人 IP 的 IP 位址設定。
如果 `usePrivateIP: true` 未指派任何私人 IP，AGIC 會死毀並當機。

> [!NOTE]
> 應用程式閘道 v2 SKU 需要公用 IP。 如果您需要應用程式閘道是私用的，請將附加 [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) 至應用程式閘道的子網，以限制流量。
