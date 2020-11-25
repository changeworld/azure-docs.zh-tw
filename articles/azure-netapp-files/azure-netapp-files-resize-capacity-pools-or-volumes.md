---
title: 針對 Azure NetApp Files 調整容量集區或磁碟區的大小 | Microsoft Docs
description: 瞭解如何變更容量集區或磁片區的大小。 調整容量集區的大小後，將會變更已購買的 Azure NetApp Files 容量。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 62bf154c1dbf1a0d3f12e2cef916b37059ce985b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012471"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>調整容量集區或磁碟區的大小
您可以視需要變更容量集區或磁碟區的大小。 

## <a name="resize-the-capacity-pool"></a>調整容量集區的大小 

您可以用 1 TiB 增量或遞減方式變更容量集區大小。 不過，容量集區大小不能小於 4 TiB。 調整容量集區的大小後，將會變更已購買的 Azure NetApp Files 容量。

1. 在 [管理 NetApp 帳戶] 刀鋒視窗中，按一下要調整大小的容量集區。 
2. 以滑鼠右鍵按一下容量集區名稱，或按一下容量集區資料列結尾處的 ["..."] 圖示，以顯示內容功能表。 
3. 使用內容功能表選項來調整大小，或刪除容量集區。

## <a name="resize-a-volume"></a>調整磁碟區的大小

您可以視需要變更磁碟區的大小。 磁碟區的容量耗用量是根據其集區的佈建容量進行計算。

1. 在 [管理 NetApp 帳戶] 刀鋒視窗中，按一下 [磁碟區]。 
2. 以滑鼠右鍵按一下要調整大小的磁碟區名稱，或按一下磁碟區資料列結尾處的 ["..."] 圖示，以顯示內容功能表。
3. 使用內容功能表選項來調整大小，或刪除磁碟區。

## <a name="next-steps"></a>後續步驟

- [設定容量集區](azure-netapp-files-set-up-capacity-pool.md)
- [管理手動 QoS 容量集區](manage-manual-qos-capacity-pool.md)
- [動態變更磁碟區的服務等級](dynamic-change-volume-service-level.md) 