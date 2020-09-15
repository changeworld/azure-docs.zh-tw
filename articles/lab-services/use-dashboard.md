---
title: 在 Azure 實驗室服務中使用適用于教室實驗室的儀表板 |Microsoft Docs
description: 瞭解如何在 Azure 實驗室服務中使用適用于教室實驗室的儀表板。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 607a3e7faaae65adb67afc0ab0ffd5698adf4655
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90528256"
---
# <a name="dashboard-for-classroom-labs"></a>教室實驗室的儀表板
本文說明 Azure 實驗室服務中的教室實驗室儀表板視圖。 

![螢幕 capature 會顯示 Azure 實驗室服務中教室實驗室的儀表板視圖。](./media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>成本和計費圖格
此磚提供下列成本預估詳細資料：

| 設定 | 值 | 
| ------- | ----- | 
| 配額時數 | 使用者可以在排程的時間以外使用 VM 的時數上限。 |
| 排程時數 | 將會根據實驗室中設定的排程而產生的時數。 只有在所有排程事件上都有設定 [開始]/[結束] 時，才能使用這個值。 |
| 小時/使用者 | 配額時數和已排程時數的總和。 |
| 最大使用者 | 實驗室中的使用者數目上限，以宣告的所有虛擬機器為基礎。 |
| 小時 x 使用者 | 小時/使用者乘以使用者數目。 |
| 調整配額 | 新增至特定使用者之配額時數的總和。 |
| 總時數 * $ 每小時 | 根據所選 VM 大小的每小時成本。 這是根據一般隨用隨付價格。 |
| 預估總成本 | 這是此實驗室的最大價格（以目前的設定為基礎）。 |

## <a name="template-tile"></a>範本磚
您會在此磚上看到下列資訊：

- 範本的建立日期 
- 上次發佈範本的日期 

它也有一個連結可流覽至 **範本** 頁面，您可以在其中 [管理類別的範本 VM](how-to-create-manage-template.md) 。 

## <a name="virtual-machine-pool-tile"></a>虛擬機器集區磚

您會在此磚上看到下列資訊：

- 指派給學生 (使用者) 的虛擬機器數目
- 尚未指派給學生的虛擬機器數目

它也有一個連結可流覽至 [ **虛擬機器集** 區] 頁面，您可以在其中管理實驗室中的 [虛擬機器集](how-to-set-virtual-machine-passwords.md) 區。 

## <a name="users-tile"></a>使用者磚

您會在此磚上看到下列資訊：

- 註冊至類別的使用者數目
- 新增至實驗室但未註冊至類別的使用者數目 

它也有一個連結可流覽至 [ **使用者** ] 頁面，您可以在其中管理實驗室的 [使用者](how-to-configure-student-usage.md) 。 

## <a name="schedules-tile"></a>排程磚
您會在磚上看到實驗室目前已排程的事件。 它也有一個連結可流覽至 [ **排程** ] 頁面，您可以在其中 [建立及管理](how-to-create-schedules.md)排程。 磚只會顯示兩個排程事件的詳細資料，以及實驗室的剩餘排程事件數目。 

![排定的事件](./media/use-dashboard/scheduled-events.png)

