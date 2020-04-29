---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80628399"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>區域可用性
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>入口網站


1. 登入[Azure 入口網站](https://portal.azure.com/)，然後流覽至您想要快照的磁片。
1. 在您的磁片上，選取 [**建立快照**集]

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="截取.您的磁片分頁會反白顯示 * * + 建立快照集 * *，因為這就是您必須選取的功能。":::

1. 選取您想要使用的資源群組，然後輸入名稱。
1. 選取 [**增量**] 並選取 [**審核] + [建立**]

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="截取.建立快照分頁，填入名稱並選取 [增量]，然後建立快照集。":::

1. 選取 [**建立**]

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="截取.您的快照集的驗證頁面，確認您的選擇，然後建立快照集。":::

## <a name="next-steps"></a>後續步驟

如果您想要查看示範增量快照集差異功能的範例程式碼，請使用 .NET，請參閱[將 Azure 受控磁碟備份複製到具有增量快照差異功能的另一個區域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
