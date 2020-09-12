---
title: 推送和提取 OCI 構件
description: 使用 Azure 中的私人容器登錄，推送和提取開放式容器計畫 (OCI) 構件
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 08/12/2020
ms.author: stevelas
ms.openlocfilehash: 7c95766cc12b281521fa52ab113fadd4321d0815
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89484998"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>使用 Azure container registry 推送和提取 OCI 成品

您可以使用 Azure container registry 來儲存和管理 [Open Container 方案 (OCI) 構件](container-registry-image-formats.md#oci-artifacts) ，以及 Docker 和 docker 相容容器映射。

為了示範這項功能，本文將說明如何使用 [OCI 登錄作為儲存體 (ORAS) ](https://github.com/deislabs/oras) 工具將範例成品（文字檔）推送至 Azure container Registry。 然後，從登錄中提取成品。 您可以使用適用于每個成品的不同命令列工具，在 Azure container registry 中管理各種 OCI 構件。

## <a name="prerequisites"></a>必要條件

* **Azure container registry** -在您的 azure 訂用帳戶中建立容器登錄。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。
* **ORAS 工具** -從 [GitHub](https://github.com/deislabs/oras/releases)存放庫下載並安裝適用于您作業系統的目前 ORAS 版本。 此工具會以壓縮的 tarball 形式發行 (檔案 `.tar.gz`) 。 使用適用于您作業系統的標準程式來解壓縮和安裝檔案。
* **Azure Active Directory 服務主體 (選擇性) ** -若要直接使用 ORAS 進行驗證，請建立 [服務主體](container-registry-auth-service-principal.md) 以存取您的登錄。 確定服務主體已獲指派角色（例如 AcrPush），讓它有權推送和提取成品。
* **Azure CLI (選擇性) ** -若要使用個別的身分識別，您需要本機安裝 Azure CLI。 建議使用2.0.71 版或更新版本。 執行 `az --version ` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。
* **Docker (選擇性) ** -若要使用個別身分識別，您也必須在本機安裝 docker，才能向登錄進行驗證。 Docker 提供可輕鬆在任何 [macOS][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系統上設定 Docker 的套件。


## <a name="sign-in-to-a-registry"></a>登入登錄

本節根據所使用的身分識別，顯示登入登錄的兩個建議工作流程。 選擇適合您環境的方法。

### <a name="sign-in-with-oras"></a>使用 ORAS 登入

使用具有推播許可權的 [服務主體](container-registry-auth-service-principal.md) ，執行 `oras login` 命令以使用服務主體應用程式識別碼和密碼登入登錄。 指定完整的登錄名稱 (所有小寫) ，在此案例中為 *myregistry.azurecr.io*。 服務主體應用程式識別碼會傳遞至環境變數 `$SP_APP_ID` ，以及變數中的密碼 `$SP_PASSWD` 。

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

若要從 Stdin 讀取密碼，請使用 `--password-stdin` 。

### <a name="sign-in-with-azure-cli"></a>使用 Azure CLI 登入

使用您的身分識別登入 Azure CLI，以從容器[登錄](/cli/azure/authenticate-azure-cli)推送和提取成品。

然後，使用 Azure CLI 命令 [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) 來存取登錄。 例如，若要向名為 *myregistry*的登錄進行驗證：

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` 使用 Docker 用戶端在檔案中設定 Azure Active Directory token `docker.config` 。 必須安裝並執行 Docker 用戶端，才能完成個別的驗證流程。

## <a name="push-an-artifact"></a>推送成品

使用一些範例文字，在本機工作的工作目錄中建立文字檔。 例如，在 bash shell 中：

```bash
echo "Here is an artifact!" > artifact.txt
```

使用 `oras push` 命令將此文字檔推送至您的登錄。 下列範例會將範例文字檔推送至存放庫 `samples/artifact` 。 登錄是以完整登錄名稱 *myregistry.azurecr.io* 來識別， (全部小寫) 。 成品已標記 `1.0` 。 根據預設，成品具有未定義的類型，並以檔案名後面的 *媒體類型* 字串來識別 `artifact.txt` 。 如需其他類型，請參閱 [OCI 構件](https://github.com/opencontainers/artifacts) 。 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

成功推送的輸出如下所示：

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

若要管理登錄中的構件，如果您使用 Azure CLI，請執行標準 `az acr` 命令來管理映射。 例如，使用 [az acr repository show][az-acr-repository-show] 命令取得成品的屬性：

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

輸出大致如下：

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>提取成品

執行 `oras pull` 命令，從您的登錄提取成品。

首先，從您的本機工作目錄中移除文字檔：

```bash
rm artifact.txt
```

執行 `oras pull` 以提取成品，並指定用來推送成品的媒體類型：

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

確認提取成功：

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>移除成品 (選擇性) 

若要從 Azure container registry 移除成品，請使用 [az acr repository delete][az-acr-repository-delete] 命令。 下列範例會移除您儲存在該處的成品：

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="example-build-docker-image-from-oci-artifact"></a>範例：從 OCI 構件組建 Docker 映射

建立容器映射的原始程式碼和二進位檔可在 Azure container registry 中儲存為 OCI 構件。 您可以參考來源成品作為 [ACR](container-registry-tasks-overview.md)工作的組建內容。 此範例示範如何將 Dockerfile 儲存為 OCI 成品，然後參考成品以建立容器映射。

例如，建立單行 Dockerfile：

```bash
echo "FROM hello-world" > hello-world.dockerfile
```

登入目的地容器登錄。

```azurecli
az login
az acr login --name myregistry
```

使用命令建立新的 OCI 成品，並將其推送至目的地登錄 `oras push` 。 此範例會設定成品的預設媒體類型。

```bash
oras push myregistry.azurecr.io/hello-world:1.0 hello-world.dockerfile
```

執行 [az acr build](/cli/azure/acr#az-acr-build) 命令，以使用新的構件作為組建內容來建立 hello world 映射：

```azurecli
az acr build --registry myregistry --file hello-world.dockerfile \
  oci://myregistry.azurecr.io/hello-world:1.0
```

## <a name="next-steps"></a>接下來的步驟

* 深入瞭解 [ORAS 程式庫](https://github.com/deislabs/oras/tree/master/docs)，包括如何設定構件的資訊清單
* 造訪 [OCI 構件](https://github.com/opencontainers/artifacts) 存放庫以取得新成品類型的參考資訊



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
