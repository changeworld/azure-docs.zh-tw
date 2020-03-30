---
title: 註冊表身份驗證選項
description: 專用 Azure 容器註冊表的身份驗證選項，包括使用 Azure 活動目錄標識登錄、使用服務主體和使用可選的管理認證。
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247042"
---
# <a name="authenticate-with-an-azure-container-registry"></a>向 Azure Container Registry 進行驗證 | Microsoft Docs

向 Azure Container Registry 進行驗證的方式有數種，每一種方式都適用於一或多個登錄使用案例。

建議的方法包括直接通過[個人登錄](#individual-login-with-azure-ad)對註冊表進行身份驗證，或者應用程式和容器協調器可以使用 Azure 活動目錄 （Azure AD）[服務主體](#service-principal)執行無人值守或"無頭"身份驗證。

## <a name="authentication-options"></a>驗證選項

下表列出了可用的身份驗證方法和建議的方案。 有關詳細資訊，請參閱連結的內容。

| 方法                               | 如何進行身份驗證                                           | 案例                                                            | RBAC                             | 限制                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [單個 AD 標識](#individual-login-with-azure-ad)                | `az acr login` 在 Azure CLI 中                             | 開發人員、測試人員的互動式推送/拉動                                    | 是                              | AD 權杖必須每 3 小時續訂一次     |
| [AD 服務主體](#service-principal)                  | `docker login`<br/><br/>`az acr login`在 Azure CLI 中<br/><br/> API 或工具中的註冊表登錄設置<br/><br/> [庫伯內斯拉秘密](container-registry-auth-kubernetes.md)                                           | 來自 CI/CD 管道的無人值守推送<br/><br/> 無人參與的對 Azure 或外部服務的拉取  | 是                              | SP 密碼預設過期為 1 年       |                                                           
| [與 AKS 集成](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | AKS 群集創建或更新時附加註冊表  | 無人參與地拉到 AKS 群集                                                  | 否，僅拉訪問             | 僅適用于 AKS 群集            |
| [Azure 資源的託管標識](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` 在 Azure CLI 中                                       | Azure CI/CD 管道的無人值守推送<br/><br/> 無人參與的對 Azure 服務的拉取<br/><br/>   | 是                              | 僅從支援 Azure[資源的託管標識的](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)Azure 服務中使用              |
| [管理員使用者](#admin-account)                            | `docker login`                                          | 由各個開發人員或測試人員進行互動式推送/拉動                           | 否，始終拉動和推送訪問  | 每個註冊表的單一帳戶，不建議多個使用者         |
| [存儲庫範圍訪問權杖](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`在 Azure CLI 中   | 由各個開發人員或測試人員互動式推送/拉取到存儲庫<br/><br/> 由單個系統或外部設備無人值守的推送/拉取到存儲庫                  | 是                              | 當前未與 AD 標識集成  |

## <a name="individual-login-with-azure-ad"></a>使用 Azure AD 進行個人登入

直接與您的登錄搭配運作時 (例如將映像拖曳至開發工作站或從開發工作站推送映像)，請在 [Azure CLI](/cli/azure/install-azure-cli) 中使用 [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) 命令來進行驗證：

```azurecli
az acr login --name <acrName>
```

當您使用 `az acr login` 來進行登入時，CLI 會使用您執行 [az login](/cli/azure/reference-index#az-login) 時所建立的權杖，以順暢地向登錄驗證您的工作階段。 要完成身份驗證流，必須在您的環境中安裝和運行 Docker。 `az acr login`使用 Docker 用戶端在`docker.config`檔中設置 Azure 活動目錄權杖。 在您以此方式登入之後，系統會快取您的認證，而您工作階段中的後續 `docker` 命令不會要求提供使用者名稱或密碼。

> [!TIP]
> 當您要`az acr login`將 Docker 映射以外的專案推送或拉取到註冊表（如[OCI 工件](container-registry-oci-artifacts.md)）時，還用於對單個標識進行身份驗證。  


對於註冊表訪問，所使用的`az acr login`權杖的有效期為 3**小時**，因此我們建議您在運行`docker`命令之前始終登錄到註冊表。 如果您的權杖過期，您可以再次使用 `az acr login` 命令進行重新驗證來重新整理該權杖。 

使用 `az acr login` 搭配 Azure 身分識別可提供[角色型存取](../role-based-access-control/role-assignments-portal.md)功能。 對於某些方案，您可能希望在 Azure AD 中使用自己的個人標識登錄到註冊表。 對於跨服務方案或處理不想管理單個訪問的工作組或開發工作流的需求，還可以使用[Azure 資源的託管標識](container-registry-authentication-managed-identity.md)登錄。

## <a name="service-principal"></a>服務主體

如果您將[服務主體](../active-directory/develop/app-objects-and-service-principals.md)指派給登錄，應用程式或服務便可以使用它來進行遠端控制驗證。 服務主體可允許對登錄進行[角色型存取](../role-based-access-control/role-assignments-portal.md)，而您可以將多個服務主體指派給登錄。 多個服務主體可讓您為不同的應用程式定義不同的存取權。

容器登錄的可用角色包括：

* **AcrPull**：提取

* **AcrPush**：提取和推送

* **擁有者**：拉取、推送角色並將其分配給其他使用者

如需角色的完整清單，請參閱 [Azure Container Registry 角色和權限](container-registry-roles.md)。

有關 CLI 腳本創建用於使用 Azure 容器註冊表進行身份驗證的服務主體，以及更多指南，請參閱[具有服務主體的 Azure 容器註冊表身份驗證](container-registry-auth-service-principal.md)。

## <a name="admin-account"></a>管理帳戶

每個容器登錄都包含一個管理使用者帳戶，且預設為停用。 您可以在 Azure 入口網站中或藉由使用 Azure CLI 或其他 Azure 工具，啟用此管理使用者並管理其認證。

> [!IMPORTANT]
> 管理帳戶是專為讓單一使用者存取登錄而設計，主要用於測試。 我們不建議在多個使用者之間共用管理員帳戶憑據。 所有使用管理帳戶進行驗證的使用者會顯示為單一使用者，此使用者具備登錄的推送和提取存取權。 變更或停用此帳戶時，會將所有使用其認證之使用者的登錄存取權都停用。 針對遠端控制案例的使用者和服務主體，建議使用個人身分識別。
>

管理帳戶隨附兩個密碼，兩個密碼都可以重新產生。 兩個密碼可讓您在重新產生其中一個密碼時，使用另一個密碼來維持與登錄的連線。 如果已啟用管理帳戶，即可在系統提示時將使用者名稱和其中一個密碼傳遞給 `docker login` 命令，向登錄進行基本驗證。 例如：

```
docker login myregistry.azurecr.io 
```

有關管理登錄憑據的最佳做法，請參閱[docker 登錄](https://docs.docker.com/engine/reference/commandline/login/)命令引用。

若要為現有的登錄啟用管理使用者，您可以在 Azure CLI 中使用 [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) 命令的 `--admin-enabled` 參數：

```azurecli
az acr update -n <acrName> --admin-enabled true
```

您可以在 Azure 入口網站中啟用管理使用者，方法是瀏覽至您的登錄，選取 [設定]**** 底下的 [存取金鑰]****，然後選取 [管理使用者]**** 底下的 [啟用]****。

![Azure 入口網站中的啟用管理使用者 UI][auth-portal-01]

## <a name="next-steps"></a>後續步驟

* [使用 Azure CLI 推送您的第一個映像](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
