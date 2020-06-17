---
title: 整合 Azure Key Vault 與 Kubernetes
description: 在本教學課程中，您將使用密碼存放區 CSI (容器儲存體介面) 驅動程式，從 Azure Key Vault 存取及擷取祕密，然後掛接至 Kubernetes Pod 中。
author: taytran0
ms.author: t-trtr
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/04/2020
ms.openlocfilehash: e945a30ca1fcd62fdfccd16d4e853540dbf73d8a
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636931"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-secret-store-csi-driver-on-kubernetes"></a>教學課程：在 Kubernetes 上，為密碼存放區 CSI 驅動程式設定及執行 Azure Key Vault 提供者

在本教學課程中，您將使用密碼存放區 CSI (容器儲存體介面) 驅動程式，從 Azure Key Vault 存取及擷取祕密，然後掛接至 Kubernetes Pod 中。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立服務主體
> * 部署 Azure Kubernetes Service 叢集
> * 安裝 Helm 與密碼存放區 CSI 驅動程式
> * 建立 Azure Key Vault 及設定祕密
> * 建立您自己的 SecretProviderClass 物件
> * 使用從 Key Vault 掛接而來的祕密，部署您的 Pod

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

開始本教學課程之前，請先安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)。

## <a name="create-a-service-principal-or-use-managed-identities"></a>建立服務主體，或使用受控識別

若要使用受控識別，請直接跳至下一節。

建立服務主體，以控制可從您 Azure Key Vault 存取的資源內容。 此服務主體的存取範圍，取決於其獲派的角色。 此功能可讓您控制服務主體如何管理您的祕密。 在下列範例中，服務主體的名稱為 **contosoServicePrincipal**。

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
此作業會傳回一系列的金鑰/值組：

![映像](../media/kubernetes-key-vault-1.png)

複製該**應用程式識別碼**與**密碼**。 之後將會需要這些認證。



## <a name="deploy-an-azure-kubernetes-service-cluster-using-azure-cli"></a>使用 Azure CLI 部署 Azure Kubernetes Service 叢集

您無須使用 Azure Cloud Shell，將會使用安裝有 Azure CLI 的命令提示字元 (終端機)。 

請遵循這份[指南](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough)完成下列各節：**建立資源群組**、**建立 AKS 叢集**，以及**連線到叢集**。

**注意：** 若要使用 Pod 識別，而不使用服務主體。 建立 Kubernetes 叢集時，請務必啟用該識別，如下所示：

```azurecli
az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
```

