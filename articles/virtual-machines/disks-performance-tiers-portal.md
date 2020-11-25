---
title: 使用 Azure 入口網站變更 Azure 受控磁片的效能
description: 瞭解如何使用 Azure 入口網站來變更新的和現有受控磁片的效能層級。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/19/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a868d5d236cf4c5a8d29f15490909c2f1a53546f
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "96016560"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>使用 Azure 入口網站變更效能層級

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>開始使用

### <a name="new-disks"></a>新磁片

下列步驟顯示當您第一次建立磁片時，如何變更磁片的效能層級：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 流覽至您想要為其建立新磁片的 VM。
1. 選取新磁片時，請先選擇您需要的磁片大小。
1. 選取大小之後，請選取不同的效能層級來變更其效能。
1. 選取 **[確定]** 以建立磁片。

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="磁片建立分頁的螢幕擷取畫面，已反白顯示磁片，[效能層級] 下拉式清單已反白顯示。" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>現有的磁片

下列步驟概述如何變更現有磁片的效能層級：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 流覽至包含您要變更之磁片的 VM。
1. 請解除配置 VM 或卸離磁片。
1. 選取您的磁片
1. 選取 [ **大小 + 效能**]。
1. 在 [ **效能層** ] 下拉式清單中，選取與磁片目前基準不同的層級。
1. 選取 [調整大小]。

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="[大小 + 效能] 分頁的螢幕擷取畫面，其中會反白顯示效能層級。" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>後續步驟

如果您需要調整磁片大小以利用較高的效能層級，請參閱下列文章：

- [使用 Azure CLI 擴充 Linux VM 上的虛擬硬碟](linux/expand-disks.md)
- [擴充連接至 Windows 虛擬機器的受控磁片](windows/expand-os-disk.md)
