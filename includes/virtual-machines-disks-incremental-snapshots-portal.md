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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628399"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>區域可用性
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>入口網站


1. 登錄到[Azure 門戶](https://portal.azure.com/)並導航到要快照的磁碟。
1. 在磁碟上,選擇 **"創建快照**"

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="截圖。磁碟的邊欄選項卡,突出顯示 [創建快照],因為您必須選擇。":::

1. 選擇要使用的資源組並輸入名稱。
1. 選擇**增量**並選擇 **"查看 + 創建"**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="截圖。建立快照邊欄選項卡,填寫名稱並選擇增量,然後創建快照。":::

1. 選擇 **"創建"**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="截圖。驗證快照的驗證頁,確認您的選擇,然後創建快照。":::

## <a name="next-steps"></a>後續步驟

如果要檢視範例代碼,展示使用 .NET 的增量快照的差分功能,請參閱[將 Azure 託管磁碟備份複製到具有增量快照差異功能的另一個區域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
