---
title: 對入口終結點使用私人 IP 位址進行內部路由
description: 本文提供有關如何使用專用 IP 進行內部路由，從而將群集中的入口終結點公開給 VNet 的其餘部分的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795499"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>將專用 IP 用於入口終結點的內部路由 

此功能允許`Virtual Network`使用專用 IP 公開入口終結點。

## <a name="pre-requisites"></a>必要條件  
具有[專用 IP 配置](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)的應用程式閘道

有兩種方法可以將控制器配置為對入口使用專用 IP，

## <a name="assign-to-a-particular-ingress"></a>分配給特定入口
要公開專用 IP 上的特定入口，請使用入口[`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip)中的注釋。

### <a name="usage"></a>使用量
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

對於沒有專用 IP 的應用程式閘道，將忽略帶有`appgw.ingress.kubernetes.io/use-private-ip: "true"`帶"入口"的入侵。 這將在入口事件和 AGIC 窗格日誌中指示。

* 入口事件中所示的錯誤

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* AGIC 日誌中指示的錯誤

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>在全球範圍內分配
在這種情況下，要求限制所有入口通過專用 IP 公開，在`appgw.usePrivateIP: true`配置中使用。 `helm`

### <a name="usage"></a>使用量
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

這將使入口控制器在應用程式閘道上配置前端攔截器時篩選專用 IP 的 IP 位址配置。
如果未`usePrivateIP: true`分配私有 IP，AGIC 將發生恐慌和崩潰。

> [!NOTE]
> 應用程式閘道 v2 SKU 需要公共 IP。 如果要求應用程式閘道為私有，請將 附加到[`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview)應用程式閘道的子網以限制流量。
