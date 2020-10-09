---
title: 設定 Azure Red Hat OpenShift 4 的 DNS 轉送
description: 設定 Azure Red Hat OpenShift 4 的 DNS 轉送
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6784c69c049945f3bd4977ecd647d57ec59bf9f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82232627"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>在 Azure Red Hat OpenShift 4 叢集上設定 DNS 轉送

若要在 Azure Red Hat OpenShift 叢集上設定 DNS 轉送，您必須修改 DNS 操作員。 這種修改可讓在叢集內執行的應用程式 pod 解析裝載于叢集外部私人 DNS 伺服器的名稱。 這些步驟記載于 [此處](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html)的 OpenShift 4.3。

例如，如果您想要轉寄 DNS 伺服器192.168.100.10 解析 *. example.com 的所有 DNS 要求，您可以執行下列動作來編輯操作員設定：
 
```bash
oc edit dns.operator/default
```
 
這將會啟動編輯器，而您可以 `spec: {}` 用下列內容取代：
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

儲存檔案並結束編輯器。

## <a name="next-steps"></a>接下來的步驟
請在 [這裡](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html)查看 OPENSHIFT 4.3 DNS 轉送的詳細資訊。