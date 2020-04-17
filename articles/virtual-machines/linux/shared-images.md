---
title: 使用 Azure CLI 建立分享影像庫
description: 在本文中，您將了解如何使用 Azure CLI，在 Azure 中建立 VM 的共用映像。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: axayjo
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: ab0a4b98e562cda544800f2938d0b829fb1d6ef7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458636"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>使用 Azure CLI 建立共用映像資源庫

[共用映像資源庫](shared-image-galleries.md)可簡化跨組織共用自訂映像。 自訂映像類似 Marketplace 映像，但您要自行建立它們。 自訂映像可用於啟動程序設定，例如，預先載入應用程式、應用程式設定和其他 OS 設定。 

共用映像資源庫可讓您與 AAD 租用戶中區域內或跨區域組織中的其他人共用自訂 VM 映像。 選擇您要共用的映像、您要開放使用的區域，以及您要共用的對象。 您可以建立多個資源庫，讓您可以根據邏輯群組共用映像。 

資源庫是一種頂層資源，可提供完整的角色型存取控制 (RBAC)。 可建立映像版本，並且您可以選擇將每個映像版本複寫到不同的 Azure 區域集合。 資源庫僅適用於受控映像。

共用映像庫具有多個資源類型。 我們將在這篇文章中使用或建置這些資源類型：

| 資源 | 描述|
|----------|------------|
| **託管映像** | 這是基本映像，既可單獨使用，也可用來在映像庫中建立個**映像版本**。 受控映像是從一般化 VM 建立的。 受控映像是一種特殊的 VHD 類型，可用來產生多個 VM，現在可以用來建立共用映像版本。 |
| **影像庫** | 和 Azure Marketplace 一樣，**映像庫**是用於管理和共用映像的存放庫，但您可以控制哪些使用者能夠存取。 |
| **映像定義** | 映像會在資源庫內定義，並帶有映像資訊以及在內部使用時所需滿足的需求。 這包括映像是 Windows 還是 Linux、版本資訊以及最小和最大的記憶體需求。 這是映像類型的定義。 |
| **映像版本** | **映像版本**是在使用資源庫時用來建立 VM 的項目。 您可以視需要為環境準備多個映像版本。 和受控映像一樣，當您使用**映像版本**來建立 VM 時，系統會使用映像版本來建立 VM 的新磁碟。 映像版本可以使用多次。 |



[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>建立 VM

使用[az vm 創建](/cli/azure/vm#az-vm-create)從最新映像版本創建 VM。

```azurecli-interactive 
az vm create\
   --resource-group myGalleryRG \
   --name myVM \
   --image "/subscriptions/subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --generate-ssh-keys
```

還可以使用`--image`參數的圖像版本 ID 來使用特定版本。 例如,要使用映像版本*1.0.0*類型`--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`: 。

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>後續步驟
[Azure 映像產生器(預覽)](image-builder-overview.md)可以協助自動建立映像版本,您甚至可以使用它[從現有映像版本更新和建立新映像版本](image-builder-gallery-update-image-version.md)。 

您還可以使用範本創建共用圖像庫資源。 有數個 Azure 快速入門範本可以使用： 

- [建立共用映像庫](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [在共用映像資源庫中建立映像定義](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [在共用映像資源庫中建立映像版本](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [從映像版本建立 VM](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

如需共用映像資源庫的詳細資訊，請參閱[概觀](shared-image-galleries.md)。 若遇到任何問題，請參閱[針對共用映像資源庫問題進行疑難排解](troubleshooting-shared-images.md)。
