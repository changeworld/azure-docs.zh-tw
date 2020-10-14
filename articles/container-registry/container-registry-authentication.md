---
title: 登錄驗證選項
description: 私用 Azure container registry 的驗證選項，包括使用 Azure Active Directory 身分識別登入、使用服務主體，以及使用選擇性的系統管理員認證。
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 1747dfa0664778283d0cea06940ea95982c269a2
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048010"
---
# <a name="authenticate-with-an-azure-container-registry"></a>向 Azure Container Registry 進行驗證 | Microsoft Docs

向 Azure Container Registry 進行驗證的方式有數種，每一種方式都適用於一或多個登錄使用案例。

建議的方式包括透過 [個別登](#individual-login-with-azure-ad)入直接向登錄進行驗證，或您的應用程式和容器協調器可以使用 Azure Active Directory (Azure AD) [服務主體](#service-principal)來執行自動或「無周邊」驗證。

## <a name="authentication-options"></a>驗證選項

下表列出可用的驗證方法和一般案例。 如需詳細資料，請參閱連結的內容。

| 方法                               | 如何驗證                                           | 案例                                                            | 角色型存取控制 (RBAC)                             | 限制                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [個別 AD 身分識別](#individual-login-with-azure-ad)                | `az acr login` 在 Azure CLI                             | 開發人員、測試人員的互動式推送/提取                                    | 是                              | 必須每3小時更新一次 AD 權杖     |
| [AD 服務主體](#service-principal)                  | `docker login`<br/><br/>在 Azure CLI 中的 `az acr login`<br/><br/> Api 或工具中的登錄登入設定<br/><br/> [Kubernetes 提取秘密](container-registry-auth-kubernetes.md)                                           | 從 CI/CD 管線自動推入<br/><br/> 自動提取至 Azure 或外部服務  | 是                              | SP 密碼預設過期期限為1年       |                                                           
| [與 AKS 整合](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | 建立或更新 AKS 叢集時附加登錄  | 自動提取至 AKS 叢集                                                  | 否，僅提取存取             | 僅適用于 AKS 叢集            |
| [適用于 Azure 資源的受控識別](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` 在 Azure CLI                                       | 從 Azure CI/CD 管線自動推入<br/><br/> 自動提取至 Azure 服務<br/><br/>   | 是                              | 僅用於[支援 azure 資源受控](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)識別的 azure 服務              |
| [管理使用者](#admin-account)                            | `docker login`                                          | 個別開發人員或測試人員的互動式推送/提取<br/><br/>將映射從登錄部署到 Azure App Service 或 Azure 容器實例的入口網站                      | 否，一律提取和推送存取  | 每個登錄單一帳戶，不建議用於多個使用者         |
| [存放庫範圍的存取權杖](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>在 Azure CLI 中的 `az acr login`   | 個別開發人員或測試人員的互動式推送/提取至存放庫<br/><br/> 個別系統或外部裝置的自動推送/提取至存放庫                  | 是                              | 目前未與 AD 身分識別整合  |

## <a name="individual-login-with-azure-ad"></a>使用 Azure AD 進行個人登入

直接與您的登錄搭配運作時（例如將映射提取至您建立的映射，並將其從開發工作站推送至您所建立的登錄）時，請使用您的個別 Azure 身分識別進行驗證。 使用[az login 登](/cli/azure/reference-index#az-login)入[Azure CLI](/cli/azure/install-azure-cli) ，然後執行[az acr login](/cli/azure/acr#az-acr-login)命令：

```azurecli
az login
az acr login --name <acrName>
```

當您使用 `az acr login` 來進行登入時，CLI 會使用您執行 `az login` 時所建立的權杖，以順暢地向登錄驗證您的工作階段。 若要完成驗證流程，必須在您的環境中安裝並執行 Docker CLI 和 Docker daemon。 `az acr login` 使用 Docker 用戶端在檔案中設定 Azure Active Directory token `docker.config` 。 在您以此方式登入之後，系統會快取您的認證，而您工作階段中的後續 `docker` 命令不會要求提供使用者名稱或密碼。

> [!TIP]
> `az acr login`當您想要將 Docker 映射以外的成品推送或提取至您的登錄（例如[OCI 構件](container-registry-oci-artifacts.md)）時，也可以使用來驗證個別的身分識別。  

針對登錄存取，使用的權杖 `az acr login` 有效 **時間為3小時**，因此我們建議您在執行命令之前，一律先登入登錄 `docker` 。 如果您的權杖過期，您可以再次使用 `az acr login` 命令進行重新驗證來重新整理該權杖。 

使用 `az acr login` With azure 身分識別可提供 [azure 角色型存取控制 (azure RBAC) ](../role-based-access-control/role-assignments-portal.md)。 在某些情況下，您可能會想要在 Azure AD 中以您自己的個別身分識別登入登錄，或使用特定的 [azure 角色和許可權](container-registry-roles.md)來設定其他 azure 使用者。 如果您不想要管理個別存取的跨服務案例或工作流程需求，也可以使用 [Azure 資源的受控識別](container-registry-authentication-managed-identity.md)登入。

### <a name="az-acr-login-with---expose-token"></a>az acr login with--公開-token

在某些情況下， `az acr login` 當 Docker daemon 未在您的環境中執行時，您可能需要向進行驗證。 例如，您可能需要 `az acr login` 在 Azure Cloud Shell 的腳本中執行，這會提供 DOCKER CLI，但不會執行 docker daemon。

針對此案例，請 `az acr login` 先使用 `--expose-token` 參數執行。 此選項會公開存取權杖，而不是透過 Docker CLI 登入。

```azurecli
az acr login --name <acrName> --expose-token
```

輸出會顯示存取權杖，縮寫如下：

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 

然後，執行 `docker login` ，以使用者名稱的形式傳遞， `00000000-0000-0000-0000-000000000000` 並使用存取權杖作為密碼：

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password eyJhbGciOiJSUzI1NiIs[...]24V7wA
```

## <a name="service-principal"></a>服務主體

如果您將[服務主體](../active-directory/develop/app-objects-and-service-principals.md)指派給登錄，應用程式或服務便可以使用它來進行遠端控制驗證。 服務主體可讓 [azure 以角色為基礎的存取控制 (AZURE RBAC) ](../role-based-access-control/role-assignments-portal.md) 登錄，您可以將多個服務主體指派給登錄。 多個服務主體可讓您為不同的應用程式定義不同的存取權。

容器登錄的可用角色包括：

* **AcrPull**：提取

* **AcrPush**：提取和推送

* **擁有**者：提取、推送及指派角色給其他使用者

如需角色的完整清單，請參閱 [Azure Container Registry 角色和權限](container-registry-roles.md)。

若要使用 CLI 腳本來建立服務主體，以使用 Azure container registry 進行驗證，以及更多指引，請參閱 [Azure Container Registry 使用服務主體進行驗證](container-registry-auth-service-principal.md)。

## <a name="admin-account"></a>管理帳戶

每個容器登錄都包含一個管理使用者帳戶，且預設為停用。 您可以在 Azure 入口網站中或藉由使用 Azure CLI 或其他 Azure 工具，啟用此管理使用者並管理其認證。 系統管理員帳戶具有登錄的完整許可權。

在某些案例中，將映射從容器登錄部署至某些 Azure 服務時，目前需要系統管理員帳戶。 例如，當您直接從登錄將入口網站中的容器映射部署至 [Azure 容器實例](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) 或 [適用于容器的 azure Web Apps](container-registry-tutorial-deploy-app.md)時，需要系統管理員帳戶。

> [!IMPORTANT]
> 管理帳戶是專為讓單一使用者存取登錄而設計，主要用於測試。 我們不建議在多位使用者之間共用系統管理員帳號憑證。 所有使用管理帳戶進行驗證的使用者會顯示為單一使用者，此使用者具備登錄的推送和提取存取權。 變更或停用此帳戶時，會將所有使用其認證之使用者的登錄存取權都停用。 針對遠端控制案例的使用者和服務主體，建議使用個人身分識別。
>

管理帳戶隨附兩個密碼，兩個密碼都可以重新產生。 兩個密碼可讓您在重新產生其中一個密碼時，使用另一個密碼來維持與登錄的連線。 如果已啟用管理帳戶，即可在系統提示時將使用者名稱和其中一個密碼傳遞給 `docker login` 命令，向登錄進行基本驗證。 例如︰

```
docker login myregistry.azurecr.io 
```

如需管理登入認證的最佳作法，請參閱 [docker 登](https://docs.docker.com/engine/reference/commandline/login/) 入命令參考。

若要為現有的登錄啟用管理使用者，您可以在 Azure CLI 中使用 [az acr update](/cli/azure/acr#az-acr-update) 命令的 `--admin-enabled` 參數：

```azurecli
az acr update -n <acrName> --admin-enabled true
```

您可以在 Azure 入口網站中啟用管理使用者，方法是瀏覽至您的登錄，選取 [設定]**** 底下的 [存取金鑰]****，然後選取 [管理使用者]**** 底下的 [啟用]****。

![Azure 入口網站中的啟用管理使用者 UI][auth-portal-01]

## <a name="next-steps"></a>後續步驟

* [使用 Azure CLI 推送您的第一個映像](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
