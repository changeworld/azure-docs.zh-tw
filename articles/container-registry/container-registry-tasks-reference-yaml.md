---
title: YAML 參考-ACR 工作
description: 適用於以 YAML 為「ACR 工作」定義工作的參考，包括工作屬性、步驟類型、步驟屬性及內建變數。
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: 9558f698b4a9dbca46431fc02ced6ae30de29121
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246977"
---
# <a name="acr-tasks-reference-yaml"></a>ACR 工作參考：YAML

「ACR 工作」中的多步驟工作定義提供一個著重於建置、測試及修補容器並以容器為中心的計算原始物件。 本文涵蓋定義多步驟工作之 YAML 檔的命令、參數、屬性和語法。

此文章包含適用於為「ACR 工作」建立多步驟工作 YAML 檔案的參考。 如果您想要「ACR 工作」的簡介，請參閱 [ACR 工作概觀](container-registry-tasks-overview.md)。

## <a name="acr-taskyaml-file-format"></a>acr-task.yaml 檔案格式

「ACR 工作」支援採用標準 YAML 語法的多步驟工作宣告。 您會在 YAML 檔案中定義工作的步驟。 接著，您可以將檔案傳遞至[az acr run][az-acr-run]命令，以手動方式執行工作。 或者，使用檔案來建立具有[az acr task create][az-acr-task-create]的工作，該工作會在 Git 認可或基底映射更新時自動觸發。 雖然此文章以 `acr-task.yaml` 作為包含步驟的檔案，但「ACR 工作」支援任何使用[支援的副檔名](#supported-task-filename-extensions)的有效檔案名稱。

最上層 `acr-task.yaml` 原始物件是**工作屬性** **步驟類型** 及 **步驟屬性**：

* [工作屬性](#task-properties)會套用至整個工作執行的所有步驟。 有數個全域工作屬性，包括：
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [工作步驟類型](#task-step-types)代表可在工作中執行的動作類型。 步驟類型有三種：
  * `build`
  * `push`
  * `cmd`
* [工作步驟屬性](#task-step-properties)是套用至個別步驟的參數。 步驟屬性有數個，包括：
  * `startDelay`
  * `timeout`
  * `when`
  * ...等等。

接下來是 `acr-task.yaml` 檔案的基底格式 (包括一些常見的步驟屬性)。 雖然這並未完整呈現所有可用的步驟屬性或步驟類型使用方式，但提供一個基本檔案格式的快速概觀。

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>支援的工作副檔名

「ACR 工作」保留了數個將視為工作檔案來處理的副檔名，包括 `.yaml`。 所有「未」包含在下列清單中的副檔名都會被「ACR 工作」視為 Dockerfile：.yaml、.yml、.toml、.json、.sh、.bash、.zsh、.ps1、.ps、.cmd、.bat、.ts、.js、.php、.py、.rb、.lua

YAML 是「ACR 工作」目前唯一支援的檔案格式。 其他副檔名則保留供可能的未來支援使用。

## <a name="run-the-sample-tasks"></a>執行範例工作

此文章接下來的各節中會參考數個範例工作檔案。 範例工作位於公用 GitHub 存放庫中，也就是[Azure 範例/acr 作業][acr-tasks]。 您可以使用 Azure CLI 命令[az acr run][az-acr-run]來執行它們。 範例命令會類似於：

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

範例命令的格式會假設您已在 Azure CLI 中設定預設登錄，因此它們會省略 `--registry` 參數。 若要設定預設登錄，請使用[az configure][az-configure]命令搭配 `--defaults` 參數，它會接受 `acr=REGISTRY_NAME` 值。

例如，為 Azure CLI 設定一個名為 "myregistry" 的預設登錄：

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>工作屬性

工作屬性通常會出現在 `acr-task.yaml` 檔案的頂端，而且是全域屬性，適用于整個工作步驟的完整執行。 這些全域屬性中有些可在個別步驟中被覆寫。

| 屬性 | 類型 | 選用 | 描述 | 支援覆寫 | 預設值 |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | 字串 | 是 | 「ACR 工作」服務所剖析的 `acr-task.yaml` 檔案版本。 在「ACR 工作」努力維持回溯相容性的同時，此值則可讓「ACR 工作」在已定義的版本內維持相容性。 如果未指定，則預設為最新版本。 | 否 | None |
| `stepTimeout` | 整數 (秒) | 是 | 步驟的可執行秒數上限。 如果在工作上指定屬性，則會設定所有步驟的預設 `timeout` 屬性。 如果在步驟上指定了 `timeout` 屬性，它會覆寫工作所提供的屬性。 | 是 | 600 (10 分鐘) |
| `workingDirectory` | 字串 | 是 | 容器在執行時間期間的工作目錄。 如果在工作上指定屬性，則會設定所有步驟的預設 `workingDirectory` 屬性。 如果在步驟上指定，它會覆寫工作所提供的屬性。 | 是 | `/workspace` |
| `env` | [字串, 字串, ...] | 是 |  `key=value` 格式的字串陣列，可定義工作的環境變數。 如果在工作上指定屬性，則會設定所有步驟的預設 `env` 屬性。 如果在步驟上指定，它會覆寫任何繼承自工作的環境變數。 | None |
| `secrets` | [秘密，秘密，...] | 是 | [秘密](#secret)物件的陣列。 | None |
| `networks` | [network，network，...] | 是 | [網路](#network)物件的陣列。 | None |

### <a name="secret"></a>secret

Secret 物件具有下列屬性。

| 屬性 | 類型 | 選用 | 描述 | 預設值 |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | 字串 | 否 | 密碼的識別碼。 | None |
| `keyvault` | 字串 | 是 | Azure Key Vault 秘密 URL。 | None |
| `clientID` | 字串 | 是 | 適用于 Azure 資源之[使用者指派受控識別](container-registry-tasks-authentication-managed-identity.md)的用戶端識別碼。 | None |

### <a name="network"></a>網路

Network 物件具有下列屬性。

| 屬性 | 類型 | 選用 | 描述 | 預設值 |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | 字串 | 否 | 網路的名稱。 | None |
| `driver` | 字串 | 是 | 用來管理網路的驅動程式。 | None |
| `ipv6` | bool | 是 | 是否啟用 IPv6 網路功能。 | `false` |
| `skipCreation` | bool | 是 | 是否略過網路建立。 | `false` |
| `isDefault` | bool | 是 | 網路是否為 Azure Container Registry 所提供的預設網路 | `false` |

## <a name="task-step-types"></a>工作步驟類型

「ACR 工作」支援三種步驟類型。 每一種步驟類型都支援數個屬性，詳述於每一種步驟類型的小節中。

| 步驟類型 | 描述 |
| --------- | ----------- |
| [`build`](#build) | 使用熟悉的 `docker build` 語法來建置容器映像。 |
| [`push`](#push) | 執行新建置或重新標記之映像的 `docker push` 以推送至容器登錄。 支援 Azure Container Registry、其他私人登錄，以及公用 Docker Hub。 |
| [`cmd`](#cmd) | 以命令方式執行容器，並將參數傳遞給容器的 `[ENTRYPOINT]`。 `cmd` 步驟類型支援 `env`、`detach`和其他熟悉的 `docker run` 命令選項之類的參數，可讓您使用並行容器執行來進行單元和功能測試。 |

## <a name="build"></a>build

建置容器映像。 `build` 步驟類型代表一種在雲端中以第一級原始物件形式執行 `docker build` 的多租用戶、安全方法。

### <a name="syntax-build"></a>語法：build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

`build` 步驟類型支援下表中的參數。 `build` 步驟類型也支援 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令的所有建置選項，例如以 `--build-arg` 設定建置時間變數。

| 參數 | 描述 | 選用 |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | 定義所建置映像的完整 `image:tag`。<br /><br />由於映像可能會用於內部工作驗證 (例如功能測試)，因此並非所有映像都需要 `push` 來推送至登錄。 不過，若要在工作執行內將某個映像執行個體化，則該映像確實需要一個可供參考的名稱。<br /><br />不同于 `az acr build`，執行 ACR 工作不會提供預設的推送行為。 使用「ACR 工作」時，預設案例會能夠建置、驗證，然後推送映像。 如需了解如何視需要推送所建置的映像，請參閱 [push](#push)。 | 是 |
| `-f` &#124; `--file` | 指定傳遞給 `docker build` 的 Dockerfile。 如果未指定，則會假設使用內容根目錄中的預設 Dockerfile。 若要指定 Dockerfile，請傳遞相對於內容根目錄的檔案名。 | 是 |
| `context` | 傳遞給 `docker build` 的根目錄。 每個工作的根目錄都會設定為共用的 [workingDirectory](#task-step-properties)，並且包含相關 Git 複製目錄的根目錄。 | 否 |

### <a name="properties-build"></a>屬性：build

`build` 步驟類型支援下列屬性。 在這篇文章的工作[步驟屬性](#task-step-properties)一節中，尋找這些屬性的詳細資料。

| | | |
| -------- | ---- | -------- |
| `detach` | bool | 選用 |
| `disableWorkingDirectoryOverride` | bool | 選用 |
| `entryPoint` | 字串 | 選用 |
| `env` | [字串, 字串, ...] | 選用 |
| `expose` | [字串, 字串, ...] | 選用 |
| `id` | 字串 | 選用 |
| `ignoreErrors` | bool | 選用 |
| `isolation` | 字串 | 選用 |
| `keep` | bool | 選用 |
| `network` | 物件 (object) | 選用 |
| `ports` | [字串, 字串, ...] | 選用 |
| `pull` | bool | 選用 |
| `repeat` | int | 選用 |
| `retries` | int | 選用 |
| `retryDelay` | 整數 (秒) | 選用 |
| `secret` | 物件 (object) | 選用 |
| `startDelay` | 整數 (秒) | 選用 |
| `timeout` | 整數 (秒) | 選用 |
| `when` | [字串, 字串, ...] | 選用 |
| `workingDirectory` | 字串 | 選用 |

### <a name="examples-build"></a>範例：build

#### <a name="build-image---context-in-root"></a>建置映像 - 根目錄中的內容

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>建置映像 - 子目錄中的內容

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

將一或多個已建置或重新標記的映像推送至登錄。 支援推送至私人登錄 (例如 Azure Container Registry) 或公用 Docker Hub。

### <a name="syntax-push"></a>語法：push

`push` 步驟類型支援映像集合。 YAML 集合語法支援內嵌和巢狀格式。 推送單一映像通常是以內嵌語法表示：

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

為了提升可讀性，推送多個映像時，請使用巢狀語法：

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>屬性：push

`push` 步驟類型支援下列屬性。 在這篇文章的工作[步驟屬性](#task-step-properties)一節中，尋找這些屬性的詳細資料。

| | | |
| -------- | ---- | -------- |
| `env` | [字串, 字串, ...] | 選用 |
| `id` | 字串 | 選用 |
| `ignoreErrors` | bool | 選用 |
| `startDelay` | 整數 (秒) | 選用 |
| `timeout` | 整數 (秒) | 選用 |
| `when` | [字串, 字串, ...] | 選用 |

### <a name="examples-push"></a>範例：push

#### <a name="push-multiple-images"></a>推送多個映像

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>建置、推送及執行

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

`cmd` 步驟類型會執行容器。

### <a name="syntax-cmd"></a>語法：cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>屬性：cmd

`cmd` 步驟類型支援下列屬性：

| | | |
| -------- | ---- | -------- |
| `detach` | bool | 選用 |
| `disableWorkingDirectoryOverride` | bool | 選用 |
| `entryPoint` | 字串 | 選用 |
| `env` | [字串, 字串, ...] | 選用 |
| `expose` | [字串, 字串, ...] | 選用 |
| `id` | 字串 | 選用 |
| `ignoreErrors` | bool | 選用 |
| `isolation` | 字串 | 選用 |
| `keep` | bool | 選用 |
| `network` | 物件 (object) | 選用 |
| `ports` | [字串, 字串, ...] | 選用 |
| `pull` | bool | 選用 |
| `repeat` | int | 選用 |
| `retries` | int | 選用 |
| `retryDelay` | 整數 (秒) | 選用 |
| `secret` | 物件 (object) | 選用 |
| `startDelay` | 整數 (秒) | 選用 |
| `timeout` | 整數 (秒) | 選用 |
| `when` | [字串, 字串, ...] | 選用 |
| `workingDirectory` | 字串 | 選用 |

您可以在此文章的[工作步驟屬性](#task-step-properties)一節中，找到這些屬性的詳細資料。

### <a name="examples-cmd"></a>範例：cmd

#### <a name="run-hello-world-image"></a>執行 hello-world 映像

此命令會執行 `hello-world.yaml` 工作檔案以參考 Docker Hub 上的 [hello-world](https://hub.docker.com/_/hello-world/) 映像。

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>執行 bash 映像並回應 "hello world"

此命令會執行 `bash-echo.yaml` 工作檔案以參考 Docker Hub 上的 [bash](https://hub.docker.com/_/bash/) 映像。

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>執行特定 bash 映像標記

若要執行特定的映像版本，請在 `cmd`中指定標記。

此命令會執行 `bash-echo-3.yaml` 工作檔案以參考 Docker Hub 上的 [bash:3.0](https://hub.docker.com/_/bash/) 映像。

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>執行自訂映像

`cmd` 步驟類型會使用標準 `docker run` 格式來參考映像。 映像的開頭如果不是登錄，系統會假設其產生自 docker.io。 上述範例可同樣表示為：

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

藉由使用標準 `docker run` 映射參考慣例，`cmd` 可以從任何私人登錄或公用 Docker Hub 執行映射。 如果您要參考「ACR 工作」執行所在之相同登錄中的映像，則不需要指定任何登錄認證。

* 執行來自 Azure container registry 的映射。 下列範例假設您具有名為 `myregistry`的登錄，以及自訂映射 `myimage:mytag`。

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* 使用執行變數或別名將登錄參考一般化

    您不需要在 `acr-task.yaml` 檔案中硬式編碼您的登錄名稱，而可以使用[執行變數](#run-variables)或[別名](#aliases)讓它更具可攜性。 `Run.Registry` 變數或 `$Registry` 別名會在執行時間展開為工作執行所在的登錄名稱。

    例如，若要將上述工作一般化，使其可在任何 Azure container registry 中運作，請參考映射名稱中的 $Registry 變數：

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>工作步驟屬性

每個步驟類型都支援數個適用於其類型的屬性。 下表定義所有可用的步驟屬性。 並非所有步驟類型都支援所有屬性。 若要查看每個步驟類型可使用這當中哪些屬性，請參閱 [cmd](#cmd)、[build](#build)及 [push](#push) 步驟類型參考小節。

| 屬性 | 類型 | 選用 | 描述 | 預設值 |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | 是 | 執行時是否應將容器中斷連結。 | `false` |
| `disableWorkingDirectoryOverride` | bool | 是 | 是否要停用 `workingDirectory` 覆寫功能。 搭配 `workingDirectory` 使用此項，即可完整控制容器的工作目錄。 | `false` |
| `entryPoint` | 字串 | 是 | 覆寫步驟容器的 `[ENTRYPOINT]`。 | None |
| `env` | [字串, 字串, ...] | 是 | `key=value` 格式的字串陣列，用來定義步驟的環境變數。 | None |
| `expose` | [字串, 字串, ...] | 是 | 從容器公開的埠陣列。 |  None |
| [`id`](#example-id) | 字串 | 是 | 可唯一識別工作內的步驟。 工作內的其他步驟可以參考步驟的 `id`，例如使用 `when`進行相依性檢查。<br /><br />`id` 同時也是執行中容器的名稱。 舉例來說，在工作內其他容器中執行的程序可以參考 `id` 作為其 DNS 主機名稱，或藉由 docker logs [id] 來存取它。 | `acb_step_%d`，其中 `%d` 是 YAML 檔案中步驟上階的以零為基的索引 |
| `ignoreErrors` | bool | 是 | 不論容器執行期間是否發生錯誤，是否將步驟標記為成功。 | `false` |
| `isolation` | 字串 | 是 | 容器的隔離等級。 | `default` |
| `keep` | bool | 是 | 在執行後是否應保留步驟的容器。 | `false` |
| `network` | 物件 (object) | 是 | 識別執行容器的網路。 | None |
| `ports` | [字串, 字串, ...] | 是 | 從容器發佈到主機的埠陣列。 |  None |
| `pull` | bool | 是 | 是否要先強制提取容器，再執行它以防止任何快取行為。 | `false` |
| `privileged` | bool | 是 | 是否要以特殊許可權模式執行容器。 | `false` |
| `repeat` | int | 是 | 重複執行容器的重試次數。 | 0 |
| `retries` | int | 是 | 容器失敗執行時嘗試的重試次數。 只有當容器的結束代碼不是零時，才會嘗試重試。 | 0 |
| `retryDelay` | 整數 (秒) | 是 | 容器執行重試之間的延遲（以秒為單位）。 | 0 |
| `secret` | 物件 (object) | 是 | 識別 Azure 資源的 Azure Key Vault 秘密或[受控識別](container-registry-tasks-authentication-managed-identity.md)。 | None |
| `startDelay` | 整數 (秒) | 是 | 延遲容器執行的秒數。 | 0 |
| `timeout` | 整數 (秒) | 是 | 終止步驟前可允許步驟執行的秒數上限。 | 600 |
| [`when`](#example-when) | [字串, 字串, ...] | 是 | 設定步驟與工作內一或多個其他步驟的相依性。 | None |
| `user` | 字串 | 是 | 容器的使用者名稱或 UID | None |
| `workingDirectory` | 字串 | 是 | 設定步驟的工作目錄。 「ACR 工作」預設會建立根目錄作為工作目錄。 不過，如果您的組建含有數個步驟，則可藉由指定相同的工作目錄，讓較前面的步驟與較後面的步驟共用成品。 | `/workspace` |

### <a name="examples-task-step-properties"></a>範例：工作步驟屬性

#### <a name="example-id"></a>範例：id

建置兩個映像，將功能測試映像執行個體化。 每個步驟都會由唯一 `id` 識別，工作中的其他步驟會在其 `when` 屬性中參考此 id。

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>範例：when

`when` 屬性會指定步驟與工作內其他步驟的相依性。 它支援兩個參數值：

* `when: ["-"]` - 表示與其他步驟沒有相依性。 指定 `when: ["-"]` 的步驟將立即開始執行，並可啟用並行步驟執行。
* `when: ["id1", "id2"]` - 表示步驟與 `id` 為 "id1" 和 `id` 為 "id2" 的步驟相依。 此步驟必須等到 "id1" 和 "id2" 步驟都完成後，才會執行。

如果步驟中未指定 `when`，則該步驟會取決於 `acr-task.yaml` 檔案中的前一個步驟是否完成。

不含 `when` 的循序步驟執行：

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

含有 `when` 的循序步驟執行：

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

平行映射組建：

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

平行映像建置和相依測試：

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Run 變數

「ACR 工作」包含一組預設變數，可供工作步驟在執行時使用。 使用 `{{.Run.VariableName}}` 格式即可存取這些變數，其中 `VariableName` 是下列其中之一：

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

變數名稱一般一目了然。 常用變數的詳細資料如下所示。 從 YAML 版本 `v1.1.0`開始，您可以使用縮寫的預先定義工作[別名](#aliases)來取代大部分的執行變數。 例如，若要取代 `{{.Run.Registry}}`，請使用 `$Registry` 別名。

### <a name="runid"></a>Run.ID

每次執行時，都會透過 `az acr run`，或透過 `az acr task create`建立的觸發程式執行，都有唯一的識別碼。 此識別碼代表目前正在執行的「執行」。

通常用來唯一標記某個映像：

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

登錄的完整伺服器名稱。 通常用來一般參考工作執行所在的登錄。

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>執行. 于: registryname

容器登錄的名稱。 通常用於不需要完整伺服器名稱的工作步驟中，例如，`cmd` 在登錄上執行 Azure CLI 命令的步驟。

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

執行開始的目前 UTC 時間。

### <a name="runcommit"></a>執行. Commit

對於由 GitHub 存放庫認可所觸發的工作，認可識別碼。

### <a name="runbranch"></a>執行分支

對於由 GitHub 存放庫認可所觸發的工作，則為分支名稱。

## <a name="aliases"></a>別名

從 `v1.1.0`，ACR 工作支援執行時可供工作步驟使用的別名。 別名類似于 bash 中支援的別名（命令快捷方式）和一些其他命令 shell。 

使用別名時，您可以藉由輸入單一單字，啟動任何命令或命令群組（包括選項和檔案名）。

ACR 工作支援數個預先定義的別名，以及您所建立的自訂別名。

### <a name="predefined-aliases"></a>預先定義的別名

下列工作別名可用於取代[執行變數](#run-variables)：

| Alias | 執行變數 |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

在 [工作步驟] 中，在別名前面加上 `$` 指示詞，如下列範例所示：

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>影像別名

下列每個別名都會指向 Microsoft Container Registry （MCR）中的穩定映射。 您可以在工作檔案的 [`cmd`] 區段中參考每個專案，而不使用指示詞。

| Alias | 影像 |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

下列範例工作會使用數個別名，在執行登錄中的存放庫 `samples/hello-world` 中[清除](container-registry-auto-purge.md)7 天之前的映射標記：

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>自訂別名

在 YAML 檔案中定義自訂別名並加以使用，如下列範例所示。 別名只能包含英數位元。 展開別名的預設指示詞是 `$` 字元。

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

您可以連結至遠端或本機 YAML 檔案，以取得自訂別名定義。 下列範例會連結至 Azure blob 儲存體中的 YAML 檔案：

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>後續步驟

如需多步驟工作的概觀，請參閱[執行 ACR 工作中的多步驟建置、測試及修補工作](container-registry-tasks-multi-step.md)。

如需了解單一步驟建置，請參閱 [ACR 工作概觀](container-registry-tasks-overview.md)。



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
