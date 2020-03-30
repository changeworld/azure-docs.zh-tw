---
title: 返回或替換 Azure 資料框邊緣設備 |微軟文檔
description: 描述如何返回或替換 Azure 資料框邊緣設備。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: 521277b2eed7edfba016f6a80e8f877decfb0ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651095"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>返回或替換 Azure 資料框邊緣設備

本文介紹如何擦除資料，然後返回 Azure 資料框邊緣設備。 返回設備後，還可以刪除與設備關聯的資源或訂購替換設備。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 從設備上的資料磁片上擦除資料
> * 打開支援票證以退回設備
> * 打包設備並安排取件
> * 刪除 Azure 門戶中的資源
> * 獲取更換設備

## <a name="erase-data-from-the-device"></a>從設備擦除資料

要擦除設備資料磁片上的資料，您需要重置設備。 您可以使用本地 Web UI 或 PowerShell 介面重置設備。

重置之前，如果需要，請創建設備上本地資料的副本。 可以將資料從設備複製到 Azure 存儲容器。

要使用本地 Web UI 重置設備，請執行以下步驟。

1. 在本地 Web UI 中，轉到**維護>設備重置**。
2. 選擇**重置設備**。

    ![重設裝置](media/data-box-edge-return-device/device-reset-1.png)

3. 當提示確認時，查看警告並選擇 **"是**"以繼續。

    ![確認重置](media/data-box-edge-return-device/device-reset-2.png)  

重置會擦除設備資料磁片上的資料。 根據設備上的資料量，此過程大約需要 30-40 分鐘。

或者，連接到設備的 PowerShell 介面，`Reset-HcsAppliance`並使用 Cmdlet 從資料磁片擦除資料。 有關詳細資訊，請參閱[重置設備](data-box-edge-connect-powershell-interface.md#reset-your-device)。

> [!NOTE]
> - 如果要交換或升級到新設備，我們建議您僅在收到新設備後重置設備。
> - 設備重置僅刪除設備中的所有本地資料。 雲中的資料不會被刪除並收取[費用](https://azure.microsoft.com/pricing/details/storage/)。 此資料需要使用[Azure 存儲資源管理器](https://azure.microsoft.com/features/storage-explorer/)等雲存儲管理工具單獨刪除。

## <a name="open-a-support-ticket"></a>打開支援票證

要開始返回過程，請執行以下步驟。

1. 使用 Microsoft 支援打開支援票證，指示您希望返回設備。 選擇問題類型作為**資料框邊緣硬體**。

    ![開啟支援票證](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. 微軟支援工程師將與您聯繫。 提供運輸詳細資訊。
3. 如果您需要退貨運輸箱，您可以請求它。 回答**是**，問題**需要一個空框返回**。


## <a name="schedule-a-pickup"></a>安排取件

1. 關閉裝置。 在本機 Web UI 中，移至 [維護] > [電源設定]****。
2. 選擇 **"關閉**"。 當提示確認時，按一下"**是**"以繼續。 有關詳細資訊，請參閱[管理電源](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)。
3. 拔下電源線，然後從設備上取下所有網路電纜。
4. 使用自己的框或從 Azure 收到的空框準備裝運包。 將設備隨設備隨附的電源線放入包裝盒中。
5. 在包上粘貼從 Azure 收到的出貨標籤。
6. 與區域性貨運公司排定取貨時間。 如果在美國退回設備，您的運營商可能是 UPS 或 FedEx。 要安排 UPS 的取件：

    1. 致電給當地的 UPS (國家/地區特定的免付費電話號碼)。
    2. 在通話中，請引用列印標籤上所示的反向貨件跟蹤號碼。
    3. 如果未引用追蹤號碼，UPS 將要求您在取件期間支付額外費用。

    您還可以將資料框邊緣放在最近的放置位置，而不是安排取件。

## <a name="delete-the-resource"></a>刪除資源

在 Azure 資料中心接收設備後，將檢查設備是否有損壞或任何篡改跡象。

- 如果設備完好無損地到達，並且狀況良好，則該資源的計費表將停止。 Microsoft 支援部門將與您聯繫以確認設備已退回。 然後，您可以在 Azure 門戶中刪除與設備關聯的資源。
- 如果設備嚴重損壞，可能會處以罰款。 有關詳細資訊，請參閱[有關丟失或損壞的設備](https://azure.microsoft.com/pricing/details/databox/edge/)以及[產品服務條款的](https://www.microsoft.com/licensing/product-licensing/products)常見問題解答。  


您可以在 Azure 門戶中刪除設備：
-   下訂單後，在設備由 Microsoft 準備之前。
-   將設備返回到 Microsoft 後，它將在 Azure 資料中心通過物理檢查，然後通過 Microsoft 支援呼叫以確認設備已返回。

如果您已針對其他訂閱或位置啟動設備，Microsoft 將在一個工作日內將您的訂單移動到新訂閱或位置。 移動訂單後，可以刪除此資源。


執行以下步驟刪除 Azure 門戶中的設備和資源。

1. 在 Azure 門戶中，轉到資源，然後轉到 **"概述**"。 從命令列中，選擇 **"刪除**"。

    ![選取 [刪除]](media/data-box-edge-return-device/delete-resource-1.png)

2. 在 **"刪除設備"** 邊欄選項卡中，鍵入要刪除的設備的名稱，然後選擇 **"刪除**"。

    ![Confirm delete](media/data-box-edge-return-device/delete-resource-2.png)

設備並成功刪除關聯的資源後，您將收到通知。

## <a name="get-a-replacement-device"></a>獲取更換設備

當現有設備出現硬體故障或需要升級時，需要更換設備。 當設備出現硬體問題時，請執行以下步驟：

1. [打開硬體問題的支援票證](#open-a-support-ticket)。 Microsoft 支援將確定現場替換單元 （FRU） 不適用於此實例或設備需要硬體升級。 在這兩種情況下，支援將訂購更換設備。
2. [為替換設備創建新資源](data-box-edge-deploy-prep.md#create-a-new-resource)。 請確保選擇核取方塊反對**我有一個資料框邊緣設備**。 
3. 收到更換設備後，根據新資源[安裝](data-box-edge-deploy-install.md)並[啟動](data-box-edge-deploy-connect-setup-activate.md)更換設備。
4. 按照所有步驟返回原始設備：
    1. 打開另一張票證以返回原始設備。
    2. [擦除設備上的資料](#erase-data-from-the-device)。
    3. [安排取件](#schedule-a-pickup)。
    5. [刪除與](#delete-the-resource)返回的設備關聯的資源。



## <a name="next-steps"></a>後續步驟

- 了解如何[管理頻寬](data-box-edge-manage-bandwidth-schedules.md)。
