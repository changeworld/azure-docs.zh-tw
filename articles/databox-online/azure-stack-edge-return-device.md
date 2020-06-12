---
title: 退回或更換 Azure Stack Edge 裝置 | Microsoft Docs
description: 說明如何退回或更換 Azure Stack Edge 裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: bb73494dd5fe22c3be645f732f9d0958e48edb64
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743628"
---
# <a name="return-or-replace-your-azure-stack-edge-device"></a>退回或更換 Azure Stack Edge 裝置

本文說明如何抹除資料，然後退回您的 Azure Stack Edge 裝置。 退回裝置之後，您也可刪除與裝置相關聯的資源，或訂購替換裝置。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 將資料從裝置上的資料磁碟抹除
> * 開啟支援票證以退回您的裝置
> * 包裝裝置並安排取貨
> * 在 Azure 入口網站中刪除資源
> * 取得替換裝置

## <a name="erase-data-from-the-device"></a>清除裝置中的資料

若要將資料從您裝置的資料磁碟抹除，您必須將裝置重設。 您可使用本機 Web UI 或 PowerShell 介面來重設裝置。

重設之前，請先在裝置上建立本機資料的複本 (如有需要)。 您可將資料從裝置複製到 Azure 儲存體容器。

若要使用本機 Web UI 重設裝置，請執行下列步驟。

1. 在本機 Web UI 中，移至 [維護] > [裝置重設]。
2. 選取 [重設裝置]。

    ![重設裝置](media/azure-stack-edge-return-device/device-reset-1.png)

3. 當系統提示您確認時，請檢閱警告並選取 [是] 以便繼續。

    ![確認重設](media/azure-stack-edge-return-device/device-reset-2.png)  

重設會將資料從裝置資料磁碟清除。 視裝置上的資料量而定，此程序大約需要 30-40 分鐘的時間。

或者，連線到裝置的 PowerShell 介面，並使用 `Reset-HcsAppliance` Cmdlet 來清除資料磁碟中的資料。 如需相關資訊，請參閱[重設裝置](azure-stack-edge-connect-powershell-interface.md#reset-your-device)。

> [!NOTE]
> - 如果您要交換或升級至新裝置，建議您只有在收到新裝置後重設裝置。
> - 裝置重設只會刪除裝置上的所有本機資料。 雲端的資料不會遭到刪除，也不會收取[費用](https://azure.microsoft.com/pricing/details/storage/)。 此資料必須使用雲端儲存體管理工具 (例如 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)) 分開刪除。

## <a name="open-a-support-ticket"></a>開啟支援票證

若要開始退貨程序，請採取下列步驟。

1. 向 Microsoft 支援服務開啟支援票證，表示您想要退回裝置。 針對問題類型選取 [Azure Stack Edge 硬體]。

    ![開啟支援票證](media/azure-stack-edge-return-device/open-support-ticket-1.png)  

2. Microsoft 支援服務將會與您連絡。 提供運送詳細資料。
3. 如果需要退貨運送盒，則可提出要求。 對於 [需要空盒進行退貨] 這個問題，回答 [是]。


## <a name="schedule-a-pickup"></a>安排取貨

1. 關閉裝置。 在本機 Web UI 中，移至 [維護] > [電源設定]。
2. 選取 [關機]。 系統提示您進行確認時，按一下 [是] 以繼續。 如需詳細資訊，請參閱[管理電源](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)。
3. 拔下電源線，並移除與裝置相連的所有網路線。
4. 使用您自己的盒子或 Azure 所提供的空盒來準備運送包裝。 將裝置和裝置隨附的電源線放在盒子中。
5. 將 Azure 提供的運送標籤貼到包裝上。
6. 與區域性貨運公司排定取貨時間。 如果在美國退回裝置，您的運送廠商可能是 UPS 或 FedEx。 若要與 UPS 安排取貨：

    1. 致電給當地的 UPS (國家/地區特定的免付費電話號碼)。
    2. 在您的電話中提供反向出貨追蹤號碼，如您印出的標籤所示。
    3. 若未提供追蹤號碼，UPS 將在取貨期間要求您支付額外的費用。

    除了安排取貨，您也可以在最接近的托運地點托運 Azure Stack Edge。

## <a name="delete-the-resource"></a>刪除資源

Azure 資料中心收到裝置後，就會檢查裝置是否受損或有任何篡改跡象。

- 如果裝置完整無缺地送達而且外形良好，則會停止該資源的計費計量。 Microsoft 支援服務會與您連絡，確認裝置已退回。 然後，您可以在 Azure 入口網站中刪除與裝置相關聯的資源。
- 如果裝置送達時明顯受損，則可能會酌收罰款。 如需詳細資訊，請參閱[裝置遺失或受損的常見問題集](https://azure.microsoft.com/pricing/details/databox/edge/)和[產品服務條款](https://www.microsoft.com/licensing/product-licensing/products)。  


您可以在 Azure 入口網站中刪除裝置：
-    在您下訂單之後，以及在 Microsoft 準備好裝置之前。
-    將裝置退回給 Microsoft 之後，其會在 Azure 資料中心通過實體檢查，且 Microsoft 支援服務會來電確認裝置已退回。

如果您已針對另一個訂用帳戶或位置啟用該裝置，Microsoft 會在一個工作天內將您的訂單移至新的訂用帳戶或位置。 移轉訂單之後，您即可刪除此資源。


請在 Azure 入口網站中採取下列步驟來刪除裝置和資源。

1. 在 Azure 入口網站中，移至您的資源，然後移至 [概觀]。 在命令列中選取 [刪除]。

    ![選取 [刪除]](media/azure-stack-edge-return-device/delete-resource-1.png)

2. 在 [刪除裝置] 刀鋒視窗中，輸入您要刪除的裝置名稱，然後選取 [刪除]。

    ![Confirm delete](media/azure-stack-edge-return-device/delete-resource-2.png)

您會在裝置和相關聯的資源成功刪除後收到通知。

## <a name="get-a-replacement-device"></a>取得替換裝置

當現有裝置發生硬體故障或需要升級時，就需要替換裝置。 當裝置發生硬體問題時，請採取下列步驟：

1. [開啟硬體問題的支援票證](#open-a-support-ticket)。 Microsoft 支援服務將判斷現場替換單元 (FRU) 不適用於此執行個體，或裝置需要硬體升級。 不論是哪種情況，支援服務都會訂購替換裝置。
2. 為替換裝置[建立新資源](azure-stack-edge-deploy-prep.md#create-a-new-resource)。 請務必針對 [我有 Azure Stack Edge 裝置]，選取此核取方塊。 
3. 當您收到替換裝置後，請針對新資源[安裝](azure-stack-edge-deploy-install.md)並[啟動](azure-stack-edge-deploy-connect-setup-activate.md)替換裝置。
4. 依照所有步驟來退回原始裝置：
    1. 開啟另一個票證以退回原始裝置。
    2. [清除裝置上的資料](#erase-data-from-the-device)。
    3. [安排取貨](#schedule-a-pickup)。
    5. [刪除與所退回裝置相關聯的資源](#delete-the-resource)。



## <a name="next-steps"></a>後續步驟

- 了解如何[管理頻寬](azure-stack-edge-manage-bandwidth-schedules.md)。
