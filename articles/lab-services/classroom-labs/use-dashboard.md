---
title: 在 Azure 實驗室服務中使用教室實驗室的儀表板 |微軟文檔
description: 瞭解如何在 Azure 實驗室服務中使用教室實驗室的儀表板。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 363e5705e9ccf4aa207ff7e5cafb615bc01bc7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538935"
---
# <a name="dashboard-for-classroom-labs"></a>教室實驗室儀表板
本文介紹了 Azure 實驗室服務中教室實驗室的儀表板視圖。 

![儀表板](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>成本和計費磁貼
此磁貼提供以下成本估算詳細資訊：

| 設定 | 值 | 
| ------- | ----- | 
| 配額時數 | 使用者可以在計畫工時之外使用 VM 的最大小時數。 |
| 預定時間 | 將根據實驗室中設置的計畫發生的小時數。 僅當所有計劃事件都設置了從/到日期時，此值才可用。 |
| 小時/使用者 | 配額小時和計畫工時的總和。 |
| 最大使用者數 | 基於要聲明的所有虛擬機器，實驗室中的最大使用者數。 |
| 小時 x 使用者 | 工時/使用者乘以使用者數。 |
| 調整後的配額 | 添加到特定使用者的配額小時數的總和。 |
| 總時數 = $/小時 | 基於所選 VM 大小每小時的成本。 這是基於正常支付，你去價格。 |
| 估計費用總額 | 這是此實驗室基於當前設置的最高價格。 |

## <a name="template-tile"></a>範本磁貼
有關此磁貼的以下資訊：

- 創建範本的日期 
- 範本上次發佈的日期 

它還具有一個連結，用於導航到**範本**頁面，您可以在該頁面[管理類的範本 VM。](how-to-create-manage-template.md) 

## <a name="virtual-machine-pool-tile"></a>虛擬機器池磁貼

有關此磁貼的以下資訊：

- 分配給學生（使用者）的虛擬機器數
- 尚未分配給學生的虛擬機器數

它還具有一個連結，用於導航到**虛擬機器池**頁面，您可以在該頁面[管理實驗室中的虛擬機器池](how-to-set-virtual-machine-passwords.md)。 

## <a name="users-tile"></a>使用者磁貼

有關此磁貼的以下資訊：

- 註冊到類的使用者數
- 添加到實驗室但未註冊到類的使用者數 

它還具有一個連結，用於導航到 **"使用者"** 頁面，您可以在該頁面[管理實驗室的使用者](how-to-configure-student-usage.md)。 

## <a name="schedules-tile"></a>計畫磁貼
在磁貼上可以看到實驗室的當前計畫事件。 它還有一個連結，以導航到 **"計畫"** 頁，您可以在其中[創建和管理計畫](how-to-create-schedules.md)。 磁貼僅顯示兩個計畫事件的詳細資訊以及實驗室的剩餘計畫事件數。 

![排定的事件](../media/use-dashboard/scheduled-events.png)

