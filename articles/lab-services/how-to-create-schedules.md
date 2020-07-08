---
title: 在 Azure 實驗室服務中建立教室實驗室的排程 | Microsoft Docs
description: 了解如何在 Azure 實驗室服務中建立教室實驗室的排程，使實驗室中的 VM 可在指定的時間啟動和關閉。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d6dcdd8d09c2c71c2d41aebd358639883ccfb33d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445775"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>在 Azure 實驗室服務中建立和管理教室實驗室的排程 
排程可讓您設定教室實驗室，使實驗室中的 VM 可在指定的時間自動啟動和關閉。 您可以定義一次性排程或週期性排程。 下列程序將為您提供建立和管理教室實驗室排程的步驟： 

> [!IMPORTANT]
> VM 的排程執行時間不會計入[分配給使用者的配額](how-to-configure-student-usage.md#set-quotas-for-users)中。 該配額用於學生在排程時間以外花費於 VM 上的時間。 

## <a name="set-a-schedule-for-the-lab"></a>設定實驗室的排程
建立實驗室的排程事件，讓實驗室中的 VM 在特定時間自動啟動/停止。 您稍早指定的使用者配額，便是會在此排程時間以外指派給每位使用者的額外時間。 

> [!NOTE]
> 開始之前，以下是排程如何影響實驗室虛擬機器： 
>- 排程中未包含範本虛擬機器。 
>- 只有指派的虛擬機器才會啟動。 這表示，如果電腦不是由使用者（student）所宣告，電腦將不會在排程的時間開始。 
>- 所有虛擬機器（不論是否由使用者宣告）都會根據實驗室排程停止。 

1. 切換至 [排程] 頁面，然後選取工具列上的 [新增已排定事件]。 

    ![[排程] 頁面上的 [新增排程] 按鈕](./media/how-to-create-schedules/add-schedule-button.png)
2. 確認已為 [事件類型] 選取 [標準]。 您可以選取 [僅啟動]****，以便僅指定 VM 的啟動時間。 您可以選取 [僅停止]****，以便僅指定 VM 的停止時間。 
7. 在 [重複]**** 區段中，選取目前的排程。 

    ![[排程] 頁面上的 [新增排程] 按鈕](./media/how-to-create-schedules/select-current-schedule.png)
5. 在 [重複] 對話方塊中，執行下列步驟：
    1. 確認已為 [重複] 欄位設定 [每週]。 
    3. 指定 [啟動日期]****。
    4. 指定您想要讓 VM 啟動的 [啟動時間]****。
    5. 指定要關閉 VM 的 [停止時間]****。 
    6. 針對您指定的啟動和停止時間指定 [時區]****。 
    2. 選取您想要讓排程生效的日期。 下列範例中選取了星期一至星期四。 
    8. 選取 [儲存]。 

        ![設定重複排程](./media/how-to-create-schedules/set-repeat-schedule.png)

3. 現在，在 [新增已排定事件] 頁面上的 [附註 (選擇性)] 中，輸入排程的描述或附註。 
4. 在 [新增已排定事件] 頁面上，選取 [儲存]。 

    ![每週排程](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>檢視行事曆中的排程
您可以看到，排程的日期和時間在行事曆檢視中均醒目提示，如下圖所示：

![行事曆檢視中的排程](./media/how-to-create-schedules/schedules-calendar.png)

選取右上角的 [今天]**** 按鈕，可在行事曆中切換至目前的日期。 選取 [向左箭號]**** 可在行事曆中切換至上一週，[向右箭號]**** 則會切換至下一週。 

## <a name="edit-a-schedule"></a>編輯排程
當您在行事曆中選取反白顯示的排程時，您會看到**編輯**或**刪除**排程的按鈕。 

![編輯排程頁面](./media/how-to-create-schedules/schedule-edit-button.png)

在 [**編輯排程的事件**] 頁面上，您可以更新排程，然後選取 [**儲存**]。 

## <a name="delete-a-schedule"></a>刪除排程

1. 若要刪除排程，請在行事曆中選取反白顯示的排程，然後選取垃圾桶圖示（刪除）按鈕：

    ![工具列上的刪除按鈕](./media/how-to-create-schedules/schedule-delete-button.png)
2. 在 [**刪除排程事件**] 對話方塊中，選取 [**是]** 以確認刪除。 



## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
