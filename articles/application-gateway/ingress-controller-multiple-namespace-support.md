---
title: 針對應用程式閘道輸入控制器啟用多個命名空間支援
description: 本文提供如何在具有應用程式閘道輸入控制器的 Kubernetes 叢集中啟用多個命名空間支援的相關資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: c13c4410852d97f0bf4548578f40a5cc560804d7
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874588"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>在具有應用程式閘道輸入控制器的 AKS 叢集中啟用多個命名空間支援

## <a name="motivation"></a>動機
Kubernetes [命名空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) 可讓 Kubernetes 叢集進行分割，並配置給較大小組的子群組。 然後，這些子小組可以透過更細微的資源、安全性、設定等控制項來部署和管理基礎結構。Kubernetes 可讓您在每個命名空間內個別定義一或多個輸入資源。

從0.7 版開始 [Azure 應用程式閘道 Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) 可以從內嵌事件並觀察多個命名空間。 如果 AKS 系統管理員決定使用 [應用程式閘道](https://azure.microsoft.com/services/application-gateway/) 作為輸入，則所有命名空間會使用相同的應用程式閘道實例。 單一安裝輸入控制器將會監視可存取的命名空間，並設定與其相關聯的應用程式閘道。

`default`除非在 Helm 設定中明確變更為一或多個不同的命名空間，否則 AGIC 的0.7 版將繼續以獨佔方式觀察命名空間 (請參閱下一節) 。

## <a name="enable-multiple-namespace-support"></a>啟用多個命名空間支援
若要啟用多個命名空間支援：
1. 以下列其中一種方式修改 [helm yaml](#sample-helm-config-file) 檔案：
   - `watchNamespace`從 helm 完全刪除金鑰[。 yaml](#sample-helm-config-file) -AGIC 將會觀察到所有命名空間
   - 設定 `watchNamespace` 為空字串時，AGIC 會觀察到所有命名空間
   - 新增多個以逗號分隔的命名空間 (`watchNamespace: default,secondNamespace`) AGIC 將會以獨佔方式觀察這些命名空間
2. 將 Helm 範本變更套用至： `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

一旦部署了觀察多個命名空間的功能，AGIC 將會：
  - 列出所有可存取的命名空間的輸入資源
  - 篩選至標注的輸入資源 `kubernetes.io/ingress.class: azure/application-gateway`
  - 撰寫組合的[應用程式閘道](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)設定
  - 透過[ARM](../azure-resource-manager/management/overview.md)將設定套用至相關聯的應用程式閘道

## <a name="conflicting-configurations"></a>衝突的設定
多個命名空間輸入 [資源](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) 可以指示 AGIC 建立單一應用程式閘道的衝突設定。  (兩個宣告相同網域的 ingresses。 ) 

階層 **的頂端 (IP** 位址、埠和主機) 和 **路由規則** (系結接聽程式、後端集區和 HTTP 設定) 可由多個命名空間/ingresses 建立及共用。

另一方面，您只能使用一個命名空間來建立後端集區、HTTP 設定和 TLS 憑證，而將會移除重複的專案。

例如，請考慮下列重複的輸入資源定義命名 `staging` 空間 `production` 和 `www.contoso.com` ：

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

儘管兩個輸入資源要求流量 `www.contoso.com` 路由傳送至個別的 Kubernetes 命名空間，但只有一個後端可以服務流量。 AGIC 會針對其中一個資源，在「第一次提供，第一次服務」的基礎上建立設定。 如果同時建立兩個 ingresses 資源，則字母之前的資源將會優先使用。 從上述範例中，我們只能建立輸入的設定 `production` 。 應用程式閘道將會使用下列資源進行設定：

  - 聽眾： `fl-www.contoso.com-80`
  - 路由規則： `rr-www.contoso.com-80`
  - 後端集區： `pool-production-contoso-web-service-80-bp-80`
  - HTTP 設定： `bp-production-contoso-web-service-80-80-websocket-ingress`
  - 健康情況探查： `pb-production-contoso-web-service-80-websocket-ingress`

請注意，除了接聽 *程式和**路由規則* 之外，所建立的應用程式閘道資源還包含建立命名空間 (`production`) 名稱。

如果在不同時間點將這兩個輸入資源引進 AKS 叢集中，AGIC 可能會在將應用程式閘道重新設定，並將流量重新路由傳送至的情況下結束 `namespace-B` `namespace-A` 。

例如，如果您 `staging` 先新增，AGIC 會將應用程式閘道設定為將流量路由傳送至暫存後端集區。 在稍後的階段中，引入輸入 `production` 將會導致 AGIC Reprogram 應用程式閘道，這會開始將流量路由至 `production` 後端集區。

## <a name="restrict-access-to-namespaces"></a>限制存取命名空間
根據預設，AGIC 會根據任何命名空間內的標注輸入來設定應用程式閘道。 如果您想要限制這項行為，您可以選擇下列選項：
  - 藉由明確定義命名空間來限制命名空間，AGIC 應該透過 `watchNamespace` [helm-config](#sample-helm-config-file)中的 YAML 索引鍵來觀察。 YAML
  - 使用 [角色/RoleBinding](../aks/azure-ad-rbac.md) 限制特定命名空間的 AGIC

## <a name="sample-helm-config-file"></a>範例 Helm 設定檔

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
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
```