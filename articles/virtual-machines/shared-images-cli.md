---
title: 使用 Azure CLI 建立共用映射資源庫
description: 在本文中，您將了解如何使用 Azure CLI，在 Azure 中建立 VM 的共用映像。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d64b5f94dae7ad0213fa231b5603064ad3647da1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82796690"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>使用 Azure CLI 建立共用映射資源庫

[共用映像資源庫](./linux/shared-image-galleries.md)可簡化跨組織共用自訂映像。 自訂映像類似 Marketplace 映像，但您要自行建立它們。 自訂映像可用於啟動程序設定，例如，預先載入應用程式、應用程式設定和其他 OS 設定。 

共用映像庫可讓您與其他人共用您的自訂 VM 映像。 選擇您要共用的映像、您要開放使用的區域，以及您要共用的對象。 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>後續步驟

使用 Azure CLI，從[VM](image-version-vm-cli.md)建立映射版本或[受控映射](image-version-managed-image-cli.md)。

如需共用映像資源庫的詳細資訊，請參閱[概觀](./linux/shared-image-galleries.md)。 若遇到任何問題，請參閱[針對共用映像資源庫問題進行疑難排解](troubleshooting-shared-images.md)。
