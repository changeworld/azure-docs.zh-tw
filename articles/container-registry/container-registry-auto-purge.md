---
title: 清除標記和資訊清單
description: 使用清除命令，根據壽命和標記篩選條件刪除 Azure Container Registry 中的多個標記和資訊清單，並選擇性地排程清除作業。
ms.topic: article
ms.date: 05/14/2020
ms.openlocfilehash: ab6794648babd2bd491ded5788455b75c10d675a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83652645"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>自動清除 Azure Container Registry 中的映像

使用 Azure Container Registry 做為開發工作流程的一部分時，登錄很快會被短時間內就不需要的映像或其他成品填滿。 您可能想要刪除比特定期間還舊或符合指定名稱篩選條件的所有標記。 為了快速刪除多個成品，本文介紹 `acr purge` 命令，您可以用隨需工作或[排程的](container-registry-tasks-scheduled.md)存取控制記錄 (ACR) 工作兩種方式執行。 

`acr purge` 命令目前在公用容器映像 (`mcr.microsoft.com/acr/acr-cli:0.2`) 中散發，是從 GitHub [acr-cli](https://github.com/Azure/acr-cli) 存放庫中的原始碼建構而來。

您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來執行本文中的 ACR 工作。 如果您想要在本機使用，需使用 2.0.76 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。 

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

> [!WARNING]
> 請謹慎使用 `acr purge` 命令，已刪除的映像資料無法復原。 如果您的系統會依照資訊清單摘要 (相對於映像名稱) 提取映像，則不應該清除無標記的映像。 刪除已取消標記的映像，會導致這些系統無法從登錄中提取映像。 請考慮採用「唯一標記」配置 (這是[建議的最佳做法](container-registry-image-tag-version.md))，而不是依照資訊清單提取。

如果您想要使用 Azure CLI 命令來刪除單一映像標記或資訊清單，請參閱[刪除 Azure Container Registry 中的容器映像](container-registry-delete.md)。

## <a name="use-the-purge-command"></a>使用清除命令

`acr purge` 容器命令會依據存放庫中的標記，將符合名稱篩選條件以及比指定時間更舊的映像刪除。 根據預設，只會刪除標記參考，而非基礎的[資訊清單](container-registry-concepts.md#manifest)和層資料。 此命令也可以選擇是否要刪除資訊清單。 

> [!NOTE]
> `acr purge` 不會刪除 `write-enabled` 屬性設為 `false` 的映像標記或存放庫。 如需詳細資訊，請參閱[鎖定 Azure Container Registry 中的容器映像](container-registry-image-lock.md)。

`acr purge` 設計為在 [ACR 工作](container-registry-tasks-overview.md)中以容器命令的形式執行，因此其會在執行工作的登錄中自動進行驗證，並在其中執行動作。 本文中的工作範例使用 `acr purge` 命令的[別名](container-registry-tasks-reference-yaml.md#aliases)取代完整的容器映像命令。

當您執行 `acr purge` 時，請至少指定下列內容：

* `--filter` - 存放庫和規則運算式，用來篩選存放庫中標記。 範例：`--filter "hello-world:.*"` 會比對 `hello-world` 存放庫中的所有標記，而 `--filter "hello-world:^1.*"` 會比對以 `1` 開頭的標記。 傳遞多個 `--filter` 參數來清除多個存放庫。
* `--ago` - Go 樣式的[持續時間字串](https://golang.org/pkg/time/)，指出刪除的映像需經歷的持續時間。 持續時間是由一或多個十進位數字的序列所組成，每個都有一個單位尾碼。 有效的時間單位包括 "d" 代表天1，"h" 代表小時，"m" 表示分鐘。 例如，`--ago 2d3h6m` 選取的是上次修改時間超過 2 天 3 小時 6 分鐘前的所有映像，`--ago 1.5h` 則選取上次修改時間超過 1.5 小時前的映像。

`acr purge` 支援數個選擇性參數。 本文中的範例使用下列兩個參數：

* `--untagged` - 指定刪除沒有關聯標記的資訊清單 (無標記的資訊清單)。
* `--dry-run` - 指定不刪除任何資料，但輸出會與在沒有此旗標的情況下執行命令相同。 此參數很適合用於測試清除命令，以確保其不會不小心刪除您想要保留的資料。

如需其他參數，請執行 `acr purge --help`。 

`acr purge` 支援 ACR 工作命令的其他功能，包括 [Run 變數](container-registry-tasks-reference-yaml.md#run-variables)和[工作執行記錄](container-registry-tasks-logs.md)，這些會進行資料串流處理，並儲存以供日後擷取。

### <a name="run-in-an-on-demand-task"></a>在隨需工作中執行

下列範例使用 [az acr run][az-acr-run] 命令隨需執行 `acr purge` 命令。 這個範例會刪除在 *myregistry* 中 `hello-world` 存放庫中，超過 1 天前修改的所有映像標籤和資訊清單。 容器命令需使用環境變數來傳遞。 工作會在沒有來源內容的情況下執行。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>排程工作中的執行

下列範例使用 [az acr task create][az-acr-task-create] 命令建立每日[排程的 ACR 工作](container-registry-tasks-scheduled.md)。 此工作會清除 `hello-world` 存放庫中超過 7 天前修改的標記。 容器命令需使用環境變數來傳遞。 工作會在沒有來源內容的情況下執行。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

執行 [az acr task show][az-acr-task-show] 命令來查看計時器觸發程序是否已設定。

### <a name="purge-large-numbers-of-tags-and-manifests"></a>清除大量標記和資訊清單

清除大量的標記和資訊清單可能需要數分鐘或更久的時間。 若要清除數以千計的標記和資訊清單，此命令需要的執行時間可能會超過隨需工作的預設逾時時間 600 秒，以及排程工作的預設逾時時間 3600 秒。 如果超過逾時時間，只會刪除標記和資訊清單的其中一部分。 若要確保大規模清除完成，請傳遞 `--timeout` 參數來增加此值。 

例如，下列隨需工作會將逾時時間設定為 3600 秒 (1小時)：

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>範例：登錄中多個存放庫的排程清除

這個範例會逐步解說如何使用 `acr purge` 定期清除登錄中的多個存放庫。 例如，您可能有一個開發管線，可將映像推送至 `samples/devimage1` 和 `samples/devimage2` 存放庫。 您定期將開發映像匯入部署的生產存放庫，因此不再需要開發映像。 每週一次，您會清除 `samples/devimage1` 和 `samples/devimage2` 存放庫，做為下一周的工作準備。

### <a name="preview-the-purge"></a>預覽清除

在刪除資料之前，建議使用 `--dry-run` 參數執行隨需清除工作。 此選項可讓您查看命令將清除的標記和資訊清單，而不會移除任何資料。 

在下列範例中，每個存放庫中的篩選條件都會選取所有標記。 `--ago 0d` 參數會比對符合篩選條件的存放庫`之中所有壽命的映像。 視您的案例需要修改選取條件。 `--untagged` 參數表示除了標記之外，也要刪除資訊清單。 容器命令會使用環境變數傳遞至 [az acr run][az-acr-run] 命令。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

請檢查命令輸出，查看符合選取參數的標記和資訊清單。 因為命令是以 `--dry-run` 執行，所以不會刪除任何資料。

範例輸出：

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>排程清除

在您確認試執行之後，請建立排程的工作來自動清除。 下列範例會排程每週工作，在星期日的 1:00 UTC 執行先前的清除命令：

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

執行 [az acr task show][az-acr-task-show] 命令來查看計時器觸發程序是否已設定。

## <a name="next-steps"></a>後續步驟

深入瞭解 在 Azure Container Registry 中[刪除映像資料](container-registry-delete.md)的其他選項。

如需映像儲存體的詳細資訊，請參閱 [Azure Container Registry 中的容器映像儲存體](container-registry-storage.md)。

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

