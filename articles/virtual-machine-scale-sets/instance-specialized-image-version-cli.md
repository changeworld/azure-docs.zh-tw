---
title: 從使用 Azure CLI 的特製化映射版本建立擴展集
description: 使用 Azure CLI，在共用映射資源庫中使用特製化映射版本來建立擴展集。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e1b260b1249af25ac5a8364798c532dcb3885cb9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887884"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>使用具有 Azure CLI 的特製化映射版本來建立擴展集

從儲存在共用映射資源庫中的[特製化映射版本](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images)建立擴展集。 如果您想要使用一般化映射版本來建立擴展集，請參閱[從一般化映射建立擴展集](instance-generalized-image-version-cli.md)。

如果您選擇在本機安裝和使用 CLI，本教學課程會要求您執行 Azure CLI 2.4.0 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

視需要取代此範例中的資源名稱。 

使用[az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list)來列出資源庫中的映射定義，以查看定義的名稱和識別碼。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

使用參數來建立擴展集， [`az vmss create`](/cli/azure/vmss#az-vmss-create) `--specialized` 以指出映射是特製化映射。

使用 `--image` 的映像定義識別碼，從可用的最新擴展集執行個體版本建立 VM。 您也可以藉由提供 `--image` 的映像版本識別碼，從特定版本建立擴展集執行個體。 請注意，使用特定映射版本表示自動化可能會失敗，因為該特定映射版本無法使用，因為它已從區域中刪除或移除。 除非需要特定映射版本，否則建議使用映射定義識別碼來建立新的 VM。

在此範例中，我們會從最新版本的*myImageDefinition*映射建立實例。

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --Specialized
```


## <a name="next-steps"></a>後續步驟
[Azure 映射產生器（預覽）](../virtual-machines/linux/image-builder-overview.md)可協助自動建立映射版本，您甚至可以使用它來更新和[建立現有映射版本的新映射版本](../virtual-machines/linux/image-builder-gallery-update-image-version.md)。 

您也可以使用範本建立共用映像庫資源。 有數個 Azure 快速入門範本可以使用： 

- [建立共用映像資源庫](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共用映像資源庫中建立映像定義](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共用映像資源庫中建立映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)



