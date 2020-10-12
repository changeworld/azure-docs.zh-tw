---
title: 使用新的應用程式閘道建立輸入控制器
description: 本文提供如何使用新的應用程式閘道部署應用程式閘道輸入控制器的相關資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: cbebf430bf44ccdee51bf44b11b8b01f23544dcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84807148"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>如何使用新的應用程式閘道 (AGIC) 安裝應用程式閘道輸入控制器

下列指示假設應用程式閘道輸入控制器 (AGIC) 將安裝在沒有任何預先存在元件的環境中。

## <a name="required-command-line-tools"></a>必要的命令列工具

建議您在下列所有命令列作業中使用 [Azure Cloud Shell](https://shell.azure.com/) 。 從 shell.azure.com 啟動您的 shell，或按一下連結：

[![內嵌啟動](https://shell.azure.com/images/launchcloudshell.png "啟動 Azure Cloud Shell")](https://shell.azure.com)

或者，使用下列圖示從 Azure 入口網站啟動 Cloud Shell：

![入口網站啟動](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

您的 [Azure Cloud Shell](https://shell.azure.com/) 已經有所有必要的工具。 如果您選擇使用其他環境，請確定已安裝下列命令列工具：

* `az` -Azure CLI： [安裝指示](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl` -Kubernetes 命令列工具： [安裝指示](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` -Kubernetes 套件管理員： [安裝指示](https://github.com/helm/helm/releases/latest)
* `jq` -命令列 JSON 處理器： [安裝指示](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>建立身分識別

遵循下列步驟來建立 Azure Active Directory (AAD) [服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 請記錄 `appId` 、 `password` 和值， `objectId` 這些會在下列步驟中使用。

1. 建立 AD 服務主體 ([閱讀有關 RBAC) 的詳細資訊](https://docs.microsoft.com/azure/role-based-access-control/overview) ：
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    在 `appId` `password` 下列步驟中，會使用 JSON 輸出的和值


1. 使用 `appId` 上一個命令輸出中的來取得 `objectId` 新服務主體的：
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    此命令的輸出是 `objectId` ，將用於下列 Azure Resource Manager 範本

1. 建立稍後將用於 Azure Resource Manager 範本部署的參數檔案。
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    若要部署已啟用 **RBAC** 的叢集，請將 `aksEnableRBAC` 欄位設定為 `true`

## <a name="deploy-components"></a>部署元件
此步驟會將下列元件新增至您的訂用帳戶：

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [應用程式閘道](https://docs.microsoft.com/azure/application-gateway/overview) v2
- 具有 2[個子網](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)的[虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)，將由[AAD Pod 身分識別](https://github.com/Azure/aad-pod-identity/blob/master/README.md)使用

1. 視需要下載 Azure Resource Manager 範本和修改範本。
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. 使用部署 Azure Resource Manager 範本 `az cli` 。 這最多可能需要5分鐘的時間。
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az group deployment create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. 部署完成後，請將部署輸出下載到名為的檔案中 `deployment-outputs.json` 。
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>設定應用程式閘道輸入控制器

使用上一節中的指示，我們已建立並設定新的 AKS 叢集和應用程式閘道。 我們現在已準備好將範例應用程式和輸入控制器部署到新的 Kubernetes 基礎結構。

### <a name="setup-kubernetes-credentials"></a>設定 Kubernetes 認證
在下列步驟中，我們需要安裝 [kubectl](https://kubectl.docs.kubernetes.io/) 命令，我們將用它來連接到新的 Kubernetes 叢集。 [Cloud Shell](https://shell.azure.com/) 已 `kubectl` 安裝。 我們將使用 `az` CLI 來取得 Kubernetes 的認證。

取得新部署之 AKS 的認證 ([閱讀更多](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)) ：
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>安裝 AAD Pod 身分識別
  Azure Active Directory Pod 身分識別會提供對 [Azure Resource Manager (ARM) ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的權杖型存取。

  [AAD Pod 身分識別](https://github.com/Azure/aad-pod-identity) 會將下列元件新增至您的 Kubernetes 叢集中：
   * Kubernetes [CRD](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/)：`AzureIdentity`、`AzureAssignedIdentity`、`AzureIdentityBinding`
   * [受控識別控制器 (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) 元件
   * [節點受控識別 (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) 元件


若要將 AAD Pod 身分識別安裝到您的叢集：

   - *已啟用 RBAC* AKS 叢集

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *已停用 RBAC* AKS 叢集

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>安裝 Helm
[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) 是 Kubernetes 的套件管理員。 我們將利用它來安裝 `application-gateway-kubernetes-ingress` 套件：

1. 安裝 [Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm) ，並執行下列步驟以新增 `application-gateway-kubernetes-ingress` Helm 套件：

    - *已啟用 RBAC* AKS 叢集

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *已停用 RBAC* AKS 叢集

        ```bash
        helm init
        ```

1. 新增 AGIC Helm 存放庫：
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>安裝輸入控制器 Helm 圖表

1. 使用 `deployment-outputs.json` 上面建立的檔案，並建立下列變數。
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. 下載 helm-yaml，它會設定 AGIC：
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    或複製以下的 YAML 檔案： 
    
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

1. 編輯新下載的 helm yaml，並填寫章節 `appgw` 和 `armAuth` 。
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   值：
     - `verbosityLevel`:設定 AGIC 記錄基礎結構的詳細資訊層級。 如需可能的值，請參閱[記錄層級](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) \(英文\)。
     - `appgw.subscriptionId`：應用程式閘道所在的 Azure 訂用帳戶識別碼。 範例： `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`：建立應用程式閘道的 Azure 資源組名。 範例： `app-gw-resource-group`
     - `appgw.name`:應用程式閘道的名稱。 範例： `applicationgatewayd0f0`
     - `appgw.shared`:此布林值旗標應該預設為 `false`。 如果 `true` 您需要 [共用的應用程式閘道](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)，請將設定為。
     - `kubernetes.watchNamespace`:指定 AGIC 應該監看的命名空間。 這可以是單一字串值，也可以是以逗號分隔的命名空間清單。
    - `armAuth.type`：可能是 `aadPodIdentity` 或 `servicePrincipal`
    - `armAuth.identityResourceID`： Azure 受控識別的資源識別碼
    - `armAuth.identityClientId`:身分識別的用戶端識別碼。 如需有關身分識別的詳細資訊，請參閱下文
    - `armAuth.secretJSON`：只有在選擇服務主體秘密類型時，才需要 (當 `armAuth.type` 設定為 `servicePrincipal`)  


   > [!NOTE]
   > `identityResourceID`和 `identityClientID` 是在[部署元件](ingress-controller-install-new.md#deploy-components)步驟期間建立的值，可以使用下列命令再次取得：
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>` 在上述命令中，是您應用程式閘道的資源群組。 `<identity-name>` 這是所建立之身分識別的名稱。 您可以使用下列內容列出指定訂用帳戶的所有身分識別： `az identity list`


1. 安裝應用程式閘道輸入控制器套件：

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>安裝範例應用程式
既然我們已安裝應用程式閘道、AKS 和 AGIC，我們就可以透過 [Azure Cloud Shell](https://shell.azure.com/)安裝範例應用程式：

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

或者，您可以：

* 下載上述的 YAML 檔案：

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* 套用 YAML 檔案：

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>其他範例
本操作 [指南](ingress-controller-expose-service-over-http-https.md) 包含更多有關如何透過 HTTP 或 HTTPS 向網際網路透過應用程式閘道公開 AKS 服務的範例。
