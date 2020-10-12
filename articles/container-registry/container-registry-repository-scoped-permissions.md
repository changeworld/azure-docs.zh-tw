---
title: Azure Container Registry 中存放庫的權限
description: 建立權杖，其許可權範圍限於 Premium 登錄中的特定存放庫，以提取或推送映射，或執行其他動作
ms.topic: article
ms.date: 05/27/2020
ms.openlocfilehash: 8661ff2e320788d3899ae16dd3bee7d3ff662caa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84509401"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>建立具有存放庫範圍權限的權杖

本文說明如何建立權杖和範圍對應，以管理容器登錄中的存放庫範圍權限。 藉由建立權杖，登錄擁有者可以提供具有限定範圍、限時存取存放庫的使用者或服務，以提取或推送映像，或執行其他動作。 權杖提供較其他登錄[驗證選項](container-registry-authentication.md) (範圍是整個登錄的權限) 更加細微的權限。 

建立權杖的案例包括：

* 允許具有個別權杖的 IoT 裝置從存放庫提取映像
* 提供外部組織特定存放庫的權限 
* 為貴組織中的不同使用者群組限制存放庫存取權限。 例如，為負責組建特定映像的開發人員提供寫入和讀取權限，而為從這些存放庫進行部署的群組提供讀取權限。

