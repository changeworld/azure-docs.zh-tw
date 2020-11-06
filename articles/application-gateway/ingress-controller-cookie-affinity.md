---
title: 使用應用程式閘道啟用以 cookie 為基礎的親和性
description: 本文提供有關如何使用應用程式閘道啟用 cookie 型親和性的相關資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397411"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>使用應用程式閘道啟用以 Cookie 為基礎的親和性
如 [Azure 應用程式閘道檔](./application-gateway-components.md#http-settings)中所述，應用程式閘道支援以 cookie 為基礎的親和性，這表示它可以將來自使用者會話的後續流量導向至同一部伺服器進行處理。

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