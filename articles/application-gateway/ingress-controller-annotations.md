---
title: 應用程式閘道輸入控制器批註
description: 本文提供應用程式閘道輸入控制器專屬附注的相關檔。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80335814"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>應用程式閘道輸入控制器的注釋 

## <a name="introductions"></a>介紹

Kubernetes 輸入資源可以用任意的索引鍵/值組來標注。 AGIC 依賴注釋來設計應用程式閘道功能，而這些功能無法透過輸入 YAML 設定。 輸入批註會套用至所有 HTTP 設定、後端集區，以及從輸入資源衍生的接聽程式。

## <a name="list-of-supported-annotations"></a>支援的批註清單

針對要由 AGIC 觀察的輸入資源，它必須加上 **批註** `kubernetes.io/ingress.class: azure/application-gateway` 。 只有 AGIC 會使用有問題的輸入資源。

| 注釋索引鍵 | 數值類型 | 預設值 | 允許的值
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` (秒)  | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` (秒)  | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>後端路徑前置詞

此批註可讓輸入資源中指定的後端路徑，以此批註中指定的前置詞進行重寫。 這可讓使用者公開服務，其端點與用來在輸入資源中公開服務的端點名稱不同。

### <a name="usage"></a>使用狀況

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
在上述範例中，我們已定義名為的輸入資源 `go-server-ingress-bkprefix` 和批註 `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` 。 批註會告知應用程式閘道建立 HTTP 設定，此設定會有路徑的路徑前置詞覆 `/hello` 寫 `/test/` 。

> [!NOTE] 
> 在上述範例中，我們只定義了一個規則。 不過，批註適用于整個輸入資源，因此，如果使用者已定義多個規則，則會為每個指定的路徑設定後端路徑前置詞。 因此，如果使用者想要具有不同路徑前置詞的不同規則， (即使是相同的服務) 它們都必須定義不同的輸入資源。

## <a name="tls-redirect"></a>TLS 重新導向

應用程式閘道 [可以設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview) 為自動將 HTTP url 重新導向至其 HTTPS 對應專案。 當此注釋存在且已正確設定 TLS 時，Kubernetes 輸入控制器會使用重新導向設定來建立 [路由規則](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration) ，並將變更套用至您的應用程式閘道。 所建立的重新導向將會是 HTTP `301 Moved Permanently` 。

### <a name="usage"></a>使用狀況

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

## <a name="connection-draining"></a>連接清空

`connection-draining`：此批註可讓使用者指定是否要啟用連接清空。
`connection-draining-timeout`：此批註可讓使用者指定等待時間之後，應用程式閘道將會終止清空後端端點的要求。

### <a name="usage"></a>使用狀況

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

## <a name="cookie-based-affinity"></a>以 Cookie 為基礎的相似性

這個批註可讓您指定是否要啟用以 cookie 為基礎的親和性。

### <a name="usage"></a>使用狀況

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

此批註可讓您指定要求超時（以秒為單位），在這之後，如果未收到回應，應用程式閘道將會導致要求失敗。

### <a name="usage"></a>使用狀況

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

## <a name="use-private-ip"></a>使用私人 IP

此批註可讓我們指定是否要在應用程式閘道的私人 IP 上公開此端點。

> [!NOTE]
> * 應用程式閘道不支援相同埠上的多個 Ip (範例： 80/443) 。 具有批註的輸入 `appgw.ingress.kubernetes.io/use-private-ip: "false"` 和另一個使用注釋， `appgw.ingress.kubernetes.io/use-private-ip: "true"` `HTTP` 會導致 AGIC 在更新應用程式閘道時失敗。
> * 針對沒有私人 IP 的應用程式閘道，將會 `appgw.ingress.kubernetes.io/use-private-ip: "true"` 忽略 Ingresses with。 這會反映在這些 ingresses 的控制器記錄和輸入事件中，但出現 `NoPrivateIP` 警告。


### <a name="usage"></a>使用狀況
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

## <a name="backend-protocol"></a>後端通訊協定

此批註可讓我們指定應用程式閘道與 pod 說話時應使用的通訊協定。 支援的通訊協定： `http` 、 `https`

> [!NOTE]
> * 雖然應用程式閘道上支援自我簽署憑證，但目前只有 `https` 當 pod 使用由知名 CA 簽署的憑證時，AGIC 才會支援。
> * 請務必不要在 pod 上搭配使用 HTTPS 和埠443與 HTTP 來使用埠80。

### <a name="usage"></a>使用狀況
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