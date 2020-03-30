---
title: 應用程式閘道入口控制器注釋
description: 本文提供有關特定于應用程式閘道入口控制器的注釋的文檔。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335814"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>應用程式閘道入口控制器的注釋 

## <a name="introductions"></a>介紹

庫伯內斯入口資源可以使用任意鍵/值對進行進一步調整。 AGIC 依靠注釋來對應用程式閘道功能進行程式設計，這些功能無法通過入口 YAML 進行配置。 入口注釋應用於所有 HTTP 設置、後端池和從入口資源派生的攔截器。

## <a name="list-of-supported-annotations"></a>受支援的注釋清單

要由 AGIC 觀察入口資源，必須用`kubernetes.io/ingress.class: azure/application-gateway`進行**注注**。 只有這樣，AGIC 才能處理有問題的入口資源。

| 注釋鍵 | 值類型 | 預設值 | 允許的值
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`（秒） | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`（秒） | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>後端路徑首碼

此注釋允許用此注釋中指定的首碼重寫入口資源中指定的後端路徑。 這允許使用者公開終結點不同于用於在入口資源中公開服務的終結點名稱的服務。

### <a name="usage"></a>使用量

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>範例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
在上面的示例中，我們定義了一個帶有注釋`go-server-ingress-bkprefix``appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"`的入口資源。 注釋告訴應用程式閘道創建 HTTP 設置，該設置將具有路徑首碼覆蓋到`/hello``/test/`的路徑。

> [!NOTE] 
> 在上面的示例中，我們只定義了一個規則。 但是，注釋適用于整個入口資源，因此，如果使用者定義了多個規則，則將為指定的每個路徑設置後端路徑首碼。 因此，如果使用者想要具有不同路徑首碼的不同規則（即使對於同一服務），則需要定義不同的入口資源。

## <a name="tls-redirect"></a>TLS 重定向

可以將應用程式閘道[配置為](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview)自動將 HTTP URL 重定向到其 HTTPS 對應方。 當存在此注釋且 TLS 配置正確時，Kubernetes 入口控制器將創建[一個帶有重定向配置的路由規則](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration)，並將更改應用於應用程式閘道。 創建的重定向將為 HTTP `301 Moved Permanently`。

### <a name="usage"></a>使用量

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>範例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>連接排水

`connection-draining`：此注釋允許使用者指定是否啟用連接耗盡。
`connection-draining-timeout`：此注釋允許使用者指定超時，之後應用程式閘道將終止對排水後端終結點的請求。

### <a name="usage"></a>使用量

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>範例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>基於 Cookie 的關聯

此注釋允許指定是否啟用基於 Cookie 的關聯。

### <a name="usage"></a>使用量

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>範例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>要求逾時

此注釋允許指定請求超時，在幾秒鐘內，應用程式閘道將在未收到回應時使請求失敗。

### <a name="usage"></a>使用量

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>範例

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>使用專用 IP

此注釋允許我們指定是否在應用程式閘道的專用 IP 上公開此終結點。

> [!NOTE]
> * 應用程式閘道不支援同一端口上的多個 IP（例如：80/443）。 帶注釋`appgw.ingress.kubernetes.io/use-private-ip: "false"`的入口和其他帶`appgw.ingress.kubernetes.io/use-private-ip: "true"`on`HTTP`的入口將導致 AGIC 在更新應用程式閘道時失敗。
> * 對於沒有專用 IP 的應用程式閘道，將忽略帶有`appgw.ingress.kubernetes.io/use-private-ip: "true"`入口的入口。 這將反映在帶有`NoPrivateIP`警告的入口的控制器日誌和入口事件中。


### <a name="usage"></a>使用量
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>範例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>後端協定

此注釋允許我們指定應用程式閘道在與 Pods 對話時應使用的協定。 支援的協定： `http``https`

> [!NOTE]
> * 雖然應用程式閘道支援自簽章憑證，但當前 AGIC 僅在 Pod`https`使用由知名 CA 簽名的證書時支援。
> * 確保不要將埠 80 與 HTTPS 一起使用，並且埠 443 與 POD 上的 HTTP 一起使用。

### <a name="usage"></a>使用量
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>範例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```