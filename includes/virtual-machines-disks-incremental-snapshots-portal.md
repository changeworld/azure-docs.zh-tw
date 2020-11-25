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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002508"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>區域可用性
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>入口網站


1. 登入 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至您想要快照集的磁片。
1. 在您的磁片上，選取 [**建立快照** 集]

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="截圖。您磁片的分頁，並醒目提示 [+ 建立快照] * *，這就是您必須選取的選項。":::

1. 選取您要使用的資源群組，然後輸入名稱。
1. 選取 **增量**，然後選取 [**審核 + 建立**]

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="截圖。建立快照集分頁，填寫名稱並選取累加式，然後建立您的快照集。":::

1. 選取 [建立] 

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="截圖。您的快照集的 [驗證] 頁面，確認您的選取專案，然後建立快照集。":::

## <a name="next-steps"></a>後續步驟

如果您想要查看使用 .NET 來示範增量快照差異功能的範例程式碼，請參閱 [將 Azure 受控磁碟備份複製到具有增量快照差異功能的另一個區域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
