---
title: 清除標記和資訊清單
description: 使用 [清除] 命令，根據 age 和標籤篩選來刪除 Azure container registry 中的多個標記和資訊清單，並選擇性地排程清除作業。
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087334"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>自動清除 Azure container registry 中的映射

當您使用 Azure container registry 做為開發工作流程的一部分時，登錄可以快速填滿一小段時間後不需要的影像或其他成品。 您可能想要刪除比特定期間還舊的所有標記，或符合指定的名稱篩選器。 為了快速刪除多個成品，本文會介紹您可以視需要或[排定](container-registry-tasks-scheduled.md)的 ACR 工作來執行的 `acr purge` 命令。 

`acr purge` 命令目前是散發在公用容器映射（`mcr.microsoft.com/acr/acr-cli:0.1`）中，從 GitHub 中的[acr-cli](https://github.com/Azure/acr-cli)存放庫中的原始程式碼所建立。

您可以使用 Azure Cloud Shell 或本機安裝的 Azure CLI 來執行本文中的 ACR 工作範例。 如果您想要在本機使用，則需要2.0.69 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。 

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

> [!WARNING]
> 請小心使用 `acr purge` 命令--已刪除的映射資料無法復原。 如果您有依資訊清單摘要提取映射的系統（相對於映射名稱），則不應清除未標記的映射。 刪除已取消標記的映像，會導致這些系統無法從登錄中提取映像。 請考慮採用*唯一的標記*配置，這是[建議的最佳作法](container-registry-image-tag-version.md)，而不是依資訊清單提取。

如果您想要使用 Azure CLI 命令來刪除單一映射標記或資訊清單，請參閱[Azure Container Registry 中刪除容器映射](container-registry-delete.md)。

## <a name="use-the-purge-command"></a>使用清除命令

`acr purge` container 命令會依標籤在符合名稱篩選的存放庫中刪除映射，而且比指定的持續時間還舊。 根據預設，只會刪除標記參考，而不是基礎[資訊清單](container-registry-concepts.md#manifest)和圖層資料。 此命令也可以選擇是否要刪除資訊清單。 

> [!NOTE]
> `acr purge` 不會刪除 `write-enabled` 屬性設定為 `false`的影像標記或存放庫。 如需相關資訊，請參閱[鎖定 Azure container registry 中的容器映射](container-registry-image-lock.md)。

`acr purge` 是設計為在[ACR](container-registry-tasks-overview.md)工作中以容器命令的形式執行，因此它會自動與工作執行所在的登錄進行驗證，並在該處執行動作。 本文中的工作範例會使用 `acr purge` 命令[別名](container-registry-tasks-reference-yaml.md#aliases)來取代完整的容器映射命令。

當您執行 `acr purge`時，請至少指定下列內容：

* `--filter`-存放庫和*正則運算式*來篩選儲存機制中的標記。 範例： `--filter "hello-world:.*"` 符合 `hello-world` 存放庫中的所有標記，而且 `--filter "hello-world:^1.*"` 符合以 `1`開頭的標記。 傳遞多個 `--filter` 參數來清除多個存放庫。
* `--ago`-Go 樣式[持續時間字串](https://golang.org/pkg/time/)，表示要刪除影像的持續時間。 持續時間是由一或多個十進位數的序列所組成，每個都有一個單位尾碼。 有效的時間單位包括 "d" 代表日，"h" 代表小時，"m" 表示分鐘。 例如，`--ago 2d3h6m` 選取 [所有篩選的映射] [上次修改時間] 超過2天、3小時和6分鐘前，而且 `--ago 1.5h` 選取 [上次修改時間超過1.5 小時前的映射]。

`acr purge` 支援數個選擇性參數。 本文中的範例會使用下列兩個專案：

* `--untagged`-指定不會刪除相關聯標記的資訊清單（未標記的*資訊清單*）。
* `--dry-run`-指定不刪除任何資料，但輸出會與在沒有此旗標的情況下執行命令相同。 此參數適用于測試清除命令，以確保它不會不慎刪除您想要保留的資料。

如需其他參數，請執行 `acr purge --help`。 

`acr purge` 支援 ACR 工作命令的其他功能，包括已串流處理並儲存以供日後抓取的[執行變數](container-registry-tasks-reference-yaml.md#run-variables)和工作[執行記錄](container-registry-tasks-logs.md)。

### <a name="run-in-an-on-demand-task"></a>在隨選工作中執行

下列範例會使用[az acr run][az-acr-run]命令，視需要執行 `acr purge` 命令。 這個範例會刪除*myregistry*中的 `hello-world` 存放庫中，已修改超過1天前的所有影像標記和資訊清單。 Container 命令會使用環境變數來傳遞。 工作會在沒有來源內容的情況下執行。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>在排程工作中執行

下列範例會使用[az acr task create][az-acr-task-create]命令來建立每日[排程的 acr](container-registry-tasks-scheduled.md)工作。 此工作會清除在 `hello-world` 存放庫中超過7天前修改的標記。 Container 命令會使用環境變數來傳遞。 工作會在沒有來源內容的情況下執行。

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

執行[az acr task show][az-acr-task-show]命令，以查看計時器觸發程式是否已設定。

### <a name="purge-large-numbers-of-tags-and-manifests"></a>清除大量標記和資訊清單

清除大量標記和資訊清單可能需要數分鐘或更久的時間。 若要清除數以千計的標記和資訊清單，此命令可能需要執行的時間超過預設的執行時間（600秒），以供隨選工作使用，或以3600秒做為排程工作。 如果超過超時時間，只會刪除標記和資訊清單的子集。 若要確保大規模清除已完成，請傳遞 `--timeout` 參數來增加值。 

例如，下列隨選工作會將超時時間設定為3600秒（1小時）：

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

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>範例：已排程清除登錄中的多個存放庫

這個範例會逐步解說如何使用 `acr purge`，定期清除登錄中的多個存放庫。 例如，您可能有一個開發管線，可將影像推送至 `samples/devimage1` 並 `samples/devimage2` 存放庫。 您會定期將開發映射匯入至部署的生產存放庫，因此您不再需要開發映射。 您會每週清除 `samples/devimage1` 並 `samples/devimage2` 存放庫，以準備下周的工作。

### <a name="preview-the-purge"></a>預覽清除

在刪除資料之前，建議使用 `--dry-run` 參數執行隨選清除工作。 此選項可讓您查看命令將會清除的標記和資訊清單，而不會移除任何資料。 

在下列範例中，每個存放庫中的篩選都會選取所有標記。 `--ago 0d` 參數會比對符合篩選器之存放庫中所有年齡的影像。 視您的案例需要修改選取準則。 `--untagged` 參數表示除了標記之外，也會刪除資訊清單。 Container 命令會使用環境變數傳遞至[az acr run][az-acr-run]命令。

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

請檢查命令輸出，以查看符合選取參數的標記和資訊清單。 因為命令是以 `--dry-run`執行，所以不會刪除任何資料。

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

在您確認試執行之後，請建立排定的工作來自動化清除作業。 下列範例會在星期日 1:00 UTC 排程每週工作，以執行先前的清除命令：

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

執行[az acr task show][az-acr-task-show]命令，以查看計時器觸發程式是否已設定。

## <a name="next-steps"></a>後續步驟

瞭解在 Azure Container Registry 中[刪除影像資料](container-registry-delete.md)的其他選項。

如需映射儲存體的詳細資訊，請參閱[Azure Container Registry 中的容器映射儲存體](container-registry-storage.md)。

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

