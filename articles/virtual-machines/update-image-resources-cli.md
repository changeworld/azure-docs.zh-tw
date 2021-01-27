---
title: 使用 Azure CLI 列出、更新和刪除映射資源
description: 使用 Azure CLI，列出、更新和刪除共用映射庫中的映射資源。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6099afc82e76ed28e8557ac0eee3e64cb292a715
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882003"
---
# <a name="list-update-and-delete-image-resources"></a>列出、更新和刪除映射資源 

您可以使用 Azure CLI 來管理共用映射庫資源。

[!INCLUDE [virtual-machines-common-gallery-list-cli.md](../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>後續步驟

[Azure 映射產生器 (預覽版) ](./image-builder-overview.md) 可協助自動建立映射版本，您甚至可以使用它來更新和 [建立現有映射版本的新映射版本](./linux/image-builder-gallery-update-image-version.md)。