---
title: 退回您的 Azure Stack Edge Pro 裝置 |Microsoft Docs
description: 瞭解如何抹除資料並傳回您的 Azure Stack Edge Pro 裝置，然後刪除與裝置相關聯的資源。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/27/2020
ms.author: alkohli
ms.openlocfilehash: 0c35bde40cac9629f084d69d52f119651b5655f7
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784472"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>退回您的 Azure Stack Edge Pro 裝置

本文說明如何抹除資料，然後傳回 Azure Stack Edge Pro 裝置。 傳回裝置之後，您也可以刪除與裝置相關聯的資源。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 將資料從裝置上的資料磁碟抹除
> * 在 Azure 入口網站中起始裝置退回
> * 包裝裝置並安排取貨
> * 在 Azure 入口網站中刪除資源

## <a name="erase-data-from-the-device"></a>清除裝置中的資料

若要將資料從您裝置的資料磁碟抹除，您必須將裝置重設。 您可使用本機 Web UI 或 PowerShell 介面來重設裝置。

重設之前，請先在裝置上建立本機資料的複本 (如有需要)。 您可將資料從裝置複製到 Azure 儲存體容器。

您可以在裝置重設之前，先起始裝置的退貨。 

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

## <a name="initiate-device-return"></a>起始裝置退回

若要開始退貨程序，請採取下列步驟。

1. 移至 Azure 入口網站中的 Azure Stack Edge Pro/Data Box Gateway 資源。 在 [ **總覽**] 中，移至右窗格中的命令列，然後選取 [傳回 **裝置**]。 

    ![退回裝置1](media/azure-stack-edge-return-device/return-device-1.png)  

2. 在 [傳回 **裝置** ] 分頁中，于 [ **基本詳細資料**] 底下：

    1. 提供裝置的序號。 若要取得裝置序號，請移至裝置的本機 web UI，然後移至 **[總覽**]。  
    
    ![裝置序號1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. 輸入服務標籤號碼，這是您裝置唯一的五個或更多個字元識別碼。 當您面對裝置) 時，服務標籤位於裝置 (的右下角。 將資訊標記拉出 (它是一個滑出標籤面板) 。 此面板包含系統資訊，例如服務標籤、NIC、MAC 位址等等。 
    
    ![服務標記編號1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. 從下拉式清單中，選擇退貨的原因。

    ![退貨裝置2](media/azure-stack-edge-return-device/return-device-2.png) 

3. 在 [出 **貨詳細資料**] 底下：

    1. 提供您的姓名、公司名稱和完整的公司位址。 輸入公司電話，包括區碼和通知的電子郵件識別碼。
    2. 如果需要退貨運送盒，則可提出要求。 對於 [需要空盒進行退貨] 這個問題，回答 [是]。

    ![退回裝置3](media/azure-stack-edge-return-device/return-device-3.png)

4. 請參閱 **隱私權條款** ，並針對您已檢查並同意隱私權條款的附注選取核取方塊。

5. 選取 [ **起始** 傳回]。

    ![退貨裝置4](media/azure-stack-edge-return-device/return-device-4.png) 

6. 一旦捕捉到您的裝置傳回詳細資料，您可以透過電子郵件通知 Azure Stack Edge Pro 作業小組。 假設已安裝並設定電子郵件應用程式，您可以使用您的電子郵件應用程式。 您也可以複製資料來建立和傳送電子郵件。

    ![退回裝置5](media/azure-stack-edge-return-device/return-device-5.png) 

7. 當 Azure Stack Edge Pro 營運團隊收到電子郵件之後，他們會將反向出貨標籤傳送給您。 當您收到此標籤時，您可以使用貨運公司來排程裝置取貨。 

## <a name="schedule-a-pickup"></a>安排取貨

若要排程取貨，請採取下列步驟。

1. 關閉裝置。 在本機 Web UI 中，移至 [維護] > [電源設定]。
2. 選取 [關機]。 系統提示您進行確認時，按一下 [是] 以繼續。 如需詳細資訊，請參閱[管理電源](../databox-gateway/data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)。
3. 拔下電源線，並移除與裝置相連的所有網路線。
4. 使用您自己的盒子或 Azure 所提供的空盒來準備運送包裝。 將裝置和裝置隨附的電源線放在盒子中。
5. 將 Azure 提供的運送標籤貼到包裝上。
6. 與區域性貨運公司排定取貨時間。 如果在美國退回裝置，您的運送廠商可能是 UPS 或 FedEx。 若要與 UPS 安排取貨：

    1. 致電給當地的 UPS (國家/地區特定的免付費電話號碼)。
    2. 在您的電話中提供反向出貨追蹤號碼，如您印出的標籤所示。
    3. 若未提供追蹤號碼，UPS 將在取貨期間要求您支付額外的費用。

    您也可以在最接近的放置位置卸載 Azure Stack Edge Pro，而不是排程取貨。

## <a name="delete-the-resource"></a>刪除資源

Azure 資料中心收到裝置後，就會檢查裝置是否受損或有任何篡改跡象。

- 如果裝置未完整地送達，而且是良好的形狀，則會停止該資源的計費計量。 Azure Stack Edge Pro 營運小組將會洽詢您，以確認裝置已傳回。 然後，您可以在 Azure 入口網站中刪除與裝置相關聯的資源。
- 如果裝置送達時明顯受損，則可能會酌收罰款。 如需詳細資訊，請參閱[裝置遺失或受損的常見問題集](https://azure.microsoft.com/pricing/details/databox/edge/)和[產品服務條款](https://www.microsoft.com/licensing/product-licensing/products)。  


您可以在 Azure 入口網站中刪除裝置：

- 在您下訂單之後，以及在 Microsoft 準備好裝置之前。
- 當您將裝置傳回給 Microsoft 之後，它會在 Azure 資料中心傳遞實體檢查，並 Azure Stack Edge Pro 作業小組呼叫來確認裝置已傳回。

如果您已針對另一個訂用帳戶或位置啟用該裝置，Microsoft 會在一個工作天內將您的訂單移至新的訂用帳戶或位置。 移轉訂單之後，您即可刪除此資源。


請在 Azure 入口網站中採取下列步驟來刪除裝置和資源。

1. 在 Azure 入口網站中，移至您的資源，然後移至 [概觀]。 在命令列中選取 [刪除]。

    ![選取 [刪除]](media/azure-stack-edge-return-device/delete-resource-1.png)

2. 在 [刪除裝置] 刀鋒視窗中，輸入您要刪除的裝置名稱，然後選取 [刪除]。

    ![Confirm delete](media/azure-stack-edge-return-device/delete-resource-2.png)

您會在裝置和相關聯的資源成功刪除後收到通知。


## <a name="next-steps"></a>後續步驟

- 瞭解如何 [取得更換 Azure Stack Edge Pro 裝置](azure-stack-edge-replace-device.md)。