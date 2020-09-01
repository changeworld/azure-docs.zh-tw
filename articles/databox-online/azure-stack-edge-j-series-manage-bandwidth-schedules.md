---
title: Azure Stack Edge GPU 管理頻寬排程 |Microsoft Docs
description: 說明如何使用 Azure 入口網站管理 Azure Stack Edge GPU 上的頻寬排程。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: b27c4fb9beb631b40617307cfc5952b7dcb45369
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147043"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge"></a>使用 Azure 入口網站來管理您 Azure Stack Edge 上的頻寬排程 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本文說明如何管理 Azure Stack Edge 上的頻寬排程。 頻寬排程可讓您設定多個日期時間排程的網路頻寬使用量。 這些排程可以套用至從您的裝置上傳和下載作業到雲端。

您可以透過 Azure 入口網站來新增、修改或刪除 Azure Stack Edge 的頻寬排程。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 新增排程
> * 修改排程
> * 刪除排程


## <a name="add-a-schedule"></a>新增排程

在 Azure 入口網站中執行下列步驟，以新增排程。

1. 在 Azure Stack Edge 資源的 Azure 入口網站中，移至 [ **頻寬**]。
2. 在右窗格中，選取 [+ 新增排程]****。

    ![選取頻寬](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-1.png)

3. 在 [新增排程]**** 中： 

   1. 提供排程的 [開始日期]****、[結束日期]****、[開始時間]**** 及[結束時間]****。
   2. 如果此排程應該整天執行，請勾選 [全天]****。
   3. [頻寬速率]**** 是以 MB / 秒 (Mbps) 為單位的頻寬，由您的裝置使用於涉及雲端的作業 (上傳與下載)。 請為此欄位提供20到2147483647之間的數位。
   4. 如果您不想節流處理上傳和下載日期，請勾選 [無限制]**** 頻寬。
   5. 選取 [新增]。

      ![新增排程](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-2.png)

3. 使用指定的設定建立排程。 此排程即會顯示在入口網站中的頻寬排程清單中。

    ![頻寬排程的更新清單](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>編輯排程

執行下列步驟來編輯頻寬排程。

1. 在 Azure 入口網站中，移至您的 Azure Stack Edge 資源，然後移至 [ **頻寬**]。 
2. 從頻寬排程清單中，選取您要修改的排程。
    ![選取頻寬排程](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-1.png)

3. 進行所要的變更並儲存變更。

    ![修改使用者](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-2.png)

4. 修改排程之後，排程清單就會更新，以反映出修改過的排程。

    ![修改使用者](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>刪除排程

請執行下列步驟，以刪除與您的 Azure Stack Edge 裝置相關聯的頻寬排程。

1. 在 Azure 入口網站中，移至您的 Azure Stack Edge 資源，然後移至 [ **頻寬**]。  

2. 從頻寬排程清單中，選取您想要刪除的排程。 在 [編輯排程]**** 中，選取 [刪除]****。 系統提示您確認時，請選取 [是]****。

   ![刪除使用者](media/azure-stack-edge-j-series-manage-bandwidth-schedules/delete-schedule-2.png)

3. 刪除排程之後，排程清單就會更新。


## <a name="next-steps"></a>接下來的步驟

- 了解如何[管理共用](azure-stack-edge-j-series-manage-shares.md)。
