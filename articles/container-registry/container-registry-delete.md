---
title: 刪除圖像資源
description: 有關如何使用 Azure CLI 命令刪除容器映射資料來有效管理註冊表大小的詳細資訊。
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403351"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>使用 Azure CLI 刪除 Azure 容器註冊表中的容器映射

若要維護 Azure Container Registry 的大小，您應該定期刪除過時的映像資料。 雖然有些部署至生產環境的容器映像可能需要較長時間的儲存，但其他容器映像通常可以更快速地刪除。 例如，在自動化建置和測試案例中，您的登錄可以快速地填入可能永遠不會部署的映像，並可在完成建置和測試行程後立刻清除。

您可以用數種不同的方式刪除映像資料，因此務必了解每項刪除作業對於儲存體使用量有何影響。 本文介紹了刪除圖像資料的幾種方法：

* 刪除[存放庫](#delete-repository)：刪除存放庫內的所有映像和所有唯一層次。
* 依[標記](#delete-by-tag)刪除：刪除映像、標記、映像參考的所有唯一層次，以及與映像相關聯的所有其他標記。
* 刪除[資訊清單摘要](#delete-by-manifest-digest)：刪除映像、映像參考的所有唯一層次，以及與映像相關聯的所有標記。

提供了示例腳本來説明自動執行刪除操作。

有關這些概念的介紹，請參閱[關於註冊表、存儲庫和圖像](container-registry-concepts.md)。

## <a name="delete-repository"></a>刪除存放庫

刪除存放庫即可刪除存放庫中的所有映像，包括所有標籤、唯一層次及資訊清單。 刪除存儲庫時，將恢復引用該存儲庫中唯一圖層的圖像使用的存儲空間。

下列 Azure CLI 命令可刪除 "acr-helloworld" 存放庫以及該存放庫內的所有標記和資訊清單。 如果已刪除清單引用的圖層未被註冊表中的任何其他圖像引用，則其圖層資料也會被刪除，從而恢復存儲空間。

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>依標記刪除

您可以在刪除作業中指定存放庫名稱和標記，以從存放庫中刪除個別映像。 當您依標記刪除時，會復原映像中任何唯一層次 (並未由登錄中任何其他映像共用的層次) 所使用的儲存空間。

若要依標記刪除，請使用 [az acr repository delete][az-acr-repository-delete] 並在 `--image` 參數中指定映像名稱。 映像獨有的所有層次，與該映像相關聯的任何其他標記都會遭到刪除。

例如，從 "myregistry" 登錄中刪除 "acr-helloworld:latest" 映像：

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> 「依標記」** 刪除不應該與刪除標記 (取消標記) 混淆。 您可以使用 Azure CLI 命令 [az acr repository untag][az-acr-repository-untag] 來刪除標記。 解除標記圖像時不會釋放任何空間，因為圖像[的清單](container-registry-concepts.md#manifest)和圖層資料將保留在註冊表中。 只有標記參考本身會被刪除。

## <a name="delete-by-manifest-digest"></a>依資訊清單摘要刪除

[資訊清單摘要](container-registry-concepts.md#manifest-digest)可以與一個、零個或多個標記產生關聯。 當您依摘要刪除時，由資訊清單參考的所有標記都會遭到刪除，因為任何層次的層次資料對映像而言都是唯一的。 共用的層次資料不會遭到刪除。

若要依摘要刪除，首先針對包含所要刪除映像的存放庫列出資訊清單摘要。 例如：

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

接下來，在 [az acr repository delete][az-acr-repository-delete] 命令中指定您要刪除的摘要。 此命令的格式為：

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

例如，若要刪除前面輸出中所列的最後一個資訊清單 (含標記 "v2")：

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

圖像`acr-helloworld:v2`將從註冊表中刪除，該映射唯一的任何圖層資料也是一樣。 如果資訊清單與多個標記相關聯，也會一併刪除所有相關聯的標記。

## <a name="delete-digests-by-timestamp"></a>按時間戳記刪除摘要

要維護存儲庫或註冊表的大小，您可能需要定期刪除早于特定日期的清單摘要。

以下 Azure CLI 命令按昇冪列出早于指定時間戳記的存儲庫中的所有清單摘要。 以適合您環境的值取代 `<acrName>` 和 `<repositoryName>`。 時間戳記可以是完整的日期時程表達式或日期，如本示例所示。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

識別陳舊的清單摘要後，可以運行以下 Bash 腳本來刪除早于指定時間戳記的清單摘要。 它需要 Azure CLI 和 **xargs**。 根據預設，此指令碼不會執行任何刪除。 將 `ENABLE_DELETE` 值變更為 `true`，以啟用映像刪除。

> [!WARNING]
> 請謹慎使用以下示例腳本 -已刪除的圖像資料無法恢復。 如果系統通過清單摘要（而不是圖像名稱）提取圖像，則不應運行這些腳本。 刪除清單摘要將阻止這些系統從註冊表中拉出圖像。 請考慮採用「唯一標記」** 配置(這是[建議的最佳做法](container-registry-image-tag-version.md))，而不是依照資訊清單提取。 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>刪除已取消標記的映像

如[資訊清單摘要](container-registry-concepts.md#manifest-digest)一節所述，使用現有標記推送已修改的映像會**取消標記**先前推送的映像，因而產生孤立 (或「懸盪」) 的映像。 先前推送映像的資訊清單以及其層次資料都會保留在登錄中。 考量以下事件順序：

1. 推送具有 **latest** 標記的映像 *acr-helloworld*：`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. 檢查 *acr-helloworld* 存放庫的資訊清單：

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. 修改 *acr-helloworld* Dockerfile
1. 推送具有 **latest** 標記的映像 *acr-helloworld*：`docker push myregistry.azurecr.io/acr-helloworld:latest`
1. 檢查 *acr-helloworld* 存放庫的資訊清單：

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

正如您在序列中最後一步的輸出中看到的，現在有一個孤立清單，其`"tags"`屬性為空清單。 此資訊清單以及它所參考的任何唯一層次資料，仍存在於登錄中。 **若要刪除這類孤立映像及其層次資料，您必須依資訊清單摘要刪除**。

## <a name="delete-all-untagged-images"></a>刪除所有已取消標記的映像

您可以使用下列 Azure CLI 命令，列出存放庫中所有已取消標記的映像。 以適合您環境的值取代 `<acrName>` 和 `<repositoryName>`。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

在腳本中使用此命令，可以刪除存儲庫中的所有未標記圖像。

> [!WARNING]
> 請小心使用下列範例指令碼--無法復原已刪除的映像資料。 如果系統通過清單摘要（而不是圖像名稱）提取圖像，則不應運行這些腳本。 刪除已取消標記的映像，會導致這些系統無法從登錄中提取映像。 請考慮採用「唯一標記」** 配置(這是[建議的最佳做法](container-registry-image-tag-version.md))，而不是依照資訊清單提取。

**Bash 中的 Azure CLI**

下列 Bash 指令碼會從存放庫中刪除所有已取消標記的映像。 它需要 Azure CLI 和 **xargs**。 根據預設，此指令碼不會執行任何刪除。 將 `ENABLE_DELETE` 值變更為 `true`，以啟用映像刪除。

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**PowerShell 中的 Azure CLI**

下列 PowerShell 指令碼會從存放庫中刪除所有已取消標記的映像。 它需要 PowerShell 和 Azure CLI。 根據預設，此指令碼不會執行任何刪除。 將 `$enableDelete` 值變更為 `$TRUE`，以啟用映像刪除。

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>自動清除標記和資訊清單 (預覽)

作為編寫 Azure CLI 命令腳本的替代方法，請運行按需或計畫的 ACR 任務，以刪除早于特定持續時間或匹配指定名稱篩選器的所有標記。 有關詳細資訊，請參閱從[Azure 容器註冊表中自動清除圖像](container-registry-auto-purge.md)。

可以選擇為每個註冊表設置[保留原則](container-registry-retention-policy.md)，以管理未標記的清單。 啟用保留原則時，註冊表中沒有任何關聯標記的圖像清單和基礎圖層資料會在設定的時間段後自動刪除。

## <a name="next-steps"></a>後續步驟

如需 Azure Container Registry 中映像儲存體的詳細資訊，請參閱 [Azure Container Registry 中的容器映像儲存體](container-registry-storage.md)。

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
