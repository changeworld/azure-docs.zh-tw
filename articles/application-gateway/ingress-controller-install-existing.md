---
title: 使用現有應用程式閘道建立閘道控制器
description: 本文提供有關如何使用現有應用程式閘道部署應用程式閘道入口控制器的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 949f1b3ee3db72e1c541c3dd4c5f74f364f1b514
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869885"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>使用現有應用程式閘道安裝應用程式閘道閘道 (AGIC)

應用程式閘道閘道控制器 (AGIC) 是庫伯內斯群集中的窗格。
AGIC 監視庫伯內斯[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)資源,並根據庫伯內斯群集的狀態創建和應用應用程式閘道配置。

## <a name="outline"></a>大綱:
- [先決條件](#prerequisites)
- [Azure 資源管理員認證 (ARM)](#azure-resource-manager-authentication)
    - 選項 1:[設定 aad-pod 識別](#set-up-aad-pod-identity)並在 ARM 上建立 Azure 識別
    - 選項 2:[使用服務主體](#using-a-service-principal)
- [用頭盔安裝入口控制器](#install-ingress-controller-as-a-helm-chart)
- [多群集/共用應用程式閘道](#multi-cluster--shared-application-gateway):在一個環境中安裝 AGIC,其中應用程式閘道在一個或多個 AKS 群集和/或其他 Azure 元件之間共用。

## <a name="prerequisites"></a>Prerequisites
此文件的檔案的檔案的項目是安裝以下工具與基礎結構:
- 開啟[進階網路](https://docs.microsoft.com/azure/aks/configure-azure-cni)的[AKS](https://azure.microsoft.com/services/kubernetes-service/)
- 與 AKS 位於同一虛擬網路中[的應用程式閘道 v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant)
- [AKS](https://github.com/Azure/aad-pod-identity)叢集安裝的 AAD Pod 識別
- [雲外殼](https://shell.azure.com/)是 Azure 外殼`az`環境,具有`kubectl``helm`CLI, 並安裝了 。 這些工具是以下命令所必需的。

請在安裝 AGIC 之前__備份應用程式閘道的設定__:
  1. 使用[Azure 門戶](https://portal.azure.com/)瀏覽`Application Gateway`到實體
  2. 從`Export template`點擊`Download`

您下載的 zip 檔案將具有 JSON 樣本、bash 和 PowerShell 文稿,如果需要,您可以使用這些文本來還原應用閘道

## <a name="install-helm"></a>安裝 Helm
[赫爾姆](https://docs.microsoft.com/azure/aks/kubernetes-helm)是庫伯內斯的包經理。 我們將利用它來安裝`application-gateway-kubernetes-ingress`套件。
使用[雲外殼](https://shell.azure.com/)安裝頭盔:

1. 安裝[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm)並執行以下`application-gateway-kubernetes-ingress`內容以 新增掌舵包:

    - *已開啟 RBAC*AKS 叢集

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *RBAC 已關閉*AKS 叢集

    ```bash
    helm init
    ```

1. 新增 AGIC Helm 存放庫：
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Azure 資源管理員認證

AGIC 與庫伯奈斯 API 伺服器和 Azure 資源管理器通信。 它需要一個標識來訪問這些 API。

## <a name="set-up-aad-pod-identity"></a>設定 AAD Pod 識別

[AAD Pod 識別](https://github.com/Azure/aad-pod-identity)是一個控制器,類似於 AGIC,該控制器也運行在您的 AKS 上。 它將 Azure 活動目錄標識綁定到庫伯內斯窗格。 Kubernetes 窗格中的應用程式需要標識才能與其他 Azure 元件通信。 在此特定情況下,我們需要 AGICpod 的授權才能向[ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)發出 HTTP 請求。

按照[AAD Pod 識別安裝說明](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra)將此元件添加到 AKS。

接下來,我們需要創建一個 Azure 標識,並授予它 ARM 許可權。
使用[雲命令](https://shell.azure.com/)執行以下所有指令並建立識別碼:

1. 在**與 AKS 節點相同的資源組中**創建 Azure 標識。 選擇正確的資源組非常重要。 下面命令中所需的資源組*不是*AKS 門戶窗格中引用的資源組。 這是虛擬機的資源`aks-agentpool`組。 通常,該資源組以`MC_`AKS 開頭並包含 AKS 的名稱。 例如:`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. 對於下面的角色分配命令,我們需要為新創建的標識`principalId`獲取:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. 授予對應用程式`Contributor`閘道的標識存取許可權。 為此,您需要應用程式閘道的 ID,如下所示:`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    取得訂閱中的應用程式閘道 ID 清單,包括:`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. 授予對應用程式`Reader`閘道資源組的標識存取許可權。 資源群組識別碼 : `/subscriptions/A/resourceGroups/B` 您可以取得所有的資源群組:`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>使用服務主體
還可以通過庫伯內斯機密提供對 ARM 的 AGIC 訪問許可權。

1. 創建活動目錄服務主體,並使用 base64 進行編碼。 將 JSON blob 保存到庫伯內斯需要 base64 編碼。

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. 將基64編碼的 JSON`helm-config.yaml`blob 添加到檔中。 More information `helm-config.yaml` on is in the next section.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>將入口控制器安裝為頭盔圖
在前幾個步驟中,我們會在庫伯內斯群集上安裝赫爾姆的蒂勒。 使用[雲外殼](https://shell.azure.com/)安裝 AGIC 頭盔包:

1. 新增`application-gateway-kubernetes-ingress`掌舵回購並執行掌舵更新

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. 下載掌舵配置.yaml,這將配置AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    或複製下列的 YAML 檔: 
    
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. 編輯 helm-config.yaml 並`appgw`填`armAuth`寫和的值。
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > 和`<identity-resource-id>``<identity-client-id>`是上一節中設置的 Azure AD 標識的屬性。 可以通過執行以下命令來檢索此資訊`az identity show -g <resourcegroup> -n <identity-name>`: ,`<resourcegroup>`在其中 部署頂級 AKS 群集物件、應用程式閘道和託管標識的資源組位於何處。

1. 使用前一`application-gateway-kubernetes-ingress``helm-config.yaml`步的設定安裝 Helm 圖表

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    或者,您可以在一個步驟`helm-config.yaml`中組合 和 Helm 命令:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. 檢查新建立的窗格的紀錄,以驗證其啟動是否正確

請參閱[此操作指南](ingress-controller-expose-service-over-http-https.md),瞭解如何使用 Azure 應用程式閘道通過 HTTP 或 HTTPS 向 Internet 公開 AKS 服務。



## <a name="multi-cluster--shared-application-gateway"></a>多群集/共用應用程式閘道
默認情況下,AGIC 假定它連結到的應用程式閘道完全擁有。 AGIC 版本 0.8.0 及更高版本可以與其他 Azure 元件共用單個應用程式閘道。 例如,我們可以對虛擬機器規模集和 AKS 群集上託管的應用使用相同的應用程式閘道。

請在開啟這個設定之前__備份應用程式閘道的設定__:
  1. 使用[Azure 門戶](https://portal.azure.com/)瀏覽`Application Gateway`到實體
  2. 從`Export template`點擊`Download`

您下載的 zip 檔案將具有可用於回復應用程式閘道的 JSON 樣本、bash 和 PowerShell 文稿

### <a name="example-scenario"></a>範例案例
讓我們來看看一個假想的應用程式閘道,它管理兩個網站的流量:
  - `dev.contoso.com`- 使用應用程式閘道與 AGIC 託管在新的 AKS 上
  - `prod.contoso.com`- 託管在[Azure 虛擬機器縮放集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

使用預設設定時,AGIC 假定它所指向的應用程式閘道擁有 100% 的擁有權。 AGIC 覆蓋應用閘道的所有配置。 如果我們手動為`prod.contoso.com`(在應用程式閘道上)創建偵聽器,而不在 Kubernetes 入口中定義它,AGIC 將在數`prod.contoso.com`秒內刪除配置。

要安裝 AGIC,`prod.contoso.com`並且 也從我們的虛擬機器規模集電腦提供服務,我們必須將`dev.contoso.com`AGIC 限制為僅配置。 透過實體化以下[CRD,](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)可以促進這一點:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

上面的命令創建一個`AzureIngressProhibitedTarget`物件。 這使得 AGIC(版本 0.8.0 及更高版本)意識到應用程式閘道配置`prod.contoso.com`的存在, 並明確指示它避免更改與該主機名相關的任何配置。


### <a name="enable-with-new-agic-installation"></a>透過新的 AGIC 安裝啟用
將 AGIC(版本 0.8.0 及更高版本)限制為應用程式閘道配置`helm-config.yaml`的子集,請修改範本。
在「`appgw:`部份」`shared`下 ,新增鍵並將`true`其設定為 。

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

套用「頭盔」更改:
  1. 確保`AzureIngressProhibitedTarget`CRD 已安裝:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. 更新頭盔:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

因此,您的 AKS 將有一`AzureIngressProhibitedTarget``prohibit-all-targets`個稱為 的新實例:
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

顧名`prohibit-all-targets`思義,該物件禁止 AGIC 更改*任何*主機和路徑的配置。
Helm`appgw.shared=true`安裝 將部署 AGIC,但不會對應用程式閘道進行任何更改。


### <a name="broaden-permissions"></a>擴大權限
由於`appgw.shared=true`Helm`prohibit-all-targets`和 預設值阻止 AGIC 應用任何配置。

通過:
1. 使用特定設定`AzureIngressProhibitedTarget`建立新設定:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. 只有在創建自己的自定義禁止後,才能刪除預設禁止,該禁令過於寬泛:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>為現有的 AGIC 安裝啟用
假設我們的群集中已有一個工作的 AKS、應用程式閘道和配置的 AGIC。 我們有一個入口,`prod.contosor.com`並成功地服務於它的流量從AKS。 我們希望添加到`staging.contoso.com`現有的應用程式閘道,但需要將其託管在[VM](https://azure.microsoft.com/services/virtual-machines/)上。 我們將重用現有的應用程式閘道,並手動配置的`staging.contoso.com`偵聽器和後端池。 但是手動調整應用程式閘道配置(透過[門戶](https://portal.azure.com)[、ARM API](https://docs.microsoft.com/rest/api/resources/)或[Terraform)](https://www.terraform.io/)將與AGIC完全擁有權的假設相衝突。 在我們應用更改后不久,AGIC 將覆蓋或刪除它們。

我們可以禁止 AGIC 對配置子集進行更改。

1. 建立`AzureIngressProhibitedTarget`物件:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. 檢視新建立的物件:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. 通過門戶修改應用程式閘道配置 - 添加偵聽器、路由規則、後端等。我們創建的新物件(`manually-configured-staging-environment`) 將禁止 AGIC`staging.contoso.com`覆蓋與的應用程式閘道配置相關的。
