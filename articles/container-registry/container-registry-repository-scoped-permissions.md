---
title: Azure 容器註冊表中儲存函式庫權限
description: 建立具有限定到註冊表中特定儲存庫的權限的權杖,以拉取或推送影像,或執行其他操作
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 9004c45401833d3070266055dd7eb99a2bb43bde
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618835"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>使用儲存函式庫範圍權限建立權杖

本文介紹如何創建權杖和範圍映射來管理容器註冊表中的存儲庫範圍許可權。 通過創建權杖,註冊表擁有者可以為使用者或服務提供具有作用域、有時間限制的儲存庫存取許可權,以提取或推送圖像或執行其他操作。 令牌提供的許可權比其他註冊表[身份驗證選項](container-registry-authentication.md)更多,這些選項將許可權範圍限定在整個註冊表中。 

建立權杖的專案包括:

* 允許具有單一牌的 IoT 裝置從儲存庫抽取映像
* 向外部組織提供對特定儲存函式庫的權限 
* 限制對組織中不同使用者組的存儲庫訪問。 例如,為構建面向特定儲存庫的圖像的開發人員提供寫入和讀取存取許可權,以及對這些儲存庫部署的團隊的讀取存取許可權。

> [!IMPORTANT]
> 此功能目前在預覽階段，但[有某些限制](#preview-limitations)。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

## <a name="preview-limitations"></a>預覽限制

* 此功能僅在**先進**容器註冊表中可用。 有關註冊表服務層和限制的資訊,請參閱[Azure 容器註冊表 SKU](container-registry-skus.md)。
* 當前無法將儲存庫範圍的許可權分配給 Azure 活動目錄標識,例如服務主體或託管標識。
* 不能在註冊表中為[匿名拉取訪問](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)創建範圍映射。

## <a name="concepts"></a>概念

要設定儲存函式庫範圍的權限,請使用關聯的*作用網域映射*建立*權杖*。 

* **令牌**以及生成的密碼允許使用者使用註冊表進行身份驗證。 您可以為權杖密碼設定到期日期,或隨時禁用權杖。  

  使用權杖進行身份驗證後,使用者或服務可以執行一個或多個限定到一個或多個儲存庫*的操作*。

  |動作  |描述  | 範例 |
  |---------|---------|--------|
  |`content/delete`    | 從儲存庫中移除資料  | 刪除儲存函式庫或清單 |
  |`content/read`     |  從儲存的庫讀取資料 |  拉一個工件 |
  |`content/write`     |  將資料寫入儲存函式庫     | 推`content/read`送項目 |
  |`metadata/read`    | 從儲存的庫讀取中繼資料   | 列出標記或清單 |
  |`metadata/write`     |  將中繼資料寫入儲存函式庫  | 開啟或關閉讀取、寫入或移除操作 |

* **範圍映射**將應用於權杖的儲存庫許可權分組,並可以重新應用於其他權杖。 每個令牌都與單個作用域映射相關聯。 

   使用範圍對應:

    * 設定多個具有與一組儲存庫具有相同權限的權杖
    * 在範圍映射中新增或移除儲存函式庫操作或應用其他範圍對應時更新權杖權限 

  Azure 容器註冊表還提供多個系統定義的作用域映射,您可以應用,並對所有存儲庫具有固定許可權。

下圖顯示了令牌和範圍映射之間的關係。 

![註冊表權和範圍對應](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Prerequisites

* **Azure CLI** - Azure CLI 命令用於建立和管理權杖,可在 Azure CLI 版本 2.0.76 或更高版本中提供。 執行 `az --version` 以尋找版本。 如果需要安裝或升級,請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。
* **Docker** - 要使用註冊表進行身份驗證以拉取或推送映射,您需要本地 Docker 安裝。 Docker 提供 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系統的安裝指示。
* **容器註冊表**- 如果沒有,請在 Azure 訂閱中創建高級容器註冊表,或升級現有註冊表。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。 

## <a name="create-token---cli"></a>建立權杖 - CLI

### <a name="create-token-and-specify-repositories"></a>建立權杖並指定儲存函式庫

使用[az acr 權杖建立命令建立][az-acr-token-create]權杖。 建立權杖時,可以在每個儲存庫上指定一個或多個儲存庫和相關操作。 存儲庫不需要在註冊表中。 要通過指定現有範圍映射來創建權杖,請參閱下一節。

下面的範例在註冊表*myregistry*中建立一個權杖,該權`samples/hello-world`杖具有以下`content/write`對回購`content/read`的權限 : 和 。 預設情況下,該命令將預設權杖狀態設定為`enabled`,但您可以隨時將狀態更新`disabled`為 。

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

輸出顯示有關權杖的詳細資訊,包括兩個生成的密碼。 建議將密碼保存在安全的地方,以便以後用於身份驗證。 無法再次檢索密碼,但可以生成新密碼。

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

輸出包括有關創建命令的範圍映射的詳細資訊。 您可以使用此處命名為`MyToken-scope-map`的範圍映射將相同的儲存庫操作應用於其他權杖。 或者,稍後更新範圍映射以更改關聯權杖的許可權。

### <a name="create-token-and-specify-scope-map"></a>建立權杖並指定範圍對應

建立權杖的另一種方法是指定現有範圍映射。 如果還沒有範圍映射,請首先通過指定存儲庫和相關操作創建一個範圍映射。 然後,在創建權杖時指定範圍映射。 

要建立範圍映射,請使用[az acr 範圍映射建立][az-acr-scope-map-create]命令。 以下命令在以前使用的`samples/hello-world`儲存庫上創建具有相同許可權的範圍映射。 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

執行[創建 az acr 權杖][az-acr-token-create]以建立權杖,指定*MyScopeMap*範圍映射。 與前面的範例中一樣,這個指令將預設權杖狀態設定為`enabled`。

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

輸出顯示有關權杖的詳細資訊,包括兩個生成的密碼。 建議將密碼保存在安全的地方,以便以後用於身份驗證。 無法再次檢索密碼,但可以生成新密碼。

## <a name="create-token---portal"></a>建立權杖 - 門戶

可以使用 Azure 門戶創建權杖和範圍映射。 與`az acr token create`CLI 命令一樣,您可以應用現有範圍映射,或者通過指定一個或多個儲存庫和相關操作來創建權杖時創建範圍映射。 存儲庫不需要在註冊表中。 

下面的範例建立一個權杖,並建立一個作用網域映射,該映射`samples/hello-world`在儲存庫上`content/write`具有`content/read`以下 許可權: 和 。

1. 在門戶中,導航到容器註冊表。
1. 在 **"服務**')下,選擇**標記(預覽)>+添加**。
  ![在門戶建立權杖](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. 輸入權杖名稱。
1. 在 **「範圍」映射**下,選擇 **「創建新**」。。
1. 設定範圍對應:
    1. 輸入範圍映射的名稱和說明。 
    1. 在**儲存的庫**的儲存`samples/hello-world`與 **「權限**`content/read`」`content/write`, 選擇與 。 然後選擇 **+新增**。  
    ![在門戶中建立範圍映射](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. 新增儲存函式庫和權限後,選擇 **「添加」** 以新增範圍映射。
1. 接受預設權杖 **「已啟用****」狀態**,然後選擇 **「創建**」 。

驗證並建立權杖後,讓牌詳細資訊將顯示在 **「權杖」** 螢幕中。

### <a name="add-token-password"></a>新增權杖密碼

建立權杖後生成密碼。 要對註冊表進行身份驗證,必須啟用權杖並具有有效的密碼。

您可以生成一個或兩個密碼,並為每個密碼設置到期日期。 

1. 在門戶中,導航到容器註冊表。
1. 在 **"服務**"下,選擇**權杖(預覽),** 然後選擇權杖。
1. 在權杖詳細資訊中,選擇**密碼1**或**密碼2,** 然後選擇"生成"圖示。
1. 在密碼螢幕中,可以選擇設置密碼的到期日期,然後選擇 **「生成**」。
1. 生成密碼后,將其複製並保存到安全位置。 關閉螢幕後無法檢索生成的密碼,但可以生成新密碼。

    ![在門戶建立權杖密碼](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>使用權杖進行認證

當使用者或服務使用權杖對目標註冊表進行身份驗證時,它將令牌名稱作為使用者名及其生成的密碼之一提供。 身份驗證方法取決於配置的操作或與權杖關聯的操作。

|動作  |如何進行身份驗證  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`Azure CLI 中 |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`Azure CLI 中  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`Azure CLI 中     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`Azure CLI 中   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`Azure CLI 中 |

## <a name="examples-use-token"></a>範例:使用權杖

以下範例使用本文前面創建的權杖對儲存庫執行常見操作:推送和拉取映射、刪除映射和清單儲存庫標記。 令牌最初在`content/write``samples/hello-world`儲存庫上使用推送許可權 (`content/read`和操作)進行設置。

### <a name="pull-and-tag-test-images"></a>拉取與標記測試影像

對於以下範例,請從`hello-world`Docker `alpine` Hub 中提取和映像,並為註冊表和儲存庫標記它們。

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>使用權杖進行認證

運行`docker login`以使用註冊表進行身份驗證,將令牌名稱作為使用者名提供,並提供其密碼之一。 權杖必須具有狀態`Enabled`。

以下範例為 bash 外殼設定格式,並使用環境變數提供值。

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

輸出應顯示成功的身份驗證:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>將映像推送到登錄

成功登錄后,嘗試將標記的圖像推送到註冊表。 由於權杖具有將映射推送到`samples/hello-world`儲存函式庫的權限,因此以下推送成功:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

權杖對`samples/alpine`回購沒有權限,因此以下推送嘗試失敗,錯誤`requested access to the resource is denied`類似於 :

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>變更推送/拉取權限

要更新權杖的許可權,請更新關聯作用域映射中的許可權。 更新的範圍映射將立即應用於所有關聯的權杖。 

例如`MyToken-scope-map`,`content/write``content/read``samples/alpine`在儲存庫上更新和操作,並刪除儲存`content/write`庫`samples/hello-world`上的操作。  

要使用 Azure CLI,請執行[az acr 範圍映射更新][az-acr-scope-map-update]以更新範圍映射:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

在 Azure 入口網站中：

1. 導航到容器註冊表。
1. 在 **"服務**"下,選擇 **"範圍映射"(預覽),** 然後選擇要更新的範圍映射。
1. 在**儲存的庫**的儲存`samples/alpine`與 **「權限**`content/read`」`content/write`, 選擇與 。 然後選擇 **+新增**。
1. 在**儲存的庫的儲存**函式`samples/hello-world`, 選擇與下 **「權限**」取消選擇`content/write`。 然後選擇 **「保存**」。

更新範圍映射後,以下推送將成功:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

由於範圍映射僅在`content/read``samples/hello-world`儲存庫上具有許可權,因此推`samples/hello-world`送到 儲存庫的嘗試現在失敗:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

從兩個儲存庫中拉取影像成功,因為範圍映射為`content/read`兩個儲存庫提供了許可權:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>刪除映像

通過將操作添加到`content/delete``alpine`儲存庫來更新範圍映射。 此操作允許刪除儲存庫中的圖像,或刪除整個儲存庫。

為簡潔起見,我們僅顯示用於更新作用域映射的[az acr 範圍映射更新][az-acr-scope-map-update]命令:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

要使用門戶更新範圍映射,請參閱上一節。

使用以下[az acr 儲存函式庫刪除][az-acr-repository-delete]命令刪除`samples/alpine`儲存庫。 要刪除圖像或儲存庫,令牌不會通過`docker login`進行身份驗證。 相反,將令牌的名稱和密碼傳遞給命令。 下面的範例使用本文前面建立的環境變數:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>顯示儲存函式庫標記 

通過將操作添加到`metadata/read``hello-world`儲存庫來更新範圍映射。 此操作允許在存儲庫中讀取清單和標記數據。

為簡潔起見,我們僅顯示用於更新作用域映射的[az acr 範圍映射更新][az-acr-scope-map-update]命令:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

要使用門戶更新範圍映射,請參閱上一節。

要讀取儲存庫中的`samples/hello-world`中繼資料,請執行[az acr 儲存庫顯示清單][az-acr-repository-show-manifests]或 az [acr 儲存庫顯示標記][az-acr-repository-show-tags]命令。 

要讀取元數據,令牌不會通過`docker login`進行身份驗證。 相反,將令牌的名稱和密碼傳遞給任一命令。 下面的範例使用本文前面建立的環境變數:

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
## <a name="manage-tokens-and-scope-maps"></a>管理權杖與範圍對應

### <a name="list-scope-maps"></a>列出範圍對應

使用[az acr 範圍映射清單][az-acr-scope-map-list]命令或門戶中的 **「範圍映射(預覽)」** 螢幕列出註冊表中設定的所有作用域映射。 例如：

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

輸出顯示您定義的範圍對應及可設定權杖的多個系統定義的範圍對應:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>顯示權杖詳細資訊

要檢視權杖的詳細資訊(如其狀態和密碼到期日期),請執行[az acr 權杖顯示][az-acr-token-show]命令,或在門戶中的 **「權杖(預覽)」** 螢幕中選擇權杖。 例如：

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

使用[az acr 權杖清單][az-acr-token-list]命令或門戶中的**權杖(預覽)** 螢幕列出註冊表中配置的所有權杖。 例如：

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>產生權杖密碼

如果您沒有權杖密碼,或者想要生成新密碼,請執行[az acr 權杖認證產生指令][az-acr-token-credential-generate]。 

下面的示例為*MyToken 權杖*生成密碼 1 的新值,過期期為 30 天。 它將密碼存儲在環境變數`TOKEN_PWD`中。 此示例為 bash 外殼格式化。

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

要使用 Azure 門戶生成權杖密碼,請參閱本文前面創建[令牌門戶](#create-token---portal)中的步驟。

### <a name="update-token-with-new-scope-map"></a>使用新範圍對應更新權杖

如果要使用不同的作用域映射更新權杖,請運行[az acr 令牌更新][az-acr-token-update]並指定新的作用域映射。 例如：

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

在門戶中,在 **「權杖(預覽)」** 螢幕上,選擇權杖,並在 **「範圍」映射**下選擇其他範圍映射。

> [!TIP]
> 使用新的作用域映射更新權杖後,可能需要生成新的權杖密碼。 使用[az acr 權杖認證為 azure][az-acr-token-credential-generate]入口中重新產生權杖密碼。

## <a name="disable-or-delete-token"></a>關閉或移除權杖

您可能需要暫時禁用使用者或服務的權杖認證的使用。 

使用 Azure CLI,執行[az acr 權碼更新][az-acr-token-update]指令`status`以`disabled`設定為 :

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

在門戶中,在 **「權杖(預覽)」** 螢幕中選擇權杖,並在 **「狀態**」下選擇 **「禁用**」。

要刪除權杖以永久使使用其憑據的任何人的訪問無效,請運行[az acr 權杖刪除][az-acr-token-delete]命令。 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

在門戶中,在 **「權杖(預覽)」** 螢幕中選擇權杖,然後選擇 **「丟棄**」。

## <a name="next-steps"></a>後續步驟

* 要管理作用網域對應和權杖,請使用[az acr 作用網域映射][az-acr-scope-map]和 az [acr 權杖][az-acr-token]命令列中的其他指令。
* 有關使用 Azure 容器註冊表進行身份驗證的其他選項(包括使用 Azure 活動目錄識別、服務主體或管理者帳戶),請參閱[身份驗證概述](container-registry-authentication.md)。


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
