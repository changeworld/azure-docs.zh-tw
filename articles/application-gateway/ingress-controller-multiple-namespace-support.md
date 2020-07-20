---
title: 啟用應用程式閘道輸入控制器的多個命名空間支援
description: 本文提供有關如何在具有應用程式閘道輸入控制器的 Kubernetes 叢集中啟用多個命名空間支援的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 953430421bd30aaa1df352451b549994aeaa1a70
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85556154"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>在具有應用程式閘道輸入控制器的 AKS 叢集中啟用多個命名空間支援

## <a name="motivation"></a>動機
Kubernetes[命名空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)可以分割 Kubernetes 叢集，並將其配置給較大型小組的子群組。 這些子小組接著可以透過更精細的資源、安全性、設定等控制來部署和管理基礎結構。Kubernetes 可讓您在每個命名空間內獨立定義一或多個輸入資源。

從0.7 版開始[Azure 應用程式閘道 Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) （AGIC）可以內嵌事件，並觀察多個命名空間。 如果 AKS 系統管理員決定使用[應用程式閘道](https://azure.microsoft.com/services/application-gateway/)做為輸入，則所有命名空間都會使用相同的應用程式閘道實例。 輸入控制器的單一安裝會監視可存取的命名空間，並設定與它相關聯的應用程式閘道。

0.7 版的 AGIC 會繼續以獨佔方式觀察 `default` 命名空間，除非這會明確變更為 Helm 設定中的一或多個不同的命名空間（請參閱下一節）。

## <a name="enable-multiple-namespace-support"></a>啟用多個命名空間支援
若要啟用多個命名空間支援：
1. 以下列其中一種方式修改[helm-config yaml](#sample-helm-config-file)檔案：
   - `watchNamespace`完全從[helm-config 刪除金鑰。 yaml](#sample-helm-config-file) -AGIC 將會觀察所有的命名空間
   - 設定 `watchNamespace` 為空字串-AGIC 會觀察所有的命名空間
   - 新增多個以逗號分隔的命名空間（ `watchNamespace: default,secondNamespace` ）-AGIC 會以獨佔方式觀察這些命名空間
2. 套用 Helm 範本變更：`helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

一旦部署並具有觀察多個命名空間的功能之後，AGIC 將會：
  - 列出所有可存取的命名空間中的輸入資源
  - 篩選至以標注的輸入資源`kubernetes.io/ingress.class: azure/application-gateway`
  - 撰寫結合[應用程式閘道 config](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - 透過[ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)將設定套用至相關聯的應用程式閘道

## <a name="conflicting-configurations"></a>衝突的設定
多個命名空間輸入[資源](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource)可能會指示 AGIC 建立單一應用程式閘道的衝突設定。 （實例有兩個宣告相同網域的會輸入）。

階層的頂端-接聽**程式（IP**位址、埠和主機）和**路由規則**（系結接聽程式、後端集區和 HTTP 設定）可由多個命名空間/會輸入建立及共用。

另一方面，只有一個命名空間可以建立後端集區、HTTP 設定和 TLS 憑證，而且將會移除重複的專案。

例如，請考慮下列重複的輸入資源定義的命名空間 `staging` 和 `production` `www.contoso.com` ：

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

雖然兩個輸入資源會要求將流量 `www.contoso.com` 路由傳送至各自的 Kubernetes 命名空間，但是只有一個後端可以服務流量。 AGIC 會針對其中一個資源，在「第一次提供」的基礎上建立設定。 如果同時建立兩個會輸入資源，則會優先使用字母中較早的一項。 在上述範例中，我們只能夠建立輸入的設定 `production` 。 將會使用下列資源來設定應用程式閘道：

  - 聽眾`fl-www.contoso.com-80`
  - 路由規則：`rr-www.contoso.com-80`
  - 後端集區：`pool-production-contoso-web-service-80-bp-80`
  - HTTP 設定：`bp-production-contoso-web-service-80-80-websocket-ingress`
  - 健康情況探查：`pb-production-contoso-web-service-80-websocket-ingress`

請注意，除了接聽*程式和**路由規則*以外，所建立的應用程式閘道資源會包含所建立之命名空間（）的名稱 `production` 。

如果這兩個輸入資源在不同的時間點引進 AKS 叢集，則 AGIC 可能會在其重新設定應用程式閘道，並將流量從重新路由至的情況下結束 `namespace-B` `namespace-A` 。

例如，如果您 `staging` 先新增，AGIC 會設定應用程式閘道以將流量路由傳送至暫存後端集區。 在稍後的階段中，引入輸入 `production` 會導致 AGIC mk-reprogram 應用程式閘道，這會開始將流量路由至 `production` 後端集區。

## <a name="restrict-access-to-namespaces"></a>限制對命名空間的存取
根據預設，AGIC 會根據任何命名空間內標注的輸入來設定應用程式閘道。 如果您想要限制這種行為，您可以使用下列選項：
  - 藉由明確定義命名空間來限制命名空間，AGIC 應該透過 `watchNamespace` helm 中的 YAML 索引鍵來觀察[YAML](#sample-helm-config-file)
  - 使用[Role/接著](https://docs.microsoft.com/azure/aks/azure-ad-rbac)將 AGIC 限制為特定的命名空間

## <a name="sample-helm-config-file"></a>Helm 設定檔範例

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

