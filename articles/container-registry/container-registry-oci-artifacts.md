---
title: 推拉 OCI 工件
description: 使用 Azure 中的專用容器註冊表推送和拉取打開容器計畫 （OCI） 專案
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371047"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>使用 Azure 容器註冊表推送和拉取 OCI 專案

可以使用 Azure 容器註冊表存儲和管理[打開容器計畫 （OCI） 專案](container-registry-image-formats.md#oci-artifacts)以及與 Docker 和 Docker 相容的容器映射。

為了演示此功能，本文演示如何使用[OCI 註冊表作為存儲 （ORAS）](https://github.com/deislabs/oras)工具將示例專案（文字檔）推送到 Azure 容器註冊表。 然後，從註冊表中提取專案。 您可以使用適合每個專案的不同命令列工具在 Azure 容器註冊表中管理各種 OCI 專案。

## <a name="prerequisites"></a>Prerequisites

* **Azure 容器註冊表**- 在 Azure 訂閱中創建容器註冊表。 例如，使用 [Azure 入口網站](container-registry-get-started-portal.md)或 [Azure CLI](container-registry-get-started-azure-cli.md)。
* **ORAS 工具**- 從[GitHub 存儲庫](https://github.com/deislabs/oras/releases)下載並安裝作業系統的當前 ORAS 版本。 該工具作為壓縮的 tarball（`.tar.gz`檔）釋放。 使用作業系統的標準過程提取和安裝檔。
* **Azure 活動目錄服務主體（可選）** - 要直接使用 ORAS 進行身份驗證，請創建[一個服務主體](container-registry-auth-service-principal.md)來訪問註冊表。 確保為服務主體分配了 AcrPush 等角色，以便它具有推送和拉取工件的許可權。
* **Azure CLI（可選）** - 要使用單個標識，需要本地安裝 Azure CLI。 建議版本 2.0.71 或更高版本。 運行`az --version `以查找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。
* **Docker（可選）** - 要使用單個標識，還必須在本地安裝 Docker，以便對註冊表進行身份驗證。 Docker 提供可輕鬆在任何 [macOS][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系統上設定 Docker 的套件。


## <a name="sign-in-to-a-registry"></a>登錄到註冊表

本節顯示兩個建議的工作流，以登錄到註冊表，具體取決於所使用的標識。 選擇適合您的環境的方法。

### <a name="sign-in-with-oras"></a>使用 ORAS 登錄

使用具有推送許可權[的服務主體](container-registry-auth-service-principal.md)運行`oras login`命令，使用服務主體應用程式 ID 和密碼登錄到註冊表。 指定完全限定的登錄機碼（所有小寫），在這種情況下*myregistry.azurecr.io*。 服務主體應用程式 ID 在環境變數`$SP_APP_ID`中傳遞，並在變數`$SP_PASSWD`中的密碼傳遞。

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

要從 Stdin 讀取密碼，`--password-stdin`請使用 。

### <a name="sign-in-with-azure-cli"></a>使用 Azure CLI 登入

使用標識[登錄到](/cli/azure/authenticate-azure-cli)Azure CLI 以推送和從容器註冊表中提取專案。

然後，使用 Azure CLI 命令[az acr 登錄](/cli/azure/acr?view=azure-cli-latest#az-acr-login)訪問註冊表。 例如，要對名為*Myregistry 的*註冊表進行身份驗證：

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`使用 Docker 用戶端在`docker.config`檔中設置 Azure 活動目錄權杖。 必須安裝並運行 Docker 用戶端才能完成各個身份驗證流。

## <a name="push-an-artifact"></a>推送工件

使用一些示例文本在本地工作目錄中創建文字檔。 例如，在 bash 外殼中：

```bash
echo "Here is an artifact!" > artifact.txt
```

使用`oras push`命令將此文字檔推送到註冊表。 下面的示例將示例文字檔推送到`samples/artifact`回購。 註冊表使用完全限定的註冊表名稱*myregistry.azurecr.io（* 所有小寫）進行標識。 專案被標記`1.0`。 預設情況下，專案具有未定義的類型，由檔案名`artifact.txt`之後的*媒體類型*字串標識。 有關其他類型，請參閱[OCI 工件](https://github.com/opencontainers/artifacts)。 

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

成功推送的輸出類似于以下內容：

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

要管理註冊表中的專案，如果使用 Azure CLI，請運行用於管理映射的標準`az acr`命令。 例如，使用[az acr 存儲庫顯示][az-acr-repository-show]命令獲取專案的屬性：

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

## <a name="pull-an-artifact"></a>拉一個工件

運行命令`oras pull`從註冊表中提取專案。

首先從本地工作目錄中刪除文字檔：

```bash
rm artifact.txt
```

運行`oras pull`以拉取工件，並指定用於推送工件的媒體類型：

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

驗證拉取是否成功：

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>刪除專案（可選）

要從 Azure 容器註冊表中刪除專案，請使用[az acr 存儲庫刪除][az-acr-repository-delete]命令。 以下示例刪除您存儲在其中的專案：

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>後續步驟

* 瞭解有關[ORAS 庫](https://github.com/deislabs/oras/tree/master/docs)（包括如何為專案配置清單）的更多詳細資訊
* 訪問[OCI 工件](https://github.com/opencontainers/artifacts)存儲庫，瞭解有關新工件類型的參考資訊



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
