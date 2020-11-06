---
title: 使用私人 IP 位址進行輸入端點的內部路由
description: 本文提供有關如何使用私人 Ip 進行內部路由，以及將叢集中的輸入端點公開給 VNet 的其餘部分的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be37ed1da0da4da3db43ef4c1cd01ed962f24ed
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397303"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>使用私人 IP 進行輸入端點的內部路由 

這項功能可讓您使用私人 IP 來公開中的輸入端點 `Virtual Network` 。

## <a name="pre-requisites"></a>必要條件  
具有[私人 IP](./configure-application-gateway-with-private-frontend-ip.md)設定的應用程式閘道

有兩種方式可以設定控制器使用私人 IP 進行輸入，

## <a name="assign-to-a-particular-ingress"></a>指派給特定輸入
若要透過私人 IP 公開特定輸入，請使用輸入中的注釋 [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) 。

### <a name="usage"></a>使用方式
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

針對沒有私人 IP 的應用程式閘道，將會忽略 Ingresses 標注的 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 。 這會顯示在輸入事件和 AGIC pod 記錄檔中。

* 輸入事件中指出的錯誤

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* AGIC 記錄中指出的錯誤

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>全域指派
在此情況下，需求是限制所有 Ingresses 都必須透過私人 IP 公開，請 `appgw.usePrivateIP: true` 在設定中使用 `helm` 。

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
> 應用程式閘道 v2 SKU 需要公用 IP。 如果您需要將應用程式閘道設為私用，請將應用程式閘道附加 [`Network Security Group`](../virtual-network/network-security-groups-overview.md) 至應用程式閘道的子網以限制流量