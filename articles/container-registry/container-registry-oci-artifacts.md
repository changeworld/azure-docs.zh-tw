---
title: 推送和提取 OCI 成品
description: 在 Azure 中使用私人容器登錄來推送和提取開放容器計畫（OCI）成品
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371047"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>使用 Azure container registry 推送和提取 OCI 成品

您可以使用 Azure container registry 來儲存和管理[開放容器計畫（OCI）構件](container-registry-image-formats.md#oci-artifacts)，以及 Docker 和 docker 相容的容器映射。

為了示範這項功能，本文說明如何使用 OCI 登錄[作為儲存體（ORAS）](https://github.com/deislabs/oras)工具，將範例成品（文字檔）推送至 Azure container Registry。 然後，從登錄中提取成品。 您可以使用適用于每個成品的不同命令列工具，在 Azure container registry 中管理各種 OCI 構件。

## <a name="prerequisites"></a>Prerequisites

* **Azure 容器登錄庫** - 在 Azure 訂用帳戶中建立容器登錄庫。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。
* **ORAS 工具**-從[GitHub](https://github.com/deislabs/oras/releases)存放庫下載並安裝適用于您作業系統的最新 ORAS 版本。 此工具會以壓縮的 tarball （`.tar.gz` 檔案）發行。 使用適用于您作業系統的標準程式，解壓縮並安裝檔案。
* **Azure Active Directory 服務主體（選用）** -若要直接使用 ORAS 進行驗證，請建立[服務主體](container-registry-auth-service-principal.md)來存取您的登錄。 請確定已將角色（例如 AcrPush）指派給服務主體，讓它有推送和提取成品的許可權。
* **Azure CLI （選用）** -若要使用個別的身分識別，您需要 Azure CLI 的本機安裝。 建議使用2.0.71 或更新版本。 執行 `az --version `以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。
* **Docker （選用）** -若要使用個別的身分識別，您也必須在本機安裝 Docker，才能向登錄進行驗證。 Docker 提供可輕鬆在任何 [macOS][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系統上設定 Docker 的套件。


## <a name="sign-in-to-a-registry"></a>登入登錄

本節說明兩個建議的工作流程，以登入登錄，視所使用的身分識別而定。 選擇適用于您環境的方法。

### <a name="sign-in-with-oras"></a>使用 ORAS 登入

使用[服務主體](container-registry-auth-service-principal.md)搭配推播許可權，執行 `oras login` 命令，以使用服務主體應用程式識別碼和密碼登入登錄。 指定完整的登錄名稱（全部小寫），在此案例中為*myregistry.azurecr.io*。 服務主體應用程式識別碼會在環境變數 `$SP_APP_ID`中傳遞，而變數中的密碼 `$SP_PASSWD`。

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

若要從 Stdin 讀取密碼，請使用 `--password-stdin`。

### <a name="sign-in-with-azure-cli"></a>使用 Azure CLI 登入

使用您的身分識別登入 Azure CLI，以從容器[登錄](/cli/azure/authenticate-azure-cli)推送和提取成品。

然後，使用 Azure CLI 命令[az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login)來存取登錄。 例如，若要向名為*myregistry*的登錄進行驗證：

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` 會使用 Docker 用戶端，在 `docker.config` 檔案中設定 Azure Active Directory token。 必須安裝並執行 Docker 用戶端，才能完成個別的驗證流程。

## <a name="push-an-artifact"></a>推送成品

在本機工作的工作目錄中建立文字檔，其中包含一些範例文字。 例如，在 bash shell 中：

```bash
echo "Here is an artifact!" > artifact.txt
```

使用 `oras push` 命令，將此文字檔推送至您的登錄。 下列範例會將範例文字檔推送至 `samples/artifact` 存放庫。 登錄會以完整的登錄名稱*myregistry.azurecr.io* （全部小寫）識別。 成品已標記 `1.0`。 根據預設，成品具有未定義的類型，並以 `artifact.txt`檔案名後面的*媒體類型*字串識別。 如需其他類型，請參閱[OCI 構件](https://github.com/opencontainers/artifacts)。 

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

若要在登錄中管理成品，如果您使用 Azure CLI，請執行標準 `az acr` 命令來管理映射。 例如，使用[az acr repository show][az-acr-repository-show]命令取得成品的屬性：

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

## <a name="remove-the-artifact-optional"></a>移除成品（選擇性）

若要從 Azure container registry 移除成品，請使用[az acr repository delete][az-acr-repository-delete]命令。 下列範例會移除您儲存在該處的成品：

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>後續步驟

* 深入瞭解[ORAS 程式庫](https://github.com/deislabs/oras/tree/master/docs)，包括如何設定成品的資訊清單
* 請造訪[OCI 構件](https://github.com/opencontainers/artifacts)存放庫，以取得新成品類型的參考資訊



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