**進階**容器登錄服務層級中提供這項功能。 如需登錄服務層級和限制的相關資訊，請參閱 [Azure Container Registry 服務層級](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能目前在預覽階段，但[有某些限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="preview-limitations"></a>預覽限制

* 您目前無法將存放庫範圍的權限指派給 Azure Active Directory 身分識別，例如服務主體或受控識別。
* 您無法在啟用[匿名提取存取](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)的登錄中建立範圍對應。

## <a name="concepts"></a>概念

若要設定存放庫範圍的權限，您可以使用相關聯的*範圍對應*建立*權杖*。 

* **權杖**連同產生的密碼可讓使用者向登錄進行驗證。 您可以設定權杖密碼的到期日，或隨時停用權杖。  

  使用權杖進行驗證之後，使用者或服務可以執行一或多個*動作*，範圍設定為一個或多個存放庫。

  |動作  |描述  | 範例 |
  |---------|---------|--------|
  |`content/delete`    | 從存放庫移除資料  | 刪除存放庫或資訊清單 |
  |`content/read`     |  讀取來自存放庫的資料 |  提取成品 |
  |`content/write`     |  將資料寫入至存放庫     | 搭配 `content/read` 使用以推送成品 |
  |`metadata/read`    | 讀取來自存放庫的中繼資料   | 列出標籤或資訊清單 |
  |`metadata/write`     |  將中繼資料寫入至存放庫  | 啟用或停用讀取、寫入或刪除作業 |

* **範圍對應**會將套用至權杖的存放庫權限分組，並可重新套用至其他權杖。 每個權杖都與單一範圍對應相關聯。 

   使用範圍對應：

    * 使用與一組存放庫相同的權限來設定多個權杖
    * 在您新增或移除範圍對應中的存放庫動作，或套用不同的範圍對應時，更新權杖權限 

  Azure Container Registry 也提供數個系統定義的範圍對應，可在建立權杖時套用。 系統定義範圍對應的許可權會套用到您登錄中的所有存放庫。

下圖顯示權杖與範圍對應之間的關聯性。 

![登錄權杖和範圍對應](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Prerequisites

* **Azure CLI** - 在 Azure CLI 版本2.0.76 或更新版本提供建立和管理權杖的 Azure CLI 命令。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。
* **Docker** - 若要向登錄進行驗證以提取或推送映像，您需要本機 Docker 安裝。 Docker 提供 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系統的安裝指示。
* **容器登錄** - 如果您沒有容器登錄，請在您的 Azure 訂閱中建立進階容器登錄，或升級現有的登錄。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。 

## <a name="create-token---cli"></a>建立權杖 - CLI

### <a name="create-token-and-specify-repositories"></a>建立權杖並指定存放庫

使用 [az acr Token create][az-acr-token-create] 命令來建立權杖。 建立權杖時，您可以在每個存放庫上指定一個或多個存放庫和相關聯的動作。 存放庫還不需要在登錄中。 若要藉由指定現有的範圍對應來建立權杖，請參閱 [下一節](#create-token-and-specify-scope-map)。

下列範例會在登錄 *myregistry* 中使用 `samples/hello-world` 存放庫的下列權限建立權杖：`content/write` 和 `content/read`。 根據預設，此命令會將預設權杖狀態設定為 `enabled`，但您可以隨時將狀態更新為 [`disabled`]。

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

輸出會顯示有關權杖的詳細資料。 預設會產生兩個密碼。 建議您將密碼儲存在安全的位置，以供稍後用來進行驗證。 系統無法再次擷取密碼，但可以產生新的密碼。

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

> [!NOTE]
> 如果您想要重新產生權杖密碼並設定密碼到期期間，請參閱本文稍後的 [重新產生權杖密碼](#regenerate-token-passwords) 。

輸出會包含命令所建立的範圍對應詳細資料。 您可以使用這裡命名為 `MyToken-scope-map` 的範圍對應，將相同的存放庫動作套用至其他權杖。 或者，稍後更新範圍對應，以變更相關聯權杖的權限。

### <a name="create-token-and-specify-scope-map"></a>建立權杖並指定範圍對應

建立權杖的另一種方法是指定現有的範圍對應。 如果您還沒有範圍對應，請先指定存放庫和相關聯的動作來建立範圍對應。 然後，在建立權杖時指定範圍對應。 

若要建立範圍對應，請使用 [az acr scope-map create][az-acr-scope-map-create] 命令。 下列命令會在先前使用的 `samples/hello-world` 存放庫上建立具有相同權限的範圍對應。 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

執行 [az acr Token create][az-acr-token-create] 以建立權杖，並指定 *MyScopeMap* 範圍對應。 如先前範例所示，此命令會將預設的權杖狀態設定為 `enabled`。

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

輸出會顯示有關權杖的詳細資料。 預設會產生兩個密碼。 建議您將密碼儲存在安全的位置，以供稍後用來進行驗證。 系統無法再次擷取密碼，但可以產生新的密碼。

> [!NOTE]
> 如果您想要重新產生權杖密碼並設定密碼到期期間，請參閱本文稍後的 [重新產生權杖密碼](#regenerate-token-passwords) 。

## <a name="create-token---portal"></a>建立權杖 - 入口網站

您可以使用 Azure 入口網站來建立權杖和範圍對應。 和使用 `az acr token create` CLI 命令一樣，您可以套用現有的範圍對應，或藉由指定一個或多個存放庫和相關聯的動作，在建立權杖時建立範圍對應。 存放庫還不需要在登錄中。 

下列範例會建立權杖，並在 `samples/hello-world` 存放庫上建立具有下列權限的範圍對應：`content/write` 和 `content/read`。

1. 在入口網站中，瀏覽到您的容器登錄。
1. 在 [存放 **庫許可權**] 下，選取 [ **權杖 (預覽]) > + 新增**]。

      :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-add.png" alt-text="在入口網站中建立權杖 ":::
1. 輸入權杖名稱。
1. 在 [範圍對應] 底下，選取 [新建]。
1. 設定範圍對應：
    1. 輸入範圍對應的名稱及描述。 
    1. 在 [存放庫] 下，輸入 `samples/hello-world`，然後在 [權限] 底下，選取 [`content/read`] 和 [`content/write`]。 然後選取 [+新增]。  

        :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-scope-map-add.png" alt-text="在入口網站中建立權杖 ":::

    1. 新增存放庫和權限之後，請選取 [新增] 以新增範圍對應。
1. 接受 [已啟用] 的預設權杖**狀態**，然後選取 [建立]。

驗證並建立權杖之後，權杖詳細資料會出現在 [權杖] 畫面中。

### <a name="add-token-password"></a>新增權杖密碼

若要使用在入口網站中建立的權杖，您必須產生密碼。 您可以產生一或兩組密碼，並為每組密碼設定一個到期日。 

1. 在入口網站中，瀏覽到您的容器登錄。
1. 在 [存放 **庫許可權**] 下，選取 [ **權杖 (預覽]) **，然後選取權杖。
1. 在權杖詳細資料中，選取 [password1] 或 [password2]，然後選取 [產生] 圖示。
1. 在 [密碼] 畫面中，選擇設定密碼的到期日，然後選取 [產生]。 建議您設定到期日。
1. 產生密碼之後，請複製密碼並儲存到安全的位置。 您無法在關閉畫面後取出產生的密碼，但您可以產生新的密碼。

    :::image type="content" source="media/container-registry-repository-scoped-permissions/portal-token-password.png" alt-text="在入口網站中建立權杖 ":::

## <a name="authenticate-with-token"></a>使用權杖進行驗證

使用者或服務使用權杖向目標登錄進行驗證時，目標登錄會提供權杖名稱做為使用者名稱和其中一個產生的密碼。 

驗證方法取決於已設定的動作或與權杖相關聯的動作。

|動作  |如何驗證  |
  |---------|---------|
  |`content/delete`    | 在 Azure CLI 中的 `az acr repository delete`<br/><br/>範例： `az acr repository delete --name myregistry --repository myrepo --username MyToken --password xxxxxxxxxx`|
  |`content/read`     |  `docker login`<br/><br/>在 Azure CLI 中的 `az acr login`<br/><br/>範例： `az acr login --name myregistry --username MyToken --password xxxxxxxxxx`  |
  |`content/write`     |  `docker login`<br/><br/>在 Azure CLI 中的 `az acr login`     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>在 Azure CLI 中的 `az acr repository show-manifests`   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>在 Azure CLI 中的 `az acr repository update` |

## <a name="examples-use-token"></a>範例：使用權杖

下列範例會使用本文稍早建立的權杖執行存放庫的一般作業：推送和提取映像、刪除映像，以及列出存放庫標籤。 此權杖一開始是以 `samples/hello-world` 存放庫上的推送權限 (`content/write` 和 `content/read` 動作) 設定。

### <a name="pull-and-tag-test-images"></a>提取和標記測試影像

針對下列範例，請從 Docker Hub 提取 `hello-world` 和 `alpine` 映像，並為您的登錄和存放庫標記這些映像。

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>使用權杖進行驗證

執行 `docker login` 或 `az acr login` 向登錄進行驗證，以推送或提取映射。 提供權杖名稱做為使用者名稱，並提供其中一個密碼。 權杖必須具有 `Enabled` 狀態。

下列範例會針對 Bash 殼層進行格式化，並使用環境變數來提供值。

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

輸出應該會顯示成功的驗證：

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>將映像推送到登錄

成功登入之後，嘗試將標記的映像推送至登錄。 因為權杖具有將映像推送至 `samples/hello-world` 存放庫的權限，所以下列推送會成功：

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

權杖沒有 `samples/alpine` 存放庫的權限，因此下列的推送嘗試會失敗，並出現類似 `requested access to the resource is denied`的錯誤：

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="update-token-permissions"></a>更新權杖許可權

若要更新權杖的權限，請更新相關聯範圍對應中的權限。 更新的範圍對應會立即套用至所有相關聯的權杖。 

例如，使用 `samples/alpine` 存放庫上的 `content/write` 和 `content/read` 動作來更新 `MyToken-scope-map`，並移除 `samples/hello-world` 存放庫上的 `content/write` 動作。  

若要使用 Azure CLI，請執行 [az acr scope-map update][az-acr-scope-map-update] 以更新範圍對應：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

在 Azure 入口網站中：

1. 瀏覽至您的容器登錄。
1. 在 [存放 **庫許可權**] 下，選取 [ **範圍對應] (預覽) **，然後選取要更新的範圍對應。
1. 在 [存放庫] 下，輸入 `samples/alpine`，然後在 [權限] 底下，選取 [`content/read`] 和 [`content/write`]。 然後選取 [+新增]。
1. 在 [存放庫] 底下，選取 [`samples/hello-world`]，然後在 [權限] 下，取消選取 `content/write`。 然後選取 [儲存]。

更新範圍對應之後，下列推送會成功：

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

因為範圍對應只有 `samples/hello-world` 存放庫的 `content/read` 權限，所以對 `samples/hello-world` 存放庫的推送嘗試目前會失敗：
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

從這兩個存放庫提取映像都會成功，因為範圍對應會提供這兩個存放庫的 `content/read` 權限：

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>刪除映像

藉由將 `content/delete` 動作新增至 `alpine` 存放庫來更新範圍對應。 此動作可讓您刪除存放庫中的映像，或刪除整個存放庫。

為求簡潔，我們只會顯示 [az acr scope map update][az-acr-scope-map-update] 命令來更新範圍對應：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

若要使用入口網站更新範圍對應，請參閱 [上一節](#update-token-permissions)。

使用下列 [az acr repository delete][az-acr-repository-delete] 命令刪除 `samples/alpine` 存放庫。 若要刪除映射或存放庫，請將權杖的名稱和密碼傳遞至命令。 下列範例會使用稍早在本文中建立的環境變數：

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>顯示存放庫標籤 

藉由將 `metadata/read` 動作新增至 `hello-world` 存放庫來更新範圍對應。 此動作可讓您讀取存放庫中的資訊清單和標記資料。

為求簡潔，我們只會顯示 [az acr scope map update][az-acr-scope-map-update] 命令來更新範圍對應：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

若要使用入口網站更新範圍對應，請參閱 [上一節](#update-token-permissions)。

若要讀取 `samples/hello-world` 存放庫中的中繼資料，請執行 [az acr repository show-manifests][az-acr-repository-show-manifests] 或 [az acr repository show-tags][az-acr-repository-show-tags] 命令。 

若要讀取中繼資料，請將權杖的名稱和密碼傳遞至任一命令。 下列範例會使用稍早在本文中建立的環境變數：

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

範例輸出：

```console
[
  "v1"
]
```

## <a name="manage-tokens-and-scope-maps"></a>管理權杖和範圍對應

### <a name="list-scope-maps"></a>列出範圍對應

使用 [az acr scope map list][az-acr-scope-map-list] 命令，或入口網站中的 [範圍對應 (預覽)] 畫面，列出登錄中設定的所有範圍對應。 例如：

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

輸出包含三個系統定義的範圍對應和您所產生的其他範圍對應。 您可以使用任何一種範圍對應來設定權杖。

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>顯示權杖詳細資料

若要查看權杖的詳細資料 (例如其狀態和密碼到期日)，請執行 [az acr Token show][az-acr-token-show] 命令，或在入口網站的 [權杖 (預覽)] 畫面中選取權杖。 例如：

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

使用 [az acr Token list][az-acr-token-list] 命令，或入口網站中的 [權杖 (預覽)] 畫面，列出登錄中設定的所有權杖。 例如：

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="regenerate-token-passwords"></a>重新產生權杖密碼

如果您未產生權杖密碼，或想要產生新的密碼，請執行 [az acr token credential credential 產生][az-acr-token-credential-generate] 命令。 

下列範例會針對 *MyToken* 權杖產生新的 password1 值，並在 30 天內到期。 這會將密碼儲存在環境變數 `TOKEN_PWD` 中。 此範例會針對 Bash Shell 加以格式化。

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

若要使用 Azure 入口網站產生權杖密碼，請參閱本文稍早 [建立權杖 - 入口網站](#create-token---portal)中的步驟。

### <a name="update-token-with-new-scope-map"></a>使用新的範圍對應更新權杖

如果您想要使用不同的範圍對應來更新權杖，請執行 [az acr Token update][az-acr-token-update] 並指定新的範圍對應。 例如：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

在入口網站的 [權杖 (預覽)] 畫面上，選取權杖，然後在 [範圍對應] 底下，選取不同的範圍對應。

> [!TIP]
> 使用新的範圍對應來更新權杖之後，您可能會想要產生新的權杖密碼。 使用 [az acr token credential generate][az-acr-token-credential-generate] 命令，或在 Azure 入口網站中重新產生權杖密碼。

## <a name="disable-or-delete-token"></a>停用或刪除權杖

您可能需要暫時停用使用者或服務的權杖認證。 

使用 Azure CLI，執行 [az acr Token update][az-acr-token-update] 命令，將 `status` 設定為 `disabled`：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

在入口網站中，選取 [權杖 (預覽)] 畫面中的權杖，然後選取 [狀態] 底下的 [已停用]。

若要刪除權杖以使用其認證讓任何人永久失效存取，請執行 [az acr Token delete][az-acr-token-delete] 命令。 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

在入口網站中，選取 [權杖 (預覽)] 畫面中的權杖，然後選取 [捨棄]。

## <a name="next-steps"></a>後續步驟

* 若要管理範圍對應和權杖，請使用 [az acr scope-map][az-acr-scope-map] 和 [az acr Token][az-acr-token] 命令群組中的其他命令。
* 如需向 Azure 容器登錄進行驗證的其他選項，包括使用 Azure Active Directory 身分識別、服務主體或系統管理員帳戶，請參閱[驗證概觀](container-registry-authentication.md)。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
