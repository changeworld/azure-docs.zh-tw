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
ms.openlocfilehash: e207866b61d21334bc9923d0d784b900906b0045
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539723"
---
1. 登入 [Azure 入口網站](https://portal.azure.com/) ，然後流覽至您想要快照集的磁片。
1. 在您的磁片上，選取 [**建立快照** 集]

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="截圖。您磁片的分頁，並醒目提示 [+ 建立快照] * *，這就是您必須選取的選項。":::

1. 選取您要使用的資源群組，然後輸入名稱。
1. 選取 **增量**，然後選取 [**審核 + 建立**]

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="截圖。建立快照集分頁，填寫名稱並選取累加式，然後建立您的快照集。":::

1. 選取 [建立] 

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="截圖。您的快照集的 [驗證] 頁面，確認您的選取專案，然後建立快照集。":::