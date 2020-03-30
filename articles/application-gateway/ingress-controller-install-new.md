---
title: 使用新的應用程式閘道創建入口控制器
description: 本文提供有關如何使用新的應用程式閘道部署應用程式閘道入口控制器的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a0bc6aef1becd53217be0eeb8c865b5c78a5d69f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239471"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>如何使用新的應用程式閘道安裝應用程式閘道入口控制器 （AGIC）

以下說明假定應用程式閘道入口控制器 （AGIC） 將安裝在沒有預先存在的元件的環境中。

## <a name="required-command-line-tools"></a>所需的命令列工具

我們建議在以下所有命令列操作中使用[Azure 雲外殼](https://shell.azure.com/)。 從shell.azure.com啟動外殼，或通過按一下連結：

[![嵌入啟動](https://shell.azure.com/images/launchcloudshell.png "啟動 Azure Cloud Shell")](https://shell.azure.com)

或者，使用以下圖示從 Azure 門戶啟動雲外殼：

![入口網站啟動](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

[Azure 雲外殼](https://shell.azure.com/)已具有所有必要的工具。 如果選擇使用其他環境，請確保安裝了以下命令列工具：

* `az`- Azure CLI：[安裝說明](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* `kubectl`- 庫伯內斯命令列工具：[安裝說明](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm`- 庫伯奈斯封裝管理器：[安裝說明](https://github.com/helm/helm/releases/latest)
* `jq`- 命令列 JSON 處理器：[安裝說明](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>創建標識

按照以下步驟創建 Azure 活動目錄 （AAD）[服務主體物件](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)。 請記錄`appId`、`password`和`objectId`值 - 這些將在以下步驟中使用。

1. 創建 AD 服務主體 （[閱讀更多有關 RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)的 ）：
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    JSON 輸出的`appId`和`password`值將在以下步驟中使用


1. `appId`使用上一個命令的輸出獲取新服務主體`objectId`的：
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    此命令的輸出為`objectId`，將在下面的 Azure 資源管理器範本中使用

1. 創建稍後將在 Azure 資源管理器範本部署中使用的參數檔。
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
    要部署啟用**RBAC**的群集，`aksEnabledRBAC`請將該欄位設置為`true`

## <a name="deploy-components"></a>部署元件
此步驟將添加以下元件到訂閱：

- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)
- [應用程式閘道](https://docs.microsoft.com/azure/application-gateway/overview)v2
- 帶 2 個[子網](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)的[虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [公用 IP 位址](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- [託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)，將由[AAD Pod 標識](https://github.com/Azure/aad-pod-identity/blob/master/README.md)使用

1. 下載 Azure 資源管理器範本並根據需要修改範本。
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. 使用`az cli`部署 Azure 資源管理器範本。 這可能需要長達 5 分鐘。
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

1. 部署完成後，將部署輸出下載到名為 的`deployment-outputs.json`檔中。
    ```azurecli
    az group deployment show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>設置應用程式閘道入口控制器

使用上一節中的說明，我們創建並配置了新的 AKS 群集和應用程式閘道。 現在，我們已準備好將示例應用和入口控制器部署到我們新的 Kubernetes 基礎結構。

### <a name="setup-kubernetes-credentials"></a>設置庫伯內斯憑據
對於以下步驟，我們需要設置[庫布ectl](https://kubectl.docs.kubernetes.io/)命令，我們將用它來連接到新的 Kubernetes 群集。 [雲外殼](https://shell.azure.com/)已`kubectl`安裝。 我們將使用`az`CLI 獲取庫伯內特的憑據。

獲取新部署的 AKS 的憑據 （[閱讀更多](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)）：
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>安裝 AAD Pod 標識
  Azure 活動目錄 Pod 標識提供對[Azure 資源管理器 （ARM）](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的基於權杖的訪問。

  [AAD Pod 標識](https://github.com/Azure/aad-pod-identity)將添加以下元件到庫伯內斯群集：
   * 庫伯內斯[CRD：](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) `AzureIdentity`， `AzureAssignedIdentity`， ，`AzureIdentityBinding`
   * [受控識別控制器 (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic) 元件
   * [節點受控識別 (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi) 元件


要將 AAD Pod 標識安裝到群集中，請執行：

   - *已啟用 RBAC*AKS 群集

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *RBAC 已禁用*AKS 群集

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>安裝 Helm
[赫爾姆](https://docs.microsoft.com/azure/aks/kubernetes-helm)是庫伯內斯的包經理。 我們將利用它來安裝`application-gateway-kubernetes-ingress`套裝軟體：

1. 安裝[Helm](https://docs.microsoft.com/azure/aks/kubernetes-helm)並運行以下內容以`application-gateway-kubernetes-ingress`添加掌舵包：

    - *已啟用 RBAC*AKS 群集

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *RBAC 已禁用*AKS 群集

        ```bash
        helm init
        ```

1. 新增 AGIC Helm 存放庫：
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>安裝輸入控制器 Helm 圖表

1. 使用上面`deployment-outputs.json`創建的檔並創建以下變數。
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. 下載掌舵配置.yaml，這將配置AGIC：
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    或複製下面的 YAML 檔： 
    
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

1. 編輯新下載的 helm-config.yaml 並填寫部分`appgw`和`armAuth`。
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
     - `verbosityLevel`：設置 AGIC 日誌記錄基礎結構的詳細程度。 如需可能的值，請參閱[記錄層級](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) \(英文\)。
     - `appgw.subscriptionId`：應用程式閘道所在的 Azure 訂閱 ID。 範例： `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`：在其中創建應用程式閘道的 Azure 資源組的名稱。 範例： `app-gw-resource-group`
     - `appgw.name`：應用程式閘道的名稱。 範例： `applicationgatewayd0f0`
     - `appgw.shared`： 此布林標誌應預設為`false`。 設置為`true`需要[共用應用程式閘道](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)時。
     - `kubernetes.watchNamespace`：指定 AGIC 應監視的名稱空間。 這可以是單個字串值，也可以是逗號分隔的命名空間清單。
    - `armAuth.type`： 可以是`aadPodIdentity`或`servicePrincipal`
    - `armAuth.identityResourceID`：Azure 託管標識的資源識別碼
    - `armAuth.identityClientId`：標識的用戶端 ID。 有關標識的詳細資訊，請參閱下文
    - `armAuth.secretJSON`：僅在選擇服務主體機密類型時`armAuth.type`（已設置為`servicePrincipal`） 時才需要 


   > [!NOTE]
   > 和`identityResourceID``identityClientID`是[創建標識](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-new.md#create-an-identity)步驟期間創建的值，可以使用以下命令再次獲取 這些值：
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>`在上面的命令中是應用程式閘道的資源組。 `<identity-name>`是創建標識的名稱。 可以使用下列功能列出給定訂閱的所有標識：`az identity list`


1. 安裝應用程式閘道輸入控制器套件：

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>安裝示例應用
現在，我們已經安裝了應用程式閘道、AKS 和 AGIC，我們可以通過[Azure 雲外殼](https://shell.azure.com/)安裝示例應用：

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

或者，您也可以：

* 下載上面的 YAML 檔：

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* 套用 YAML 檔案：

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>其他範例
本[操作指南](ingress-controller-expose-service-over-http-https.md)包含有關如何通過 HTTP 或 HTTPS 向具有應用程式閘道的 Internet 公開 AKS 服務的更多示例。
