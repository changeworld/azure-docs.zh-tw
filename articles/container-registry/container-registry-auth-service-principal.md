---
title: 使用服務主體進行驗證
description: 使用 Azure Active Directory 服務主體，提供您私人容器登錄中映像的存取權。
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74455399"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>使用服務主體進行 Azure Container Registry 驗證

您可以使用 Azure Active Directory (Azure AD) 服務主體，來提供容器登錄的容器映像 `docker push` 和 `pull` 存取權。 藉由使用服務主體，您可以提供「無周邊」服務和應用程式的存取權。

## <a name="what-is-a-service-principal"></a>何謂服務主體？

Azure AD *服務主體*提供您訂用帳戶內 Azure 資源的存取權。 您可以將服務主體視為服務的使用者身分識別，其中「服務」是任何需要存取資源的應用程式、服務或平臺。 您可以設定一個服務主體，並將其存取權限範圍限制為您指定的那些資源。 然後，請設定應用程式或服務，以使用服務主體的認證來存取那些資源。

在 Azure Container Registry 的內容中，您可以建立一個 Azure AD 服務主體，並提供您在 Azure 中之私人登錄的提取、發送和提取，或其他權限。 如需完整清單，請參閱 [Azure Container Registry 角色和權限](container-registry-roles.md)。

## <a name="why-use-a-service-principal"></a>為何要使用服務主體？

藉由使用 Azure AD 服務主體，您可以提供對您私人容器登錄之有限範圍的存取權。 為每個應用程式或服務建立不同的服務主體，每個都有您登錄的量身訂做存取權限。 此外，由於您可以避免在服務和應用程式之間共用認證，因此您可以替換認證，或只撤銷您所選擇之主體服務 (以及應用程式) 的存取權。

例如，將您的 web 應用程式設定為使用僅提供影像`pull`存取權的服務主體，而您的組建系統會使用提供它`push`和`pull`存取權的服務主體。 如果應用程式的開發變更，您可以旋轉其服務主體認證，而不會影響組建系統。

## <a name="when-to-use-a-service-principal"></a>何時使用服務主體

您應該使用服務主體來提供**無周邊案例**中的登錄存取權。 也就是，任何必須以自動化或其他自動方式發送或提取容器映像的應用程式、服務或指令碼。 例如：

  * *Pull*：將容器從登錄部署至協調流程系統，包括 KUBERNETES、DC/OS 和 Docker Swarm。 您也可以從容器登錄提取到相關的 Azure 服務，例如[Azure Kubernetes Service （AKS）](../aks/cluster-container-registry-integration.md)、 [Azure 容器實例](container-registry-auth-aci.md)、 [App Service](../app-service/index.yml)、[批次](../batch/index.yml)、 [Service Fabric](/azure/service-fabric/)及其他專案。

  * *推送*：使用持續整合和部署解決方案（例如 Azure Pipelines 或 Jenkins）來建立容器映射，並將其推送至登錄。

對於登錄的個別存取（例如，當您手動將容器映射提取到您的開發工作站時），我們建議使用您自己的[Azure AD 身分識別](container-registry-authentication.md#individual-login-with-azure-ad)，而不是登錄存取（例如，使用[az acr login][az-acr-login]）。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>範例指令碼

您可以在 GitHub 上找到 Azure CLI 的前述範例腳本，以及 Azure PowerShell 的版本：

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>使用服務主體進行驗證

一旦您擁有已授與容器登錄存取權的服務主體，您可以設定其認證以存取「無周邊」服務和應用程式，或使用`docker login`命令輸入它們。 輸入下列值：

* **使用者名稱**-服務主體應用程式識別碼（也稱為*用戶端識別碼*）
* **密碼**-服務主體密碼（也稱為*用戶端秘密*）

每個值都是表單`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`的 GUID。 

> [!TIP]
> 您可以執行 [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) 命令來重新產生服務主體的密碼。
>

### <a name="use-credentials-with-azure-services"></a>搭配 Azure 服務使用認證

您可以使用從使用 Azure container registry 進行驗證的任何 Azure 服務中的服務主體認證。  在各種情況下，請使用服務主體認證來取代登錄的系統管理員認證。

例如，使用認證將映射從 Azure container registry 提取至[Azure 容器實例](container-registry-auth-aci.md)。

### <a name="use-with-docker-login"></a>搭配 docker 登入使用

您可以使用`docker login`服務主體來執行。 在下列範例中，服務主體應用程式識別碼是在環境變數`$SP_APP_ID`中傳遞，而在變數`$SP_PASSWD`中則是密碼。 如需管理 Docker 認證的最佳作法，請參閱[Docker login](https://docs.docker.com/engine/reference/commandline/login/)命令參考。

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

登入之後，Docker 會快取認證。

### <a name="use-with-certificate"></a>搭配憑證使用

如果您已將憑證新增至您的服務主體，您可以使用以憑證為基礎的驗證來登入 Azure CLI，然後使用[az acr login][az-acr-login]命令來存取登錄。 當您使用 CLI 時，使用憑證做為秘密而非密碼會提供額外的安全性。 

當您[建立服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)時，可以建立自我簽署的憑證。 或者，將一或多個憑證新增至現有的服務主體。 例如，如果您使用本文中的其中一個腳本來建立或更新具有從登錄提取或推送映射之許可權的服務主體，請使用[az ad sp credential reset][az-ad-sp-credential-reset]命令來新增憑證。

若要使用服務主體搭配憑證來登[入 Azure CLI](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)，憑證必須是 PEM 格式，並包含私密金鑰。 如果您的憑證不是必要的格式，請使用之類的`openssl`工具加以轉換。 當您執行[az login][az-login]以使用服務主體登入 CLI 時，也請提供服務主體的應用程式識別碼和 Active Directory 的租使用者識別碼。 下列範例會將這些值顯示為環境變數：

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

然後，執行[az acr login][az-acr-login]以向登錄進行驗證：

```azurecli
az acr login --name myregistry
```

CLI 會使用您執行時所建立的`az login`權杖，以向登錄驗證您的會話。

## <a name="next-steps"></a>後續步驟

* 如需使用 Azure container registry 進行驗證的其他案例，請參閱[驗證概述](container-registry-authentication.md)。

* 如需使用 Azure 金鑰保存庫來儲存和抓取容器登錄之服務主體認證的範例，請參閱[使用 ACR 工作建立及部署容器映射](container-registry-tutorial-quick-task.md)的教學課程。

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