1. 將[您的 PATH 環境變數設定](https://www.java.com/en/download/help/path.xml)為先前下載的 "kubectl.exe" 檔案。
1. 使用下列命令，檢查您的 Kubernetes 版本。 此命令會輸出用戶端與伺服器的版本。 用戶端版本是您安裝的 "kubectl.exe"，而伺服器版本則是叢集執行所在的 Azure Kubernetes Services。
    ```azurecli
    kubectl version
    ```
1. 請確定您的 Kubernetes 版本是 **v 1.16.0** 或更新版本。 此命令會同時升級 Kubernetes 叢集與節點集區。 這可能需要幾分鐘的執行時間。 在此範例中，資源群組為 **contosoResourceGroup**，Kubernetes 叢集為 **contosoAKSCluster**。
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. 使用下列命令，顯示先前所建立之 AKS 叢集的中繼資料。 複製 **principalId**、**clientId**、**subscriptionId**與 **nodeResourceGroup**。
    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    我們將輸出中的兩個參數反白顯示。
    
    ![影像](../media/kubernetes-key-vault-5.png) ![影像](../media/kubernetes-key-vault-6.png)
    
## <a name="install-helm-and-secrets-store-csi-driver"></a>安裝 Helm 與密碼存放區 CSI 驅動程式

您必須先安裝 [Helm](https://helm.sh/docs/intro/install/)，才能安裝密碼存放區 CSI 驅動程式。

[密碼存放區 CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) 驅動程式介面可讓您取得儲存在 Azure Key Vault 執行個體中的祕密內容，然後再使用驅動程式介面，將這些祕密內容掛接到 Kubernetes Pod 中。

1. 檢查 Helm 版本，確定其為 v3 或更新版本：
    ```azurecli
    helm version
    ```
1. 安裝密碼存放區 CSI 驅動程式及其 Azure Key Vault 提供者：
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-secrets"></a>建立 Azure Key Vault 並設定祕密

遵循這份[指南](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli)，建立您自己的 Key Vault 並設定您的祕密。

**注意：** 您無須使用 Azure Cloud Shell，也無須建立新的資源群組， 只要使用先前為 Kubernetes 叢集建立的資源群組即可。

## <a name="create-your-own-secretproviderclass-object"></a>建立您自己的 SecretProviderClass 物件

使用提供的[範本](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass.yaml)，建立您自己的自訂 SecretProviderClass 物件，以提供密碼存放區 CSI 驅動程式提供者專用的參數。 此物件將提供存取您 Key Vault 所需的身分識別。

使用所提供的範例 SecretProviderClass YAML 檔案。 您將填寫其中缺少的參數。 必要參數如下：

1.  **userAssignedIdentityID：** 服務主體的用戶端識別碼
1.  **keyvaultName：** Key Vault 的名稱
1.  **objects：** 此物件將包含所有您要掛接的祕密內容
    1.  **objectName：** 祕密內容的名稱
    1.  **objectType：** 物件類型 (祕密、金鑰、憑證)
1.  **resourceGroup：** 資源群組的名稱
1.  **subscriptionId：** Key Vault 的訂閱識別碼
1.  **tenantID：** Key Vault 的租用戶識別碼 (亦即目錄識別碼)

以下是更新後的範本，請將其下載為 .yaml 檔案，並填寫對應的必要欄位。 在此範例中，Key Vault 為 **contosoKeyVault5**，並有兩個祕密 **secret1** 與 **secret2**。

**注意：** 若要使用受控識別，欄位 **usePodIdentity** 必須為 **true**，並只在欄位 **userAssignedIdentityID** 中保留兩個引號 **""** 。 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If using a Service Principal, use the client id to specify which user assigned managed identity to use. If using a user assigned identity as the VM's managed identity, then specify the identity's client id. If empty, then defaults to use the system assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the above command will return the Client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the Key Vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the above command will displays the Key Vault metadata, which includes the subscription ID, resource group name, Key Vault 
    cloudName: ""                             # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name “contosoKeyVault5”
                                              #     the above command will display a list of secret names from your Key Vault
          objectType: secret                  # [REQUIRED] object types: secret, key or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the Key Vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the Key Vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the Key Vault
```
下列是 "az keyvault show --name contosoKeyVault5" 的主控台輸出，其中相關的中繼資料以反白顯示：

![映像](../media/kubernetes-key-vault-2.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>指派您的服務主體，或使用受控識別

### <a name="using-service-principal"></a>使用服務主體

使用服務主體。 必須授權給您的服務主體，允許其存取您的 Key Vault 及擷取祕密。 完成下列步驟，以指派「**讀取者**」角色，並授權給服務主體，允許其從您的 Key Vault「**取得**」祕密：

1. 將服務主體指派給現有的 Key Vault。 **$AZURE_CLIENT_ID** 參數是您在建立服務主體之後複製的**應用程式識別碼**。
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    以下是命令的輸出： 

    ![映像](../media/kubernetes-key-vault-3.png)

1. 授權給服務主體，允許其取得祕密：
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. 您現在完成服務主體的權限設定，允許其從您的 Key Vault 讀取祕密。 **$AZURE_CLIENT_SECRET** 為您服務主體的 **密碼**。 將您的服務主體認證，新增為密碼存放區 CSI 驅動程式能夠存取 Kubernetes 祕密：
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

**注意：** 若您之後在部署 Kubernetes Pod 時，收到用戶端密碼無效的錯誤， 可能用戶端是用戶端密碼識別碼較舊，而且已經到期或重設。 若要解決此問題，請刪除您的祕密 "secrets-store-creds"，然後使用目前的用戶端密碼識別碼，建立新的密碼。 您可以執行下列命令刪除您的 "secrets-store-creds"：
```azurecli
kubectl delete secrets secrets-store-creds
```

若忘記服務主體的用戶端密碼識別碼，可以使用下列命令重設：

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="using-managed-identities"></a>使用受控識別

若要使用受控識別，請為您建立的 AKS 叢集，指派特定的角色。 
1. 若要建立/列出/讀取使用者指派的受控識別，必須為您的 AKS 叢集指派[受控識別參與者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色。 請確定 **$clientId** 屬於 Kubernetes 叢集所有。

    ```azurecli
    az role assignment create --role "Managed Identity Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. 為 AKS 安裝 Azure Active Directory (Azure AD) 身分識別。
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. 建立 Azure AD 身分識別。 複製**用戶端識別碼**與**主體識別碼**。
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. 為您剛才為 Key Vault 建立的 Azure AD 身分識別，指派讀取者角色。 接著再授權給該身分識別，允許其從您的 Key Vault 取得密碼。 接下來您將使用您剛才所建立之 Azure 身分識別的**用戶端識別碼**與 **主體識別碼**。
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-key-vault"></a>使用從 Key Vault 掛接而來的祕密，部署您的 Pod

下列命令將設定您的 SecretProviderClass 物件：
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="using-service-principal"></a>使用服務主體

使用服務主體。 下列命令將使用您設定的 SecretProviderClass 與 secrets-store-creds 部署您的 Kubernetes Pod。 這是可以用於部署 [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-secrets-store-inline-volume-secretproviderclass.yaml) 與 [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml) 的範本。
```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="using-managed-identities"></a>使用受控識別

使用受控識別。 您將在叢集中建立 **AzureIdentity**，而此受控識別將會參考您先前所建立的身分識別。 接著，請建立 **AzureIdentity**，並參考您先前所建立的 **AzureIdentityBinding**。 使用下列範本填入對應的參數，再將其儲存為 **podIdentityAndBinding.yaml**。  
```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure Idenity
spec:
    type: 0                                 # Set type: 0 for Managed Service Identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of your Azure Identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure Idenity
    selector: azure-pod-identity-binding-selector
```
    
執行下列命令執行繫結：

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

接著實際部署 Pod。 下列部署 YAML 檔案將會使用最後一個步驟中的 Pod 身分識別繫結。 將此檔案儲存為 **podBindingDeployment.yaml**。

```yml
kind: Pod
apiVersion: v1
metadata:
    name: nginx-secrets-store-inline
    labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
    containers:
    - name: nginx
        image: nginx
        volumeMounts:
        - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
    volumes:
    - name: secrets-store-inline
        csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
            secretProviderClass: azure-kvname
```

執行下列命令，以部署您的 Pod：

```azurecli
kubectl apply -f podBindingDeployment.yaml
```
### <a name="check-status-and-secret-content"></a>檢查狀態與祕密內容 
若要顯示您已部署的 Pod：
```azurecli
kubectl get pods
```

若要檢查 Pod 狀態，請使用下列命令：
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![映像](../media/kubernetes-key-vault-4.png)

完成部署之 Pod 的狀態應為「正在執行」。 在底部的「事件」區段中，左側的所有事件類型，全部是「正常」類別。
只要能確認 Pod 正在執行，就能確定您的 Pod 擁有來自 Key Vault 的祕密。

若要顯示 Pod 擁有的所有祕密：
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

若要取得特定祕密的內容：
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

確認所顯示之祕密的內容。

## <a name="next-steps"></a>後續步驟

確定您的 Key Vault 可以復原：
> [!div class="nextstepaction"]
> [開啟虛刪除](https://docs.microsoft.com/azure/key-vault/general/soft-delete-clid)
