---
title: 使用服務主體進行驗證
description: 使用 Azure Active Directory 服務主體，提供您私人容器登錄中映像的存取權。
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455399"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>使用服務主體進行 Azure Container Registry 驗證

您可以使用 Azure Active Directory (Azure AD) 服務主體，來提供容器登錄的容器映像 `docker push` 和 `pull` 存取權。 藉由使用服務主體，您可以提供「無周邊」服務和應用程式的存取權。

## <a name="what-is-a-service-principal"></a>何謂服務主體？

Azure AD *服務主體*提供您訂用帳戶內 Azure 資源的存取權。 您可以將服務主體視為服務的使用者標識，其中"服務"是需要訪問資源的任何應用程式、服務或平臺。 您可以設定一個服務主體，並將其存取權限範圍限制為您指定的那些資源。 然後，請設定應用程式或服務，以使用服務主體的認證來存取那些資源。

在 Azure Container Registry 的內容中，您可以建立一個 Azure AD 服務主體，並提供您在 Azure 中之私人登錄的提取、發送和提取，或其他權限。 如需完整清單，請參閱 [Azure Container Registry 角色和權限](container-registry-roles.md)。

## <a name="why-use-a-service-principal"></a>為何要使用服務主體？

藉由使用 Azure AD 服務主體，您可以提供對您私人容器登錄之有限範圍的存取權。 為每個應用程式或服務創建不同的服務主體，每個應用程式或服務都具有對註冊表的定制存取權限。 此外，由於您可以避免在服務和應用程式之間共用認證，因此您可以替換認證，或只撤銷您所選擇之主體服務 (以及應用程式) 的存取權。

例如，將 Web 應用程式配置為使用僅提供映射`pull`存取權限的服務主體，而生成系統則使用同時提供和`push``pull`訪問的服務主體。 如果應用程式的開發更改了手，則可以輪換其服務主體憑據，而不會影響生成系統。

## <a name="when-to-use-a-service-principal"></a>何時使用服務主體

您應該使用服務主體來提供**無周邊案例**中的登錄存取權。 也就是，任何必須以自動化或其他自動方式發送或提取容器映像的應用程式、服務或指令碼。 例如：

  * *拉：* 將容器從註冊表部署到業務流程系統，包括庫伯奈斯、DC/OS 和 Docker Swarm。 還可以從容器註冊表拉到相關的 Azure 服務，如[Azure 庫伯奈斯服務 （AKS）、Azure](../aks/cluster-container-registry-integration.md)[容器實例](container-registry-auth-aci.md)、[應用服務](../app-service/index.yml)、[批次處理](../batch/index.yml)、[服務結構](/azure/service-fabric/)等。

  * *推送*：使用連續集成和部署解決方案（如 Azure 管道或 Jenkins）生成容器映射並將其推送到註冊表。

對於對註冊表的單獨訪問（例如，當您手動將容器映射拉到開發工作站時），我們建議使用自己的[Azure AD 標識](container-registry-authentication.md#individual-login-with-azure-ad)來進行註冊表訪問（例如，使用 az [acr 登錄][az-acr-login]）。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>範例指令碼

您可以在 GitHub 上找到 Azure CLI 的上述示例腳本，以及 Azure PowerShell 的版本：

* [Azure CLI][acr-scripts-cli]
* [Azure 電源外殼][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>使用服務主體進行身份驗證

獲得對容器註冊表的存取權限的服務主體後，可以配置其憑據以訪問"無頭"服務和應用程式，或使用 命令`docker login`輸入它們。 輸入下列值：

* **使用者名**- 服務主體應用程式 ID（也稱為*用戶端 ID）*
* **密碼**- 服務主體密碼（也稱為*用戶端金鑰*）

每個值都是表單`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`的 GUID。 

> [!TIP]
> 您可以執行 [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) 命令來重新產生服務主體的密碼。
>

### <a name="use-credentials-with-azure-services"></a>將憑據與 Azure 服務一起使用

可以使用使用 Azure 容器註冊表進行身份驗證的任何 Azure 服務中的服務主體憑據。  在各種方案中使用服務主體憑據代替註冊表的管理員憑據。

例如，使用憑據將映射從 Azure 容器註冊表拉到 Azure[容器實例](container-registry-auth-aci.md)。

### <a name="use-with-docker-login"></a>與 Docker 登錄一起使用

可以使用服務主體`docker login`運行。 在下面的示例中，服務主體應用程式 ID 在環境變數`$SP_APP_ID`中傳遞，並在變數`$SP_PASSWD`中的密碼傳遞。 有關管理 Docker 憑據的最佳做法，請參閱[Docker 登錄](https://docs.docker.com/engine/reference/commandline/login/)命令引用。

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

登錄後，Docker 會緩存憑據。

### <a name="use-with-certificate"></a>與證書一起使用

如果向服務主體添加了證書，則可以使用基於證書的身份驗證登錄到 Azure CLI，然後使用[az acr 登錄][az-acr-login]命令訪問註冊表。 使用證書作為機密而不是密碼可在使用 CLI 時提供額外的安全性。 

[創建服務主體](/cli/azure/create-an-azure-service-principal-azure-cli)時，可以創建自簽章憑證。 或者，將一個或多個證書添加到現有服務主體。 例如，如果您使用本文中的一個腳本創建或更新具有從註冊表中提取或推送圖像的許可權的服務主體，請使用[az ad sp 憑據重置][az-ad-sp-credential-reset]命令添加證書。

要使用具有證書的服務主體登錄到[Azure CLI，](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)證書必須採用 PEM 格式並包含私密金鑰。 如果您的證書不是所需的格式，請使用工具（如`openssl`）進行轉換。 當您運行[az 登錄][az-login]以使用服務主體登錄到 CLI 時，也提供服務主體的應用程式 ID 和活動目錄租戶 ID。 下面的示例將這些值作為環境變數顯示：

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

然後，運行[az acr 登錄][az-acr-login]以與註冊表進行身份驗證：

```azurecli
az acr login --name myregistry
```

CLI 使用運行`az login`時創建的權杖來對與註冊表的會話進行身份驗證。

## <a name="next-steps"></a>後續步驟

* 有關使用 Azure 容器註冊表進行身份驗證的其他方案，請參閱[身份驗證概述](container-registry-authentication.md)。

* 有關使用 Azure 金鑰保存庫存儲和檢索容器註冊表的服務主體憑據的示例，請參閱[使用 ACR 任務生成和部署容器映射的](container-registry-tutorial-quick-task.md)教程。

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
