---
title: 在 Azure 資料箱閘道上管理頻寬排程 | Microsoft Docs
description: 描述如何使用 Azure 入口網站來管理 Azure 資料箱閘道上的頻寬排程。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 8ccc9725ffe5304942b740d460a67129f9c1c5d8
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743840"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-gateway"></a>使用 Azure 入口網站來管理 Azure 資料箱閘道上的頻寬排程  

本文說明如何管理 Azure 資料箱閘道上的使用者。 頻寬排程可讓您設定多個日期時間排程的網路頻寬使用量。 這些排程可以套用至從您的裝置上傳和下載作業到雲端。

您可以透過 Azure 入口網站，新增、修改或刪除資料箱閘道的頻寬排程。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 新增排程
> * 修改排程
> * 刪除排程

## <a name="add-a-schedule"></a>新增排程

在 Azure 入口網站中執行下列步驟，以新增使用者。

1. 在 Azure 入口網站中，針對您的資料箱閘道資源，移至 [頻寬]****。
2. 在右窗格中，按一下 [+ 新增排程]****。

    ![按一下 [新增使用者]](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-1.png)

3. 在 [新增排程]**** 中： 

   1. 提供排程的 **開始日期**、 **結束日期**、 **開始時間**和 **結束時間** 。 
   2. 如果此排程全天執行，則您可以勾選 [全天]**** 選項。 
   3. **頻寬速率** 是您的裝置在牽涉到雲端 (上傳和下載) 時，每秒 Mb (Mbps) 的頻寬。 提供一個介於 1 到 1000 之間的數目給此欄位。 
   4. 如果您不想節流處理上傳和下載日期，請勾選 [無限制]**** 頻寬。 
   5. 按一下 [新增] 。

      ![按一下 [新增使用者 2]](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-2.png)

3. 使用指定的設定建立排程。 此排程即會顯示在入口網站中的頻寬排程清單中。


## <a name="edit-schedule"></a>編輯排程

執行下列步驟來編輯頻寬排程。 

1. 在 Azure 入口網站中，移至您的資料箱閘道資源，然後移至 [頻寬]。 
2. 從頻寬排程清單中，選取並按一下您想要修改的排程。
    ![修改使用者](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-1.png)

3. 進行所要的變更並儲存變更。

    ![修改使用者2](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-2.png)

4. 修改排程之後，排程清單就會更新，以反映出修改過的排程。

    ![修改使用者3](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>刪除排程

執行下列步驟來刪除與資料箱閘道裝置相關聯的頻寬排程。

1. 在 Azure 入口網站中，移至您的資料箱閘道資源，然後移至 [ **頻寬**]。  

2. 從頻寬排程清單中，選取您想要刪除的排程。 以滑鼠右鍵按一下以顯示操作功能表，然後按一下 [刪除]****。 

   ![刪除使用者](media/data-box-gateway-manage-bandwidth-schedules/delete-schedule-1.png)

3.  刪除排程之後，排程清單就會更新。



## <a name="next-steps"></a>後續步驟

- 了解如何[管理頻寬](data-box-gateway-manage-bandwidth-schedules.md)。 
