---
title: 為應用程式閘道入口控制器啟用多個命名空間支援
description: 本文提供有關如何在具有應用程式閘道入口控制器的 Kubernetes 群集中啟用多個命名空間支援的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279919"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>使用應用程式閘道入口控制器在 AKS 群集中啟用多個命名空間支援

## <a name="motivation"></a>動機
庫伯內斯[命名空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)使 Kubernetes 群集得以分區並分配給較大團隊的子組。 然後，這些子團隊可以通過更精細的資源、安全性、配置等控制來部署和管理基礎結構。庫伯奈斯允許在每個命名空間內獨立定義一個或多個入口資源。

從版本 0.7 [Azure 應用程式閘道 Kubernets 入口控制器](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md)（AGIC） 中引入事件並觀察多個命名空間。 如果 AKS 管理員決定使用[應用閘道](https://azure.microsoft.com/services/application-gateway/)作為入口，則所有命名空間都將使用相同的應用程式閘道實例。 入口控制器的單個安裝將監視可訪問的命名空間，並將配置它與之關聯的應用程式閘道。

AGIC 的版本 0.7 將繼續專門觀察`default`命名空間，除非在 Helm 配置中顯式更改為一個或多個不同的命名空間（請參閱下面的部分）。

## <a name="enable-multiple-namespace-support"></a>啟用多個命名空間支援
要啟用多個命名空間支援：
1. 以下列方式之一修改[helm-config.yaml](#sample-helm-config-file)檔：
   - 完全從`watchNamespace`[掌舵-config.yaml](#sample-helm-config-file)中刪除金鑰 - AGIC 將觀察所有命名空間
   - 設置為`watchNamespace`空字串 - AGIC 將觀察所有命名空間
   - 添加用逗號分隔的多個命名空間`watchNamespace: default,secondNamespace`（ ） - AGIC 將專門觀察這些命名空間
2. 應用 Helm 範本更改：`helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

部署後能夠觀察多個命名空間，AGIC 將：
  - 列出來自所有可訪問命名空間的入口資源
  - 篩選器以入口資源進行帶`kubernetes.io/ingress.class: azure/application-gateway`
  - 組合[應用程式閘道配置](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - 通過[ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)將配置應用於關聯的應用程式閘道

## <a name="conflicting-configurations"></a>衝突配置
多個名稱節奏[的入口資源](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource)可以指示 AGIC 為單個應用程式閘道創建衝突配置。 （例如，兩個聲明同一域的入口。

在層次結構的頂部 -**攔截器**（IP 位址、埠和主機）和**路由規則**（綁定攔截器、後端池和 HTTP 設置）可以由多個命名空間/入口創建和共用。

另一方面 ， 路徑、後端池、HTTP 設置和 TLS 證書只能由一個命名空間創建，並且重複項將被刪除。

例如，請考慮定義命名空間和`staging``production`的以下重複入口資源： `www.contoso.com`
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

儘管兩個入口資源要求將流量`www.contoso.com`路由到相應的 Kubernetes 命名空間，但只有一個後端可以為流量提供服務。 AGIC 將在"先到先得"的基礎上為其中一個資源創建配置。 如果同時創建兩個入口資源，則字母表中較早的一個將優先。 從上面的示例中，我們只能為`production`入口創建設置。 應用程式閘道將配置以下資源：

  - 聽眾：`fl-www.contoso.com-80`
  - 路由規則：`rr-www.contoso.com-80`
  - 後端池：`pool-production-contoso-web-service-80-bp-80`
  - HTTP 設置：`bp-production-contoso-web-service-80-80-websocket-ingress`
  - 健康探測：`pb-production-contoso-web-service-80-websocket-ingress`

請注意，除了*攔截器*和*路由規則*之外，創建的應用程式閘道資源包括為其創建它們的命名空間`production`（） 的名稱。

如果兩個入口資源在不同時間點引入 AKS 群集，則 AGIC 可能會最終處於一個方案，即它重新配置應用程式閘道並將流量從`namespace-B`重新路由到`namespace-A`。

例如，如果首先添加`staging`，AGIC 將配置應用程式閘道以將流量路由到暫存後端池。 在稍後階段，引入`production`入口將導致 AGIC 重新程式設計應用程式閘道，這將開始將流量路由到`production`後端池。

## <a name="restrict-access-to-namespaces"></a>限制對命名空間的訪問
預設情況下，AGIC 將根據任何命名空間中的帶名入口配置應用程式閘道。 如果要限制此行為，您有以下選項：
  - 限制命名空間，通過顯式定義命名空間 AGIC 應通過`watchNamespace`[helm-config.yaml](#sample-helm-config-file)中的 YAML 鍵進行觀察
  - 使用[角色/角色綁定](https://docs.microsoft.com/azure/aks/azure-ad-rbac)將 AGIC 限制為特定命名空間

## <a name="sample-helm-config-file"></a>示例頭盔設定檔
```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

