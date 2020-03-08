---
title: 刪除影像資源
description: 有關如何使用 Azure CLI 命令來刪除容器映射資料，以有效管理登錄大小的詳細資訊。
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78403351"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>使用 Azure CLI 刪除 Azure Container Registry 中的容器映射

若要維護 Azure Container Registry 的大小，您應該定期刪除過時的映像資料。 雖然有些部署至生產環境的容器映像可能需要較長時間的儲存，但其他容器映像通常可以更快速地刪除。 例如，在自動化建置和測試案例中，您的登錄可以快速地填入可能永遠不會部署的映像，並可在完成建置和測試行程後立刻清除。

您可以用數種不同的方式刪除映像資料，因此務必了解每項刪除作業對於儲存體使用量有何影響。 本文涵蓋數個刪除影像資料的方法：

* 刪除[存放庫](#delete-repository)：刪除存放庫內的所有映像和所有唯一層次。
* 依[標記](#delete-by-tag)刪除：刪除映像、標記、映像參考的所有唯一層次，以及與映像相關聯的所有其他標記。
* 刪除[資訊清單摘要](#delete-by-manifest-digest)：刪除映像、映像參考的所有唯一層次，以及與映像相關聯的所有標記。

提供範例腳本以協助自動執行刪除作業。

如需這些概念的簡介，請參閱[關於登錄、存放庫和映射](container-registry-concepts.md)。

## <a name="delete-repository"></a>刪除存放庫

刪除存放庫即可刪除存放庫中的所有映像，包括所有標籤、唯一層次及資訊清單。 當您刪除儲存機制時，會復原參照該存放庫中唯一層之映射所使用的儲存空間。

下列 Azure CLI 命令可刪除 "acr-helloworld" 存放庫以及該存放庫內的所有標記和資訊清單。 如果登錄中的任何其他映射未參考已刪除之資訊清單所參考的圖層，其層級資料也會一併刪除，並復原儲存空間。

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>依標記刪除

您可以在刪除作業中指定存放庫名稱和標記，以從存放庫中刪除個別映像。 當您依標記刪除時，會復原映像中任何唯一層次 (並未由登錄中任何其他映像共用的層次) 所使用的儲存空間。

若要依標記刪除，請使用[az acr repository delete][az-acr-repository-delete] ，並在 `--image` 參數中指定映射名稱。 映像獨有的所有層次，與該映像相關聯的任何其他標記都會遭到刪除。

例如，從 "myregistry" 登錄中刪除 "acr-helloworld:latest" 映像：

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> 「依標記」刪除不應該與刪除標記 (取消標記) 混淆。 您可以使用 Azure CLI 命令[az acr repository untag][az-acr-repository-untag]來刪除標記。 當您 untag 映射時，不會釋放任何空間，因為它的[資訊清單](container-registry-concepts.md#manifest)和圖層資料會保留在登錄中。 只有標記參考本身會被刪除。

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

接下來，在[az acr repository delete][az-acr-repository-delete]命令中指定您想要刪除的摘要。 此命令的格式為：

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

`acr-helloworld:v2` 映射會從登錄中刪除，如同該映射獨有的任何圖層資料。 如果資訊清單與多個標記相關聯，也會一併刪除所有相關聯的標記。

## <a name="delete-digests-by-timestamp"></a>依時間戳記刪除摘要

若要維護存放庫或登錄的大小，您可能需要定期刪除早于特定日期的資訊清單摘要。

下列 Azure CLI 命令會以遞增的順序，列出存放庫中比指定時間戳記更早的所有資訊清單摘要。 以適合您環境的值取代 `<acrName>` 和 `<repositoryName>`。 時間戳記可以是完整的日期時程表達式或日期，如下列範例所示。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

識別過時的資訊清單摘要之後，您可以執行下列 Bash 腳本，以刪除比指定時間戳記更舊的資訊清單摘要。 它需要 Azure CLI 和 **xargs**。 根據預設，此指令碼不會執行任何刪除。 將 `ENABLE_DELETE` 值變更為 `true`，以啟用映像刪除。

> [!WARNING]
> 請小心使用下列範例腳本--已刪除的映射資料無法復原。 如果您有依資訊清單摘要提取映射的系統（相對於映射名稱），則不應該執行這些腳本。 刪除資訊清單摘要將會導致這些系統無法從您的登錄中提取映射。 請考慮採用*唯一的標記*配置，這是[建議的最佳作法](container-registry-image-tag-version.md)，而不是依資訊清單提取。 

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

1. 推送具有 *latest* 標記的映像 **acr-helloworld**：`docker push myregistry.azurecr.io/acr-helloworld:latest`
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
1. 推送具有 *latest* 標記的映像 **acr-helloworld**：`docker push myregistry.azurecr.io/acr-helloworld:latest`
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

如您在序列中最後一個步驟的輸出中所見，現在有一個孤立的資訊清單，其 `"tags"` 屬性是空的清單。 此資訊清單以及它所參考的任何唯一層次資料，仍存在於登錄中。 **若要刪除這類孤立映像及其層次資料，您必須依資訊清單摘要刪除**。

## <a name="delete-all-untagged-images"></a>刪除所有已取消標記的映像

您可以使用下列 Azure CLI 命令，列出存放庫中所有已取消標記的映像。 以適合您環境的值取代 `<acrName>` 和 `<repositoryName>`。

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

在腳本中使用此命令，您可以刪除存放庫中的所有未標記映射。

> [!WARNING]
> 請小心使用下列範例指令碼--無法復原已刪除的映像資料。 如果您有依資訊清單摘要提取映射的系統（相對於映射名稱），則不應該執行這些腳本。 刪除已取消標記的映像，會導致這些系統無法從登錄中提取映像。 請考慮採用*唯一的標記*配置，這是[建議的最佳作法](container-registry-image-tag-version.md)，而不是依資訊清單提取。

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

做為腳本 Azure CLI 命令的替代方案，請執行隨選或排程的 ACR 工作，以刪除比特定期間還舊的所有標記，或符合指定的名稱篩選器。 如需詳細資訊，請參閱[從 Azure container Registry 自動清除映射](container-registry-auto-purge.md)。

選擇性地為每個登錄設定[保留原則](container-registry-retention-policy.md)，以管理未標記的資訊清單。 當您啟用保留原則時，登錄中沒有任何相關聯的標記和基礎層資料的映射資訊清單，會在設定的期間之後自動刪除。

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
