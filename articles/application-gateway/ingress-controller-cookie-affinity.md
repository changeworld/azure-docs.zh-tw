---
title: 使用應用程式閘道啟用基於 Cookie 的關聯
description: 本文提供有關如何與應用程式閘道啟用基於 Cookie 的關聯的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795982"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>使用應用程式閘道啟用基於 Cookie 的關聯
如[Azure 應用程式閘道文檔](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)中所述，應用程式閘道支援基於 Cookie 的關聯，這意味著它可以將後續流量從使用者會話定向到同一伺服器進行處理。

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
