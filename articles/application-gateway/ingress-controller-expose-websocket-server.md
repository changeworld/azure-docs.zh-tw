---
title: 將 WebSocket 伺服器公開給應用程式閘道
description: 本文提供有關如何將 WebSocket 伺服器公開到具有 AKS 群集入口控制器的應用程式閘道的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 1f068c9d98a827afd16da01bdc40cbb6ca5dc465
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297827"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>將 WebSocket 伺服器公開給應用程式閘道

如應用程式閘道 v2 文檔中所述 -[它為 WebSocket 和 HTTP/2 協定提供本機支援](features.md#websocket-and-http2-traffic)。 請注意，對於應用程式閘道和 Kubernetes 入口 ， 沒有使用者可配置的設置可選擇性地啟用或禁用 WebSocket 支援。

下面的 Kubernetes 部署 YAML 顯示了用於部署 WebSocket 伺服器的最小配置，這與部署常規 Web 服務器相同：
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

鑒於所有先決條件都已滿足，並且您的 AKS 中由 Kubernetes 入口控制應用程式閘道，上述部署將導致 WebSocket 伺服器在應用程式閘道的公共 IP 和`ws.contoso.com`域的埠 80 上公開。

以下 cURL 命令將測試 WebSocket 伺服器部署：
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket 運行狀況探測器

如果部署未顯式定義運行狀況探測，應用程式閘道將嘗試 WebSocket 伺服器終結點上的 HTTP GET。
取決於伺服器實現（[這裡是我們愛的](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)）WebSocket特定的標頭可能需要（`Sec-Websocket-Version`例如）。
由於應用程式閘道不添加 WebSocket 標頭，因此來自 WebSocket 伺服器的應用程式閘道的運行狀況探測回應很可能是`400 Bad Request`。
因此，應用程式閘道會將您的 pod 標記為不正常，這最終將`502 Bad Gateway`導致 WebSocket 伺服器的消費者。
為了避免這種情況，您可能需要向伺服器添加用於運行狀況檢查的 HTTP GET 處理常式（`/health`例如，返回`200 OK`）。
