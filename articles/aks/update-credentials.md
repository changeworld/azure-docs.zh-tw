---
title: 重設 Azure Kubernetes Service (AKS) 叢集的認證
description: 瞭解如何更新或重設 Azure Kubernetes Service （AKS）叢集的服務主體或 AAD 應用程式認證
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 5dab9a778653d2ec6e32ddb3833ddcf6a95cae13
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096114"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>更新或輪替 Azure Kubernetes Service 的認證（AKS）

根據預設，建立 AKS 叢集時包含的服務主體到期時間為期一年。 當到期日接近時，您可以重設認證，將服務主體再延長一段時間。 您可能也想為已定義的安全性原則更新或輪替認證。 本文詳述如何更新 AKS 叢集的認證。

您也可以將[AKS 叢集與 Azure Active Directory 整合][aad-integration]，並將其作為叢集的驗證提供者。 在此情況下，您將會為您的叢集、AAD 伺服器應用程式和 AAD 用戶端應用程式建立2個以上的身分識別，您也可以重設這些認證。 

## <a name="before-you-begin"></a>開始之前

您需要安裝並設定 Azure CLI 版本2.0.65 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>為您的 AKS 叢集更新或建立新的服務主體

當您想要更新 AKS 叢集的認證時，可以選擇：

* 更新叢集所使用之現有服務主體的認證，或
* 建立服務主體，並更新叢集，以使用這些新認證。

### <a name="reset-existing-service-principal-credential"></a>重設現有的服務主體認證

若要更新現有服務主體的認證，請使用[az aks show][az-aks-show]命令取得叢集的服務主體識別碼。 下列範例會針對 *myResourceGroup* 資源群組中名稱為 *myAKSCluster* 的叢集取得識別碼。 服務主體識別碼會設定為名為*SP_ID*的變數，以在其他命令中使用。

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

使用包含服務主體識別碼的變數集，現在請使用[az ad sp credential reset][az-ad-sp-credential-reset]重設認證。 以下範例可讓 Azure 平台為服務主體產生新的安全密碼。 這個新的安全密碼也會儲存為變數。

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

現在繼續[使用新的服務主體認證來更新 AKS](#update-aks-cluster-with-new-service-principal-credentials)叢集。 為了讓服務主體變更反映在 AKS 叢集上，此為必要步驟。

### <a name="create-a-new-service-principal"></a>建立新的服務主體

如果您選擇更新上一節中的現有服務主體認證，請跳過此步驟。 繼續[使用新的服務主體認證來更新 AKS](#update-aks-cluster-with-new-service-principal-credentials)叢集。

若要建立服務主體，然後更新 AKS 叢集以使用這些新認證，請使用[az ad sp create-rbac][az-ad-sp-create]命令。 在下列範例中，`--skip-assignment` 參數會防止指派任何額外的預設指派：

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

現在使用您自己的[az ad sp create-rbac][az-ad-sp-create]命令的輸出，定義服務主體識別碼和用戶端密碼的變數，如下列範例所示。 *SP_ID* 是您的 *appId*，而 *SP_SECRET* 是您的*密碼*：

```azurecli-interactive
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

現在繼續[使用新的服務主體認證來更新 AKS](#update-aks-cluster-with-new-service-principal-credentials)叢集。 為了讓服務主體變更反映在 AKS 叢集上，此為必要步驟。

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>以新的服務主體認證更新 AKS 叢集

無論您是否選擇更新現有服務主體的認證或建立服務主體，您現在都可以使用[az AKS update-認證][az-aks-update-credentials]命令，以新的認證更新 AKS 叢集。 使用 *--service-principal* 和 *--client-secret* 的變數：

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
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

在本文中，已更新 AKS 叢集本身和 AAD 整合應用程式的服務主體。 如需如何管理叢集中工作負載識別的詳細資訊，請參閱[AKS 中驗證和授權的最佳作法][best-practices-identity]。

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
