---
title: 在 Azure 實驗室服務中使用適用于教室實驗室的儀表板 |Microsoft Docs
description: 瞭解如何在 Azure 實驗室服務中使用適用于教室實驗室的儀表板。
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
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77538935"
---
# <a name="dashboard-for-classroom-labs"></a>教室實驗室的儀表板
本文說明 Azure 實驗室服務中教室實驗室的儀表板視圖。 

![儀表板](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>成本和計費磚
此磚提供下列成本預估詳細資料：

| 設定 | 值 | 
| ------- | ----- | 
| 配額時數 | 使用者可以在排程的時數以外使用 VM 的時數上限。 |
| 排程時數 | 將根據實驗室中設定的排程產生的時數。 只有在所有排程事件上都有設定 [從]/[到] 日期時，才可以使用此值。 |
| 小時/使用者 | 配額時數和排程時數的總和。 |
| 使用者人數上限 | 實驗室中以要宣告的所有虛擬機器為基礎的最大使用者數目。 |
| 小時 x 使用者 | 小時/使用者乘以使用者數目。 |
| 已調整配額 | 新增至特定使用者的配額時數總和。 |
| 總時數 * $/小時 | 根據所選 VM 大小的每小時成本。 這是以一般隨用隨付價格為基礎。 |
| 預估總成本 | 這是以目前的設定為基礎的此實驗室的最大價格。 |

## <a name="template-tile"></a>範本磚
您會在此磚上看到下列資訊：

- 建立範本的日期 
- 上次發佈範本的日期 

它也有連結可流覽至 [**範本**] 頁面，您可以在其中管理類別的[範本 VM](how-to-create-manage-template.md) 。 

## <a name="virtual-machine-pool-tile"></a>虛擬機器集區磚

您會在此磚上看到下列資訊：

- 指派給學生的虛擬機器數目（使用者）
- 尚未指派給學生的虛擬機器數目

它也有連結可流覽至 [**虛擬機器集**區] 頁面，您可以在其中管理實驗室中的[虛擬機器集](how-to-set-virtual-machine-passwords.md)區。 

## <a name="users-tile"></a>使用者磚

您會在此磚上看到下列資訊：

- 向類別註冊的使用者數目
- 已新增至實驗室但未向類別註冊的使用者數目 

它也有連結可流覽至 [**使用者**] 頁面，您可以在其中管理實驗室的[使用者](how-to-configure-student-usage.md)。 

## <a name="schedules-tile"></a>排程磚
您會在圖格上看到實驗室目前已排程的事件。 它也有一個連結，可流覽至 [排程] 頁面，您可以在其中[建立和管理](how-to-create-schedules.md)**排程**。 此圖格只會顯示兩個已排程事件的詳細資料，以及實驗室的剩餘排程事件數。 

![排定的事件](../media/use-dashboard/scheduled-events.png)

