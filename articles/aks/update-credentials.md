---
title: 重設叢集的認證
titleSuffix: Azure Kubernetes Service
description: 瞭解如何更新或重設 Azure Kubernetes Service (AKS) 叢集的服務主體或 AAD 應用程式認證
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: a9cc19184cc39975cce18d17a6047bedf5915555
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86251021"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>更新或輪替 Azure Kubernetes Service (AKS 的認證) 

根據預設，建立 AKS 叢集時包含的服務主體到期時間為期一年。 當到期日接近時，您可以重設認證，將服務主體再延長一段時間。 您可能也想為已定義的安全性原則更新或輪替認證。 本文詳述如何更新 AKS 叢集的認證。

您也可以將[AKS 叢集與 Azure Active Directory 整合][aad-integration]，並將其作為叢集的驗證提供者。 在此情況下，您將會為您的叢集、AAD 伺服器應用程式和 AAD 用戶端應用程式建立2個以上的身分識別，您也可以重設這些認證。

您也可以使用受控識別，而不是服務主體來取得權限。 受控識別比服務主體更容易管理，而且不需要更新或輪替。 如需詳細資訊，請參閱[使用受控識別](use-managed-identity.md)。

## <a name="before-you-begin"></a>開始之前

您需要安裝並設定 Azure CLI 版本2.0.65 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>為您的 AKS 叢集更新或建立新的服務主體

當您想要更新 AKS 叢集的認證時，可以選擇：

* 更新叢集所使用之現有服務主體的認證，或
* 建立服務主體，並更新叢集，以使用這些新認證。

### <a name="check-the-expiration-date-of-your-service-principal"></a>檢查服務主體的到期日

若要檢查服務主體的到期日，請使用[az ad sp credential list][az-ad-sp-credential-list]命令。 下列範例會使用[az aks show][az-aks-show]命令，取得*myResourceGroup*資源群組中名為*myAKSCluster*之叢集的服務主體識別碼。 服務主體識別碼會設定為名為*SP_ID*的變數，以與[az ad SP credential list][az-ad-sp-credential-list]命令搭配使用。

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-existing-service-principal-credential"></a>重設現有的服務主體認證

若要更新現有服務主體的認證，請使用 [az aks show][az-aks-show] 命令，取得叢集的服務主體識別碼。 下列範例會針對 *myResourceGroup* 資源群組中名稱為 *myAKSCluster* 的叢集取得識別碼。 服務主體識別碼會設定為名為*SP_ID*的變數，以在其他命令中使用。 這些命令會使用 Bash 語法。

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

使用包含服務主體識別碼的變數集，現在可以使用 [az ad sp credential reset][az-ad-sp-credential-reset] 重設認證。 以下範例可讓 Azure 平台為服務主體產生新的安全密碼。 這個新的安全密碼也會儲存為變數。

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

現在繼續[使用新的服務主體認證來更新 AKS](#update-aks-cluster-with-new-service-principal-credentials)叢集。 為了讓服務主體變更反映在 AKS 叢集上，此為必要步驟。

### <a name="create-a-new-service-principal"></a>建立新的服務主體

如果您選擇更新上一節中的現有服務主體認證，請跳過此步驟。 繼續[使用新的服務主體認證來更新 AKS](#update-aks-cluster-with-new-service-principal-credentials)叢集。

若要建立服務主體，然後更新 AKS 叢集以使用這些新認證，請使用 [az ad sp create-for-rbac][az-ad-sp-create] 命令。 在下列範例中，`--skip-assignment` 參數會防止指派任何額外的預設指派：

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

輸出類似於下列範例： 記下您自己的 `appId` 與 `password`。 在下一個步驟將用到這些值。

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

現在，使用您自己的 [az ad sp create-for-rbac][az-ad-sp-create] 命令輸出，定義服務主體識別碼和用戶端密碼的變數，如以下範例所示。 *SP_ID* 是您的 *appId*，而 *SP_SECRET* 是您的*密碼*：

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

現在繼續[使用新的服務主體認證來更新 AKS](#update-aks-cluster-with-new-service-principal-credentials)叢集。 為了讓服務主體變更反映在 AKS 叢集上，此為必要步驟。

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>以新的服務主體認證更新 AKS 叢集

無論您選擇要更新現有服務主體的認證，或建立服務主體，您現在可利用 [az aks update-credentials][az-aks-update-credentials] 命令，使用新認證更新 AKS 叢集。 使用 *--service-principal* 和 *--client-secret* 的變數：

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret "$SP_SECRET"
```

在 AKS 上更新服務主體認證需要幾分鐘的時間。

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>使用新的 AAD 應用程式認證來更新 AKS 叢集

您可以遵循[AAD 整合步驟][create-aad-app]來建立新的 AAD 伺服器和用戶端應用程式。 或重設現有的 AAD 應用程式，[方法與服務主體重設相同](#reset-existing-service-principal-credential)。 之後，您只需要使用相同的[az aks update-認證][az-aks-update-credentials]命令來更新叢集 AAD 應用程式認證，但使用 *--reset-AAD*變數。

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>後續步驟

在本文中，已更新 AKS 叢集本身和 AAD 整合應用程式的服務主體。 如需更多如何管理叢集內工作負載之身分識別的相關資訊，請參閱[在 AKS 中驗證和授權的最佳做法][best-practices-identity]。

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az-ad-sp-credential-list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
