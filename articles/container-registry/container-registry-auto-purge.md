---
title: 清除標記和清單
description: 使用清除命令根據年齡和標記篩選器從 Azure 容器註冊表中刪除多個標記和清單，並可以選擇計畫清除操作。
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087334"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>自動清除 Azure 容器註冊表中的圖像

將 Azure 容器註冊表用作開發工作流的一部分時，註冊表可以快速填充在短時間內不需要的圖像或其他專案。 您可能希望刪除早于特定持續時間的所有標記或匹配指定的名稱篩選器。 為了快速刪除多個專案，本文介紹了可以作為按需`acr purge`或[計畫的](container-registry-tasks-scheduled.md)ACR 任務運行的命令。 

該`acr purge`命令當前分佈在一個公共容器映射 （），`mcr.microsoft.com/acr/acr-cli:0.1`由 GitHub 中的[acr-cli](https://github.com/Azure/acr-cli)存儲庫中的原始程式碼生成。

可以使用 Azure 雲外殼或 Azure CLI 的本地安裝來運行本文中的 ACR 任務示例。 如果您想在本地使用它，則需要版本 2.0.69 或更高版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。 

> [!IMPORTANT]
> 此功能目前為預覽狀態。 若您同意[補充的使用規定][terms-of-use]，即可取得預覽。 在公開上市 (GA) 之前，此功能的某些領域可能會變更。

> [!WARNING]
> 請謹慎`acr purge`使用該命令-刪除的圖像資料是"無法恢復"。 如果系統通過清單摘要（而不是圖像名稱）提取圖像，則不應清除未標記的圖像。 刪除已取消標記的映像，會導致這些系統無法從登錄中提取映像。 請考慮採用「唯一標記」** 配置(這是[建議的最佳做法](container-registry-image-tag-version.md))，而不是依照資訊清單提取。

如果要使用 Azure CLI 命令刪除單個圖像標記或清單，請參閱[在 Azure 容器註冊表中刪除容器映射](container-registry-delete.md)。

## <a name="use-the-purge-command"></a>使用清除命令

`acr purge`容器命令通過與名稱篩選器匹配且早于指定持續時間的存儲庫中的標記刪除圖像。 預設情況下，僅刪除標記引用，而不是基礎[清單](container-registry-concepts.md#manifest)和圖層資料。 該命令可以選擇也刪除清單。 

> [!NOTE]
> `acr purge`不刪除`write-enabled`將屬性設置為`false`的圖像標記或存儲庫。 有關詳細資訊，請參閱[在 Azure 容器註冊表中鎖定容器映射](container-registry-image-lock.md)。

`acr purge`設計為在[ACR 任務中](container-registry-tasks-overview.md)作為容器命令運行，以便它使用運行任務的註冊表自動進行身份驗證，並在那裡執行操作。 本文中的任務示例使用`acr purge`命令[別名](container-registry-tasks-reference-yaml.md#aliases)代替完全限定的容器映射命令。

至少，在運行`acr purge`： 時指定以下內容：

* `--filter`- 用於篩選存儲庫中標記的存儲庫和*正則運算式*。 示例：`--filter "hello-world:.*"`匹配存儲庫中的所有`hello-world`標記，並`--filter "hello-world:^1.*"`匹配以 開頭的`1`標記。 傳遞多個`--filter`參數以清除多個存儲庫。
* `--ago`- Go 樣式[持續時間字串](https://golang.org/pkg/time/)，用於指示刪除圖像的持續時間。 持續時間由一個或多個小陣列成的序列組成，每個數位都有一個單元尾碼。 有效時間單位包括"d"為數，小時為"h"，m"表示分鐘數。 例如，`--ago 2d3h6m`選擇上次修改超過 2 天、3 小時和 6 分鐘之前的所有篩選圖像，並`--ago 1.5h`選擇上次修改超過 1.5 小時的圖像。

`acr purge`支援多個可選參數。 本文中的示例中使用了以下兩個示例：

* `--untagged`- 指定刪除沒有關聯標記（*未標記的清單*）的清單。
* `--dry-run`- 指定不刪除任何資料，但輸出與在沒有此標誌的情況下運行命令相同。 此參數可用於測試清除命令，以確保它不會無意中刪除要保留的資料。

對於其他參數，運行`acr purge --help`。 

`acr purge`支援 ACR Task 命令的其他功能，包括流式處理並保存以供以後檢索[的運行變數](container-registry-tasks-reference-yaml.md#run-variables)和[任務運行日誌](container-registry-tasks-logs.md)。

### <a name="run-in-an-on-demand-task"></a>在按需任務中運行

下面的示例使用[az acr run][az-acr-run]命令按需`acr purge`運行該命令。 本示例刪除 1 天前修改的`hello-world`*myREGISTRY*存儲庫中的所有圖像標記和清單。 容器命令使用環境變數傳遞。 任務在沒有源上下文的情況下運行。

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>在計畫任務中運行

下面的示例使用[az acr 任務創建][az-acr-task-create]命令創建每日[計畫的 ACR 任務](container-registry-tasks-scheduled.md)。 任務清除 7 天前在存儲庫中修改的`hello-world`標記。 容器命令使用環境變數傳遞。 任務在沒有源上下文的情況下運行。

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

運行[az acr 任務顯示][az-acr-task-show]命令以查看計時器觸發器已配置。

### <a name="purge-large-numbers-of-tags-and-manifests"></a>清除大量標記和清單

清除大量標記和清單可能需要幾分鐘或更長時間。 要清除數千個標記和清單，該命令可能需要比按需任務的預設超時時間 600 秒長，或計畫任務的 3600 秒。 如果超過超時時間，則僅刪除標記和清單的子集。 為確保完成大規模清除，通過參數`--timeout`增加值。 

例如，以下按需任務設置 3600 秒（1 小時）的超時時間：

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

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>示例：註冊表中多個存儲庫的計畫清除

本示例遍走使用`acr purge`定期清理註冊表中的多個存儲庫。 例如，您可能有一個開發管道，用於將映射推送到`samples/devimage1``samples/devimage2`和 存儲庫。 定期將開發映射導入到部署的生產存儲庫中，因此不再需要開發映射。 每週清除 和`samples/devimage1``samples/devimage2`存儲庫，為下周的工作做準備。

### <a name="preview-the-purge"></a>預覽清除

在刪除資料之前，我們建議使用 參數`--dry-run`運行按需清除任務。 此選項允許您查看該命令將清除的標記和清單，而無需刪除任何資料。 

在下面的示例中，每個存儲庫中的篩選器選擇所有標記。 該`--ago 0d`參數匹配存儲庫中與篩選器匹配的所有年齡的圖像。 根據需要修改方案的選擇條件。 參數`--untagged`指示刪除除標記之外的清單。 容器命令使用環境變數傳遞給[az acr run][az-acr-run]命令。

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

查看命令輸出以查看與選擇參數匹配的標記和清單。 由於該命令使用`--dry-run`運行，因此不會刪除任何資料。

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

### <a name="schedule-the-purge"></a>計畫清除

驗證幹運行後，創建計畫任務以自動清除。 以下示例將星期日 1：00 UTC 的每週任務安排以運行以前的清除命令：

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

運行[az acr 任務顯示][az-acr-task-show]命令以查看計時器觸發器已配置。

## <a name="next-steps"></a>後續步驟

瞭解在 Azure 容器註冊表中刪除[圖像資料](container-registry-delete.md)的其他選項。

有關映射存儲的詳細資訊，請參閱[Azure 容器註冊表中的容器映射存儲](container-registry-storage.md)。

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

