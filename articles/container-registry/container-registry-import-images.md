---
title: 匯入容器映像
description: 藉由使用 Azure API 將容器映像匯入到 Azure 容器登錄，不需要執行 Docker 命令。
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 3950b9fb24b80db4d9654a615521c0eb82914499
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695600"
---
# <a name="import-container-images-to-a-container-registry"></a>將容器映像匯入到容器登錄

您可以輕易地將容器映像匯入 (複製) 到 Azure 容器登錄，不用使用 Docker 命令。 例如，從開發登錄將映像匯入到生產環境登錄，或者從公用登錄複製基底映像。

Azure Container Registry 會處理一些從現有登錄複製映像的常見案例：

* 從公用登錄匯入

* 從相同或不同 Azure 訂用帳戶或租使用者中的另一個 Azure container registry 匯入

* 從非 Azure 私人容器登錄匯入

匯入到 Azure 容器登錄的映像相較於使用 Docker CLI 命令，具有下列優點：

* 因為您的用戶端環境不需要本機 Docker 安裝，所以請匯入任何容器映射，而不考慮支援的作業系統類型。

* 當您匯入多架構映像 (例如，官方 Docker 映像) 時，會複製資訊清單中指定和所有架構與平台的映像。

* 存取目標登錄不需要使用登錄的公用端點。

為了匯入容器映像，本文會要求您在 Azure Cloud Shell 或在本機執行 Azure CLI (建議使用 2.0.55 版或更新版本)。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli]。

> [!NOTE]
> 如果您需要將相同的容器映像散佈到多個 Azure 區域，Azure Container Registry 也支援[異地複寫](container-registry-geo-replication.md)。 藉由異地複寫登錄 (Premium 服務層級) ，您可以使用單一登入的相同映射和標籤名稱來提供多個區域。
>

## <a name="prerequisites"></a>先決條件

如果您還沒有 Azure 容器登錄，請建立登錄。 如需相關步驟，請參閱 [快速入門：使用 Azure CLI 建立私用容器](container-registry-get-started-azure-cli.md)登錄。

若要將映射匯入到 Azure container registry，您的身分識別必須具有目標登錄的寫入權限 (至少是參與者角色，或是允許 importImage 動作) 的自訂角色。 請參閱 [Azure Container Registry 角色和權限](container-registry-roles.md#custom-roles)。 

## <a name="import-from-a-public-registry"></a>從公用登錄匯入

### <a name="import-from-docker-hub"></a>從 Docker Hub 匯入

例如，使用 [az acr import][az-acr-import] 命令將多架構 `hello-world:latest` 映像從 Docker Hub 匯入到名為 *myregistry* 的登錄。 因為 `hello-world` 是來自 Docker Hub 的官方映像，此映像在預設 `library` 存放庫中。 在 `--source` 映像參數的值中包含存放庫名稱，以及選擇性包含標記。 (您可以選擇性依據其資訊清單摘要來識別映像，而不是依據標記，如此可確保映像的特定版本。)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

您可以藉由執行 `az acr repository show-manifests` 命令，確認與此映像相關聯的多個資訊清單：

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

下列範例會從 Docker Hub 中的 `tensorflow` 存放庫匯入公用映像：

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>從 Microsoft 容器登錄匯入

例如， `ltsc2019` 從 `windows` Microsoft Container Registry 中的存放庫匯入 Windows Server Core 映射。

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>從相同 AD 租使用者中的 Azure container registry 匯入

您可以使用整合式 Azure Active Directory 許可權，從相同 AD 租使用者中的 Azure container registry 匯入映射。

* 您的身分識別必須有 Azure Active Directory 許可權，才能從來源登錄 (讀取者角色) ，以及匯入目標登錄 (參與者角色，或允許 importImage 動作) 的 [自訂角色](container-registry-roles.md#custom-roles) 。

* 登錄可以位於相同 Active Directory 租用戶中的相同或不同 Azure 訂用帳戶。

* 可能會停用對來源登錄的[公用存取](container-registry-access-selected-networks.md#disable-public-network-access)。 如果已停用公用存取，請依資源識別碼指定來源登錄，而不是登錄登入伺服器名稱。

### <a name="import-from-a-registry-in-the-same-subscription"></a>從相同訂用帳戶中的登錄匯入

例如，將 `aci-helloworld:latest` 映像從來源登錄 mysourceregistry 匯入到相同 Azure 訂用帳戶中的 myregistry。

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

下列範例 `aci-helloworld:latest` 會從來源登錄 *mysourceregistry* 匯入要 *myregistry* 的映射，其中已停用登錄的公用端點存取權。 提供來源登錄的資源識別碼與 `--registry`。 請注意， `--source` 參數只會指定來源存放庫和標記，而不會指定登錄登入伺服器名稱。

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

下列範例會依據資訊清單摘要 (SHA-256 雜湊，表示為 `sha256:...`) 來匯入映像，而非依據標記：

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>從不同的訂用帳戶匯入登錄

在下列範例中，mysourceregistry 位於與 myregistry 相同 Active Directory 租用戶中的不同訂用帳戶。 提供來源登錄的資源識別碼與 `--registry`。 請注意， `--source` 參數只會指定來源存放庫和標記，而不會指定登錄登入伺服器名稱。

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>使用服務主體認證從登錄匯入

若要從無法使用整合式 Active Directory 許可權來存取的登錄匯入，您可以使用服務主體認證 (如果來源登錄可用) 。 提供 Active Directory [服務主體](container-registry-auth-service-principal.md)的 appID 和密碼，該服務主體具有來源登錄的 ACRPull 存取權。 在建置系統和其他必須將映像匯入到登錄的自動化系統時，使用服務主體將有其效用。

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>從不同 AD 租使用者中的 Azure container registry 匯入

若要從不同 Azure Active Directory 租使用者中的 Azure container registry 匯入，請以登入伺服器名稱指定來源登錄，並提供可讓您存取登錄的使用者名稱和密碼認證。 例如，使用存放 [庫範圍的權杖](container-registry-repository-scoped-permissions.md) 和密碼，或是具有來源登錄 ACRPull 存取權之 Active Directory [服務主體](container-registry-auth-service-principal.md) 的 appID 和密碼。 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>從非 Azure 私人容器登錄匯入

藉由指定啟用登錄存取權的認證，從非 Azure 私人登錄匯入映射。 例如，從私人 Docker 登錄提取映像： 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>後續步驟

在本文中，您會了解從公用登錄或其他私人登錄將容器映像匯入到 Azure 容器登錄。 如需其他映像匯入選項，請參閱 [az acr import][az-acr-import] 命令參考。 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
