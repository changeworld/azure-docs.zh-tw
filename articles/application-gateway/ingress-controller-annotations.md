---
title: 應用程式閘道輸入控制器注釋
description: 本文提供應用程式閘道輸入控制器特定附注的相關檔。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f54381ddcd11a2e4a24d30d812468da85b5403de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335814"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>應用程式閘道輸入控制器的注釋 

## <a name="introductions"></a>作

Kubernetes 輸入資源可以用任意索引鍵/值組來標注。 AGIC 相依于程式應用程式閘道功能的注釋，無法透過輸入 YAML 進行設定。 輸入注釋會套用至所有從輸入資源衍生的 HTTP 設定、後端集區和接聽程式。

## <a name="list-of-supported-annotations"></a>支援的批註清單

對於要由 AGIC 觀察的輸入資源，必須以**標注** `kubernetes.io/ingress.class: azure/application-gateway` 。 只有 AGIC 才會使用有問題的輸入資源。

| 注釋索引鍵 | 數值類型 | 預設值 | 允許的值
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32`表示 | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32`表示 | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>後端路徑前置詞

此批註可讓輸入資源中指定的後端路徑以此注釋中指定的前置詞重寫。 這可讓使用者公開服務，其端點與用來在輸入資源中公開服務的端點名稱不同。

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
在上述範例中，我們已定義名為 `go-server-ingress-bkprefix` 且具有批註的輸入資源 `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` 。 批註會告訴應用程式閘道建立 HTTP 設定，其路徑的前置詞會覆寫 `/hello` `/test/` 。

> [!NOTE] 
> 在上述範例中，我們只定義了一個規則。 不過，注釋適用于整個輸入資源，因此，如果使用者已定義多個規則，則會針對每個指定的路徑設定後端路徑前置詞。 因此，如果使用者想要不同的規則使用不同的路徑前置詞（即使是針對相同的服務），他們必須定義不同的輸入資源。

## <a name="tls-redirect"></a>TLS 重新導向

應用程式閘道[可以設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-redirect-overview)為將 HTTP url 自動重新導向至其 HTTPS 對應專案。 當此批註存在且已正確設定 TLS 時，Kubernetes 輸入控制器將會使用重新導向設定來建立[路由規則](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal#add-a-routing-rule-with-a-redirection-configuration)，並將變更套用至您的應用程式閘道。 建立的重新導向將會是 HTTP `301 Moved Permanently` 。

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

## <a name="connection-draining"></a>清空連接

`connection-draining`：此批註可讓使用者指定是否要啟用連接清空。
`connection-draining-timeout`：此批註可讓使用者指定超時時間，之後應用程式閘道會終止清空後端端點的要求。

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

此批註可讓指定是否啟用以 cookie 為基礎的親和性。

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

此批註可讓您指定要求超時（以秒為單位），在這之後，如果未收到回應，應用程式閘道將會讓要求失敗。

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
> * 應用程式閘道不支援相同埠上的多個 Ip （例如：80/443）。 具有批註的輸入 `appgw.ingress.kubernetes.io/use-private-ip: "false"` 和的另一個 `appgw.ingress.kubernetes.io/use-private-ip: "true"` `HTTP` 會導致 AGIC 無法更新應用程式閘道。
> * 對於沒有私人 IP 的應用程式閘道，會忽略會輸入 with `appgw.ingress.kubernetes.io/use-private-ip: "true"` 。 這會反映在這些會輸入的控制器記錄和輸入事件中，並出現 `NoPrivateIP` 警告。


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

此批註可讓我們指定應用程式閘道在與 pod 交談時應該使用的通訊協定。 支援的通訊協定： `http` 、`https`

> [!NOTE]
> * 雖然應用程式閘道支援自我簽署憑證，但目前只有 `https` 在 pod 使用由知名 CA 簽署的憑證時，AGIC 才支援。
> * 請務必不要在 pod 上使用埠80搭配 HTTPS 和埠443搭配 HTTP。

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