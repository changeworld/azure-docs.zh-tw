---
title: 建立具有現有應用程式閘道的輸入控制器
description: 本文提供如何使用現有應用程式閘道部署應用程式閘道輸入控制器的相關資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 0652c49acf58a52244cc27ae3e59120ac7f03858
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807092"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>使用現有的應用程式閘道安裝應用程式閘道輸入控制器（AGIC）

應用程式閘道輸入控制器（AGIC）是 Kubernetes 叢集中的 pod。
AGIC 會[監視 Kubernetes 輸入](https://kubernetes.io/docs/concepts/services-networking/ingress/)資源，並根據 Kubernetes 叢集的狀態建立並套用應用程式閘道 config。

## <a name="outline"></a>多級
- [先決條件](#prerequisites)
- [Azure Resource Manager Authentication （ARM）](#azure-resource-manager-authentication)
    - 選項1：[設定 aad-pod-身分識別](#set-up-aad-pod-identity)並在 arm 上建立 Azure 身分識別
    - 選項2：[使用服務主體](#using-a-service-principal)
- [使用 Helm 安裝輸入控制器](#install-ingress-controller-as-a-helm-chart)
- [多叢集/共用應用程式閘道](#multi-cluster--shared-application-gateway)：在環境中安裝 AGIC，其中應用程式閘道會在一或多個 AKS 叢集和（或）其他 Azure 元件之間共用。

## <a name="prerequisites"></a>必要條件
本檔假設您已安裝下列工具和基礎結構：
- 已啟用[Advanced 網路](https://docs.microsoft.com/azure/aks/configure-azure-cni)的[AKS](https://azure.microsoft.com/services/kubernetes-service/)
- 在與 AKS 相同的虛擬網路中[應用程式閘道 v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant)
- 安裝在 AKS 叢集上的[AAD Pod 身分識別](https://github.com/Azure/aad-pod-identity)
- [Cloud Shell](https://shell.azure.com/)是 Azure Shell 環境，其已 `az` 安裝 CLI、 `kubectl` 和 `helm` 。 下列命令需要這些工具。

安裝 AGIC 之前，請先__備份您的應用程式閘道__設定：
  1. 使用[Azure 入口網站](https://portal.azure.com/)流覽至您的 `Application Gateway` 實例
  2. 從 `Export template` 按一下`Download`

您下載的 zip 檔案會有 JSON 範本、bash 和 PowerShell 腳本，您可以用來還原應用程式閘道，這是必要的

## <a name="install-helm"></a>安裝 Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm)是 Kubernetes 的套件管理員。 我們將利用它來安裝 `application-gateway-kubernetes-ingress` 封裝。
使用[Cloud Shell](https://shell.azure.com/)安裝 Helm：

1. 安裝[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm)並執行下列步驟，以新增 `application-gateway-kubernetes-ingress` Helm 套件：

    - *已啟用 RBAC*AKS 叢集

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *已停用 RBAC*AKS 叢集

    ```bash
    helm init
    ```

1. 新增 AGIC Helm 存放庫：
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Azure Resource Manager 驗證

AGIC 會與 Kubernetes API 伺服器和 Azure Resource Manager 通訊。 它需要身分識別才能存取這些 Api。

## <a name="set-up-aad-pod-identity"></a>設定 AAD Pod 身分識別

[AAD Pod 身分識別](https://github.com/Azure/aad-pod-identity)是一個控制器，類似于 AGIC，這也會在您的 AKS 上執行。 它會將 Azure Active Directory 身分識別系結至您的 Kubernetes pod。 Kubernetes pod 中的應用程式必須要有身分識別，才能夠與其他 Azure 元件通訊。 在這裡的特定情況下，我們需要 AGIC pod 的授權，才能對[ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)提出 HTTP 要求。

遵循[AAD Pod 身分識別安裝指示](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra)，將此元件新增至您的 AKS。

接下來，我們需要建立 Azure 身分識別並為其授與 ARM 許可權。
使用[Cloud Shell](https://shell.azure.com/)執行下列所有命令，並建立身分識別：

1. **在與 AKS 節點相同的資源群組中**建立 Azure 身分識別。 挑選正確的資源群組很重要。 下列命令中所需的資源群組，*不*是 AKS 入口網站窗格上所參考。 這是虛擬機器的資源群組 `aks-agentpool` 。 一般來說，資源群組會以開頭 `MC_` ，並包含您的 AKS 名稱。 例如：`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. 針對下面的角色指派命令，我們需要取得 `principalId` 新建立的身分識別：

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. 將身分識別 `Contributor` 存取權授與您的應用程式閘道。 為此，您需要應用程式閘道的識別碼，看起來會像這樣：`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    使用下列方式取得訂用帳戶中的應用程式閘道識別碼清單：`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. 將身分識別 `Reader` 存取權授與應用程式閘道資源群組。 資源群組識別碼看起來會像這樣： `/subscriptions/A/resourceGroups/B` 。 您可以使用下列方式取得所有資源群組：`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>使用服務主體
您也可以透過 Kubernetes 秘密提供對 ARM 的 AGIC 存取權。

1. 建立 Active Directory 服務主體，並使用 base64 進行編碼。 必須要有 base64 編碼，JSON blob 才會儲存至 Kubernetes。

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. 將 base64 編碼的 JSON blob 新增至檔案 `helm-config.yaml` 。 下一節將詳細說明 `helm-config.yaml` 。
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>安裝輸入控制器作為 Helm 圖
在前幾個步驟中，我們會將 Helm 的 Tiller 安裝在您的 Kubernetes 叢集中。 使用[Cloud Shell](https://shell.azure.com/)安裝 AGIC Helm 套件：

1. 新增 helm 存放庫 `application-gateway-kubernetes-ingress` 並執行 helm 更新

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. 下載 helm-yaml，這會設定 AGIC：
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    或複製下列 YAML 檔案： 
    
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

1. 編輯 helm yaml，並填入和的值 `appgw` `armAuth` 。
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>`和 `<identity-client-id>` 是您在上一節中設定之 Azure AD 身分識別的屬性。 您可以藉由執行下列命令來抓取這項資訊： `az identity show -g <resourcegroup> -n <identity-name>` ，其中 `<resourcegroup>` 是用來部署頂層 AKS 叢集物件應用程式閘道和受控識別的資源群組。

1. `application-gateway-kubernetes-ingress`使用 `helm-config.yaml` 上一個步驟中的設定安裝 Helm 圖表

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    或者，您可以 `helm-config.yaml` 在一個步驟中結合和 Helm 命令：
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

1. 檢查新建立之 pod 的記錄檔，以確認它是否正確啟動

請參閱[本操作指南](ingress-controller-expose-service-over-http-https.md)，以瞭解如何使用 Azure 應用程式閘道，透過 HTTP 或 HTTPS 將 AKS 服務公開至網際網路。



## <a name="multi-cluster--shared-application-gateway"></a>多叢集/共用應用程式閘道
根據預設，AGIC 會假設其所連結之應用程式閘道的完整擁有權。 AGIC version 0.8.0 版和更新版本可以與其他 Azure 元件共用單一應用程式閘道。 例如，針對虛擬機器擴展集和 AKS 叢集上裝載的應用程式，我們可以使用相同的應用程式閘道。

請先__備份您的應用程式閘道__設定，然後再啟用此設定：
  1. 使用[Azure 入口網站](https://portal.azure.com/)流覽至您的 `Application Gateway` 實例
  2. 從 `Export template` 按一下`Download`

您下載的 zip 檔案將具有可用於還原的 JSON 範本、bash 和 PowerShell 腳本應用程式閘道

### <a name="example-scenario"></a>範例案例
讓我們看一下一個虛構應用程式閘道，它會管理兩個網站的流量：
  - `dev.contoso.com`-裝載于新的 AKS 上，使用應用程式閘道和 AGIC
  - `prod.contoso.com`-裝載于[Azure 虛擬機器擴展集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)上

使用預設設定時，AGIC 會假設其指向的應用程式閘道100% 的擁有權。 AGIC 會覆寫所有應用程式閘道的設定。 如果我們要以手動方式建立的接聽程式 `prod.contoso.com` （在應用程式閘道上），而不在 Kubernetes 輸入中定義它，AGIC 會在 `prod.contoso.com` 數秒內刪除設定。

若要安裝 AGIC，也可 `prod.contoso.com` 從我們的虛擬機器擴展集機器提供服務，我們必須將 AGIC 限制為 `dev.contoso.com` 僅設定。 藉由具現化下列[.crd](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)來加速此動作：

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

上述命令會建立 `AzureIngressProhibitedTarget` 物件。 這讓 AGIC （版本0.8.0 版和更新版本）知道應用程式閘道 config 是否存在 `prod.contoso.com` ，並明確指示它避免變更與該主機名稱相關的任何設定。


### <a name="enable-with-new-agic-installation"></a>以新的 AGIC 安裝啟用
若要將 AGIC （version 0.8.0 版和更新版本）限制為應用程式閘道設定的子集，請修改 `helm-config.yaml` 範本。
在 `appgw:` 區段下新增 `shared` 金鑰，並將它設定為 `true` 。

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

套用 Helm 變更：
  1. 請確定 `AzureIngressProhibitedTarget` .crd 是與一起安裝：
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. 更新 Helm：
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

因此，您的 AKS 會有一個名為的新實例 `AzureIngressProhibitedTarget` `prohibit-all-targets` ：
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

`prohibit-all-targets`如其名所示，物件禁止 AGIC 變更*任何*主機和路徑的設定。
Helm install with `appgw.shared=true` 會部署 AGIC，但不會對應用程式閘道進行任何變更。


### <a name="broaden-permissions"></a>放寬許可權
由於使用 Helm， `appgw.shared=true` 且預設 `prohibit-all-targets` 區塊會 AGIC 套用任何設定。

使用下列方式擴大 AGIC 許可權：
1. 使用您的特定設定來建立新的 `AzureIngressProhibitedTarget` ：
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

2. 只有在您建立自己的自訂禁止之後，您可以刪除預設值，而這種情況太廣泛：

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>針對現有的 AGIC 安裝啟用
假設我們的叢集中已有運作中的 AKS、應用程式閘道和設定的 AGIC。 我們有的輸入 `prod.contosor.com` ，且已成功從 AKS 為其提供流量。 我們想要新增 `staging.contoso.com` 至現有的應用程式閘道，但必須將它裝載在[VM](https://azure.microsoft.com/services/virtual-machines/)上。 我們即將重複使用現有的應用程式閘道，並手動設定的接聽程式和後端集區 `staging.contoso.com` 。 但手動調整應用程式閘道 config （透過[入口網站](https://portal.azure.com)、 [ARM api](https://docs.microsoft.com/rest/api/resources/)或[Terraform](https://www.terraform.io/)）會與 AGIC 的完整擁有權假設發生衝突。 在我們套用變更之後，AGIC 將會覆寫或刪除它們。

我們可以禁止 AGIC 對設定子集進行變更。

1. 建立 `AzureIngressProhibitedTarget` 物件：
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

2. 查看新建立的物件：
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. 透過入口網站修改應用程式閘道 config-新增接聽程式、路由規則、後端等。我們建立的新物件（ `manually-configured-staging-environment` ）會禁止 AGIC 覆寫與相關的應用程式閘道設定 `staging.contoso.com` 。
