---
title: 啟用以 cookie 為基礎的親和性應用程式閘道
description: 本文提供如何使用應用程式閘道啟用 cookie 型親和性的相關資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3af2705fedbb9c19d4f128e8e997d3fa73f8b5a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807959"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>啟用以 Cookie 為基礎的親和性與應用程式閘道
如[Azure 應用程式閘道檔](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)中所述，應用程式閘道支援以 cookie 為基礎的親和性，這表示它可以將來自使用者會話的後續流量導向至同一部伺服器進行處理。

## <a name="example"></a>範例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```
