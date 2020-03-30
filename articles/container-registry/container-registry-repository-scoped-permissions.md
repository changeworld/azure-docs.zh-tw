---
title: Azure 容器註冊表中存儲庫的許可權
description: 創建具有限定到註冊表中特定存儲庫的許可權的權杖，以拉取或推送圖像，或執行其他操作
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444270"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>使用存儲庫範圍許可權創建權杖

本文介紹如何創建權杖和範圍映射來管理容器註冊表中的存儲庫範圍許可權。 通過創建權杖，註冊表擁有者可以為使用者或服務提供具有作用域、有時間限制的存儲庫存取權限，以提取或推送圖像或執行其他操作。 權杖提供的許可權比其他註冊表[身份驗證選項](container-registry-authentication.md)更多，這些選項將許可權範圍限定在整個註冊表中。 

創建權杖的方案包括：

* 允許具有單個權杖的 IoT 設備從存儲庫中提取映射
* 向外部組織提供對特定存儲庫的許可權 
* 限制對組織中不同使用者組的存儲庫訪問。 例如，為構建面向特定存儲庫的圖像的開發人員提供寫入和讀取存取許可權，以及對這些存儲庫部署的團隊的讀取存取許可權。

> [!IMPORTANT]
> 此功能目前在預覽階段，但[有某些限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="preview-limitations"></a>預覽限制

* 此功能僅在**高級**容器註冊表中可用。 有關註冊表服務層和限制的資訊，請參閱[Azure 容器註冊表 SKU](container-registry-skus.md)。
* 當前無法將存儲庫範圍的許可權分配給 Azure 活動目錄標識，例如服務主體或託管標識。

## <a name="concepts"></a>概念

要配置存儲庫範圍的許可權，請使用關聯的*作用域映射*創建*權杖*。 

* **權杖**以及生成的密碼允許使用者使用註冊表進行身份驗證。 您可以為權杖密碼設置到期日期，或隨時禁用權杖。  

  使用權杖進行身份驗證後，使用者或服務可以執行一個或多個限定到一個或多個存儲庫*的操作*。

  |動作  |描述  | 範例 |
  |---------|---------|--------|
  |`content/delete`    | 從存儲庫中刪除資料  | 刪除存儲庫或清單 |
  |`content/read`     |  從存儲庫讀取資料 |  拉一個工件 |
  |`content/write`     |  將資料寫入存儲庫     | 用於`content/read`推送專案 |
  |`metadata/read`    | 從存儲庫讀取中繼資料   | 列出標記或清單 |
  |`metadata/write`     |  將中繼資料寫入存儲庫  | 啟用或禁用讀取、寫入或刪除操作 |

* **範圍映射**將應用於權杖的存儲庫許可權分組，並可以重新應用於其他權杖。 每個權杖都與單個作用域映射相關聯。 

   使用範圍映射：

    * 配置多個具有與一組存儲庫具有相同許可權的權杖
    * 在範圍映射中添加或刪除存儲庫操作或應用其他範圍映射時更新權杖許可權 

  Azure 容器註冊表還提供多個系統定義的作用域映射，您可以應用，並對所有存儲庫具有固定許可權。

下圖顯示了權杖和範圍映射之間的關係。 

![註冊表權杖和範圍映射](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Prerequisites

* **Azure CLI** - Azure CLI 命令用於創建和管理權杖，可在 Azure CLI 版本 2.0.76 或更高版本中提供。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。
* **Docker** - 要使用註冊表進行身份驗證以拉取或推送映射，您需要本地 Docker 安裝。 Docker 提供 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系統的安裝指示。
* **容器註冊表**- 如果沒有，請在 Azure 訂閱中創建高級容器註冊表，或升級現有註冊表。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。 

## <a name="create-token---cli"></a>創建權杖 - CLI

### <a name="create-token-and-specify-repositories"></a>創建權杖並指定存儲庫

使用[az acr 權杖創建命令創建][az-acr-token-create]權杖。 創建權杖時，可以在每個存儲庫上指定一個或多個存儲庫和相關操作。 存儲庫不需要在註冊表中。 要通過指定現有範圍映射來創建權杖，請參閱下一節。

下面的示例在註冊表*myregistry*中創建一個權杖，該權杖具有`samples/hello-world`以下對回購`content/write`的許可權`content/read`： 和 。 預設情況下，該命令將預設權杖狀態設置為`enabled`，但您可以隨時將狀態更新為`disabled`。

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

輸出顯示有關權杖的詳細資訊，包括兩個生成的密碼。 建議將密碼保存在安全的地方，以便以後用於身份驗證。 無法再次檢索密碼，但可以生成新密碼。

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

輸出包括有關創建命令的範圍映射的詳細資訊。 您可以使用此處命名為`MyToken-scope-map`的範圍映射將相同的存儲庫操作應用於其他權杖。 或者，稍後更新範圍映射以更改關聯權杖的許可權。

### <a name="create-token-and-specify-scope-map"></a>創建權杖並指定範圍映射

創建權杖的另一種方法是指定現有範圍映射。 如果還沒有範圍映射，請首先通過指定存儲庫和相關操作創建一個範圍映射。 然後，在創建權杖時指定範圍映射。 

要創建範圍映射，請使用[az acr 範圍映射創建][az-acr-scope-map-create]命令。 以下命令在以前使用的`samples/hello-world`存儲庫上創建具有相同許可權的範圍映射。 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

運行[創建 az acr 權杖][az-acr-token-create]以創建權杖，指定*MyScopeMap*範圍映射。 與前面的示例中一樣，該命令將預設權杖狀態設置為`enabled`。

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

輸出顯示有關權杖的詳細資訊，包括兩個生成的密碼。 建議將密碼保存在安全的地方，以便以後用於身份驗證。 無法再次檢索密碼，但可以生成新密碼。

## <a name="create-token---portal"></a>創建權杖 - 門戶

可以使用 Azure 門戶創建權杖和範圍映射。 與`az acr token create`CLI 命令一樣，您可以應用現有範圍映射，或者通過指定一個或多個存儲庫和相關操作來創建權杖時創建範圍映射。 存儲庫不需要在註冊表中。 

下面的示例創建一個權杖，並創建一個作用域映射，該映射在`samples/hello-world`存儲庫上具有`content/write`以下`content/read`許可權： 和 。

1. 在門戶中，導航到容器註冊表。
1. 在 **"服務**"下，選擇**標記（預覽）> +添加**。
  ![在門戶中創建權杖](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. 輸入權杖名稱。
1. 在 **"範圍"映射**下，選擇 **"創建新**"。
1. 配置範圍映射：
    1. 輸入範圍映射的名稱和說明。 
    1. 在 **"存儲庫**"下，輸入`samples/hello-world`和 **"許可權**"`content/read`下`content/write`，選擇 和 。 然後選擇 **+添加**。  
    ![在門戶中創建範圍映射](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. 添加存儲庫和許可權後，選擇 **"添加"** 以添加範圍映射。
1. 接受預設權杖 **"已啟用****"狀態**，然後選擇 **"創建**"。

驗證並創建權杖後，權杖詳細資訊將顯示在 **"權杖"** 螢幕中。

### <a name="add-token-password"></a>添加權杖密碼

創建權杖後生成密碼。 要對註冊表進行身份驗證，必須啟用權杖並具有有效的密碼。

您可以生成一個或兩個密碼，並為每個密碼設置到期日期。 

1. 在門戶中，導航到容器註冊表。
1. 在 **"服務**"下，選擇**權杖（預覽），** 然後選擇權杖。
1. 在權杖詳細資訊中，選擇**密碼1**或**密碼2，** 然後選擇"生成"圖示。
1. 在密碼螢幕中，可以選擇設置密碼的到期日期，然後選擇 **"生成**"。
1. 生成密碼後，將其複製並保存到安全位置。 關閉螢幕後無法檢索生成的密碼，但可以生成新密碼。

    ![在門戶中創建權杖密碼](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>使用權杖進行身份驗證

當使用者或服務使用權杖對目標注冊表進行身份驗證時，它將權杖名稱作為使用者名及其生成的密碼之一提供。 身份驗證方法取決於配置的操作或與權杖關聯的操作。

|動作  |如何進行身份驗證  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`在 Azure CLI 中 |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`在 Azure CLI 中  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`在 Azure CLI 中     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`在 Azure CLI 中   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`在 Azure CLI 中 |

## <a name="examples-use-token"></a>示例：使用權杖

以下示例使用本文前面創建的權杖對存儲庫執行常見操作：推送和拉取映射、刪除映射和清單存儲庫標記。 權杖最初在`content/write``samples/hello-world`存儲庫上使用推送許可權 （和`content/read`操作）進行設置。

### <a name="pull-and-tag-test-images"></a>拉取和標記測試圖像

對於以下示例，請從`hello-world`Docker `alpine` Hub 中提取 和 映射，並為註冊表和存儲庫標記它們。

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>使用權杖進行身份驗證

運行`docker login`以使用註冊表進行身份驗證，將權杖名稱作為使用者名提供，並提供其密碼之一。 權杖必須具有狀態`Enabled`。

以下示例為 bash 外殼設置格式，並使用環境變數提供值。

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

輸出應顯示成功的身份驗證：

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>將映像推送到登錄

成功登錄後，嘗試將標記的圖像推送到註冊表。 由於權杖具有將映射推送到`samples/hello-world`存儲庫的許可權，因此以下推送成功：

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

權杖對`samples/alpine`回購沒有許可權，因此以下推送嘗試失敗，錯誤類似于`requested access to the resource is denied`：

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>更改推送/拉取許可權

要更新權杖的許可權，請更新關聯作用域映射中的許可權。 更新的範圍映射將立即應用於所有關聯的權杖。 

例如`MyToken-scope-map`，在`content/write``content/read``samples/alpine`存儲庫上更新 和 操作，並刪除存儲庫`content/write`上`samples/hello-world`的操作。  

要使用 Azure CLI，請運行[az acr 範圍映射更新][az-acr-scope-map-update]以更新範圍映射：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

在 Azure 入口網站中：

1. 導航到容器註冊表。
1. 在 **"服務**"下，選擇 **"範圍映射"（預覽），** 然後選擇要更新的範圍映射。
1. 在 **"存儲庫**"下，輸入`samples/alpine`和 **"許可權**"`content/read`下`content/write`，選擇 和 。 然後選擇 **+添加**。
1. 在 **"存儲庫**"下，選擇`samples/hello-world`和 下 **"許可權**"取消選擇`content/write`。 然後選擇 **"保存**"。

更新範圍映射後，以下推送將成功：

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

由於範圍映射僅在`content/read``samples/hello-world`存儲庫上具有許可權，因此推送到`samples/hello-world`存儲庫的嘗試現在失敗：
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

從兩個存儲庫中拉取圖像成功，因為範圍映射為`content/read`兩個存儲庫提供了許可權：

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>刪除映像

通過將操作添加到`content/delete``alpine`存儲庫來更新範圍映射。 此操作允許刪除存儲庫中的圖像，或刪除整個存儲庫。

為簡潔起見，我們僅顯示用於更新作用域映射的[az acr 範圍映射更新][az-acr-scope-map-update]命令：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

要使用門戶更新範圍映射，請參閱上一節。

使用以下[az acr 存儲庫刪除][az-acr-repository-delete]命令刪除`samples/alpine`存儲庫。 要刪除圖像或存儲庫，權杖不會通過`docker login`進行身份驗證。 相反，將權杖的名稱和密碼傳遞給命令。 下面的示例使用本文前面創建的環境變數：

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>顯示存儲庫標記 

通過將操作添加到`metadata/read``hello-world`存儲庫來更新範圍映射。 此操作允許在存儲庫中讀取清單和標記資料。

為簡潔起見，我們僅顯示用於更新作用域映射的[az acr 範圍映射更新][az-acr-scope-map-update]命令：

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

要使用門戶更新範圍映射，請參閱上一節。

要讀取存儲庫中的`samples/hello-world`中繼資料，請運行[az acr 存儲庫顯示清單][az-acr-repository-show-manifests]或 az [acr 存儲庫顯示標記][az-acr-repository-show-tags]命令。 

要讀取中繼資料，權杖不會通過`docker login`進行身份驗證。 相反，將權杖的名稱和密碼傳遞給任一命令。 下面的示例使用本文前面創建的環境變數：

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
## <a name="manage-tokens-and-scope-maps"></a>管理權杖和範圍映射

### <a name="list-scope-maps"></a>列出範圍映射

使用[az acr 範圍映射清單][az-acr-scope-map-list]命令或閘戶中的 **"範圍映射（預覽）"** 螢幕列出註冊表中配置的所有作用域映射。 例如：

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

輸出顯示您定義的範圍映射以及可用於配置權杖的多個系統定義的範圍映射：

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>顯示權杖詳細資訊

要查看權杖的詳細資訊（如其狀態和密碼到期日期），請運行[az acr 權杖顯示][az-acr-token-show]命令，或在門戶中的 **"權杖（預覽）"** 螢幕中選擇權杖。 例如：

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

使用[az acr 權杖清單][az-acr-token-list]命令或閘戶中的**權杖（預覽）** 螢幕列出註冊表中配置的所有權杖。 例如：

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>生成權杖密碼

如果您沒有權杖密碼，或者想要生成新密碼，請運行[az acr 權杖憑據生成][az-acr-token-credential-generate]命令。 

下面的示例為*MyToken 權杖*生成密碼 1 的新值，過期期為 30 天。 它將密碼存儲在環境變數`TOKEN_PWD`中。 此示例為 bash 外殼格式化。

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

要使用 Azure 門戶生成權杖密碼，請參閱本文前面創建[權杖門戶](#create-token---portal)中的步驟。

### <a name="update-token-with-new-scope-map"></a>使用新範圍映射更新權杖

如果要使用不同的作用域映射更新權杖，請運行[az acr 權杖更新][az-acr-token-update]並指定新的作用域映射。 例如：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

在門戶中，在 **"權杖（預覽）"** 螢幕上，選擇權杖，並在 **"範圍"映射**下選擇其他範圍映射。

> [!TIP]
> 使用新的作用域映射更新權杖後，可能需要生成新的權杖密碼。 使用[az acr 權杖憑據生成][az-acr-token-credential-generate]命令或在 Azure 門戶中重新生成權杖密碼。

## <a name="disable-or-delete-token"></a>禁用或刪除權杖

您可能需要暫時禁用使用者或服務的權杖憑據的使用。 

使用 Azure CLI，運行[az acr 權杖更新][az-acr-token-update]命令`status`以`disabled`設置為 ：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

在門戶中，在 **"權杖（預覽）"** 螢幕中選擇權杖，並在 **"狀態**"下選擇 **"禁用**"。

要刪除權杖以永久使使用其憑據的任何人的訪問無效，請運行[az acr 權杖刪除][az-acr-token-delete]命令。 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

在門戶中，在 **"權杖（預覽）"** 螢幕中選擇權杖，然後選擇 **"丟棄**"。

## <a name="next-steps"></a>後續步驟

* 要管理作用域映射和權杖，請使用[az acr 作用域映射][az-acr-scope-map]和 az [acr 權杖][az-acr-token]命令組中的其他命令。
* 有關使用 Azure 容器註冊表進行身份驗證的其他選項（包括使用 Azure 活動目錄標識、服務主體或管理員帳戶），請參閱[身份驗證概述](container-registry-authentication.md)。


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
