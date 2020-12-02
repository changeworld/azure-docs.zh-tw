---
title: '在 Azure Kubernetes Service (Preview 中使用 Azure Active Directory pod 管理的身分識別) '
description: 瞭解如何在 Azure Kubernetes Service (AKS) 中使用 AAD pod 管理的受控識別
services: container-service
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 150e2e71a4db8ab07caad479ae098d5b9eb746da
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466363"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>在 Azure Kubernetes Service (Preview 中使用 Azure Active Directory pod 管理的身分識別) 

Azure Active Directory pod 管理的身分識別會使用 Kubernetes 基本專案，將 [Azure 資源的受控][az-managed-identities] 識別和 AZURE ACTIVE DIRECTORY (AAD) 中的身分識別關聯至 pod。 系統管理員將身分識別和系結建立為 Kubernetes 基本專案，以允許 pod 存取依賴 AAD 作為身分識別提供者的 Azure 資源。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始之前

您必須安裝下列資源：

* Azure CLI 版本2.8.0 或更新版本
* `azure-preview`延伸模組版本0.4.68 或更新版本

### <a name="limitations"></a>限制

* 叢集最多允許50個 pod 身分識別。
* 叢集允許最多50個 pod 身分識別例外狀況。
* Pod 管理的身分識別僅適用于 Linux 節點集區。

### <a name="register-the-enablepodidentitypreview"></a>註冊 `EnablePodIdentityPreview`

註冊 `EnablePodIdentityPreview` 功能：

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>安裝 `aks-preview` Azure CLI

您也需要 *aks-preview* Azure CLI 擴充功能版本0.4.64 或更新版本。 使用 [az extension add][az-extension-add]命令安裝 *aks-preview* Azure CLI 擴充功能。 或使用 [az extension update][az-extension-update] 命令安裝任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>使用受控識別建立 AKS 叢集

建立已啟用受控識別和 pod 管理身分識別的 AKS 叢集。 下列命令會使用 [az group create][az-group-create]來建立名為 *myResourceGroup* 的資源群組，並使用 [az Aks create][az-aks-create]命令，在 *MyResourceGroup* 資源群組中建立名為 *myAKSCluster* 的 aks 叢集。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-managed-identity --enable-pod-identity
```

使用 [az aks get 認證][az-aks-get-credentials] 登入您的 aks 叢集。 此命令也會 `kubectl` 在您的開發電腦上下載並設定用戶端憑證。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-an-identity"></a>建立身分識別

使用 [az identity create][az-identity-create] 建立身分識別，並設定 *IDENTITY_CLIENT_ID* 和 *IDENTITY_RESOURCE_ID* 變數。

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="create-a-pod-identity"></a>建立 pod 身分識別

使用建立叢集的 pod 身分識別 `az aks pod-identity add` 。

> [!IMPORTANT]
> 您必須在訂用帳戶上擁有適當的許可權，例如 `Owner` ，才能建立身分識別和角色系結。

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> 當您在 AKS 叢集上啟用 pod 管理的身分識別時，名為 *AKS 的* AzurePodIdentityException 會新增至 *kube-system* 命名空間。 AzurePodIdentityException 可讓具有特定標籤的 pod 存取 Azure Instance Metadata Service (IMDS) 端點，而不會被節點管理的身分識別 (NMI) 伺服器攔截。 *Aks-附加元件例外* 狀況允許 aks 第一方附加元件，例如 AAD pod 管理的身分識別，不需要手動設定 AzurePodIdentityException 即可運作。 （選擇性）您可以使用 `az aks pod-identity exception add` 、、或來新增、移除和更新 AzurePodIdentityException `az aks pod-identity exception delete` `az aks pod-identity exception update` `kubectl` 。

## <a name="run-a-sample-application"></a>執行範例應用程式

若要讓 pod 使用 AAD pod 管理的身分識別，pod 需要 *aadpodidbinding* 標籤，其值與 *AzureIdentityBinding* 的選取器相符。 若要使用 AAD pod 管理的身分識別來執行範例應用程式，請建立 `demo.yaml` 具有下列內容的檔案。 以先前步驟中的值取代 *POD_IDENTITY_NAME*、 *IDENTITY_CLIENT_ID* 和 *IDENTITY_RESOURCE_GROUP* 。 以您的訂用帳戶識別碼取代 *SUBSCRIPTION_ID* 。

> [!NOTE]
> 在先前的步驟中，您建立了 *POD_IDENTITY_NAME*、 *IDENTITY_CLIENT_ID* 和 *IDENTITY_RESOURCE_GROUP* 變數。 例如，您可以使用類似的命令 `echo` 來顯示您為變數設定的值 `echo $IDENTITY_NAME` 。

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

請注意，pod 定義具有 *aadpodidbinding* 標籤，其值符合您 `az aks pod-identity add` 在上一個步驟中執行的 pod 身分識別名稱。

`demo.yaml`使用下列方式，部署至與您的 pod 身分識別相同的命名空間 `kubectl apply` ：

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

確認範例應用程式是使用執行成功的 `kubectl logs` 。

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

確認記錄顯示已成功取得權杖，且 *取得* 作業成功。
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>清除

若要從您的叢集中移除 AAD pod 管理的身分識別，請從叢集移除範例應用程式和 pod 身分識別。 然後移除身分識別。

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>後續步驟

如需受控識別的相關詳細資訊，請參閱[適用於 Azure 資源的受控識別][az-managed-identities]。

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create
