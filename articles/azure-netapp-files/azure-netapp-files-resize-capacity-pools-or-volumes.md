---
title: 針對 Azure NetApp Files 調整容量集區或磁碟區的大小 | Microsoft Docs
description: 說明如何變更容量集區或磁碟區的大小。
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
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: c58ceef57b984f46b86bb2a8577c53b75082b78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "65794620"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>調整容量集區或磁碟區的大小
您可以視需要變更容量集區或磁碟區的大小。 

## <a name="resize-the-capacity-pool"></a>調整容量集區的大小 

您可以以 1-TiB 增量或遞減來更改容量池大小。 但是，容量池大小不能小於 4 TiB。 調整容量集區的大小後，將會變更已購買的 Azure NetApp Files 容量。

1. 在 [管理 NetApp 帳戶] 刀鋒視窗中，按一下要調整大小的容量集區。 
2. 以滑鼠右鍵按一下容量集區名稱，或按一下容量集區資料列結尾處的 ["..."] 圖示，以顯示內容功能表。 
3. 使用內容功能表選項來調整大小，或刪除容量集區。

## <a name="resize-a-volume"></a>調整磁碟區的大小

您可以視需要變更磁碟區的大小。 磁碟區的容量耗用量是根據其集區的佈建容量進行計算。

1. 在 [管理 NetApp 帳戶] 刀鋒視窗中，按一下 [磁碟區]****。 
2. 以滑鼠右鍵按一下要調整大小的磁碟區名稱，或按一下磁碟區資料列結尾處的 ["..."] 圖示，以顯示內容功能表。
3. 使用內容功能表選項來調整大小，或刪除磁碟區。

