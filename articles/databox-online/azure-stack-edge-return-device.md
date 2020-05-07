---
title: 傳回或取代您的 Azure Stack Edge 裝置 |Microsoft Docs
description: 描述如何傳回或取代 Azure Stack Edge 裝置。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: dfd9c949767d3dc8e63e3f15ba12641f27d46b80
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569351"
---
# <a name="return-or-replace-your-azure-stack-edge-device"></a>傳回或取代您的 Azure Stack Edge 裝置

本文說明如何抹除資料，然後傳回您的 Azure Stack Edge 裝置。 傳回裝置之後，您也可以刪除與裝置相關聯的資源，或訂購更換裝置。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 將資料從裝置上的資料磁片抹除
> * 開啟支援票證以退回您的裝置
> * 封裝裝置並排程取貨
> * 刪除 Azure 入口網站中的資源
> * 取得更換裝置

## <a name="erase-data-from-the-device"></a>清除裝置中的資料

若要將資料從您裝置的資料磁片抹除，您必須重設裝置。 您可以使用本機 web UI 或 PowerShell 介面來重設您的裝置。

在重設之前，請先在裝置上建立本機資料的複本（如有需要）。 您可以將資料從裝置複製到 Azure 儲存體容器。

若要使用本機 web UI 重設您的裝置，請執行下列步驟。

1. 在本機 web UI 中，移至 [維護] [>] [**裝置重設**]。
2. 選取 [**重設裝置**]。

    ![重設裝置](media/azure-stack-edge-return-device/device-reset-1.png)

3. 當系統提示您確認時，請檢查警告，然後選取 **[是]** 繼續進行。

    ![確認重設](media/azure-stack-edge-return-device/device-reset-2.png)  

重設會從裝置資料磁片清除資料。 視裝置上的資料量而定，此程式大約需要30-40 分鐘的時間。

或者，連接到裝置的 PowerShell 介面，並使用`Reset-HcsAppliance` Cmdlet 來清除資料磁片中的資料。 如需詳細資訊，請參閱[重設您的裝置](azure-stack-edge-connect-powershell-interface.md#reset-your-device)。

> [!NOTE]
> - 如果您要交換或升級至新的裝置，建議您只在收到新裝置之後重設裝置。
> - 裝置重設只會刪除裝置上的所有本機資料。 雲端中的資料不會被刪除，而且會收集[費用](https://azure.microsoft.com/pricing/details/storage/)。 此資料需要使用雲端存放裝置管理工具（例如[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)）個別刪除。

## <a name="open-a-support-ticket"></a>開啟支援票證

若要開始傳回進程，請採取下列步驟。

1. 開啟支援票證，並 Microsoft 支援服務，表示您想要傳回裝置。 將 [問題類型] 選取為 [ **Azure Stack 邊緣硬體**]。

    ![開啟支援票證](media/azure-stack-edge-return-device/open-support-ticket-1.png)  

2. Microsoft 支援服務工程師會與您聯繫。 提供出貨詳細資料。
3. 如果您需要 [退貨運送] 方塊，您可以要求它。 回答 [**是]** ，問題**需要空的方塊才**會傳回。


## <a name="schedule-a-pickup"></a>排程取貨

1. 關閉裝置。 在本機 Web UI 中，移至 [維護] > [電源設定]****。
2. 選取 [**關機**]。 當系統提示您確認時，請按一下 **[是]** 繼續進行。 如需詳細資訊，請參閱[管理電源](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)。
3. 拔下電源線，並從裝置移除所有網路纜線。
4. 使用您自己的方塊或您從 Azure 收到的空白方塊來準備出貨套件。 將裝置和裝置隨附的電源線放在方塊中。
5. 將您從 Azure 收到的出貨標籤加到封裝上。
6. 與區域性貨運公司排定取貨時間。 如果傳回美國的裝置，則您的電訊廠商可能會是 UPS 或 FedEx。 若要使用 UPS 排程取貨：

    1. 致電給當地的 UPS (國家/地區特定的免付費電話號碼)。
    2. 在您的通話中，將反向出貨追蹤號碼加上引號，如下所示。
    3. 如果追蹤號碼未加上引號，則 UPS 會要求您在取貨期間支付額外的費用。

    您也可以在最接近的放置位置卸載 Azure Stack 邊緣，而不是排程取貨。

## <a name="delete-the-resource"></a>刪除資源

在 Azure 資料中心收到裝置之後，會檢查裝置是否有損毀或任何篡改跡象。

- 如果裝置原封不動地到達，而在良好的圖形中，則會停止該資源的計費計量。 Microsoft 支援服務會與您聯繫，以確認已傳回裝置。 然後，您可以在 Azure 入口網站中刪除與裝置相關聯的資源。
- 如果裝置嚴重損毀，則可能需要罰款。 如需詳細資訊，請參閱[裝置遺失或損毀的常見問題](https://azure.microsoft.com/pricing/details/databox/edge/)和[產品服務條款](https://www.microsoft.com/licensing/product-licensing/products)。  


您可以在 [Azure 入口網站中刪除裝置：
-    在您下訂單之後，以及在 Microsoft 準備好裝置之前。
-    將裝置傳回給 Microsoft 之後，它會在 Azure 資料中心傳遞實體檢查，並 Microsoft 支援服務呼叫來確認裝置是否已傳回。

如果您已針對另一個訂用帳戶或位置啟用裝置，Microsoft 會在一個工作天內將您的訂單移至新的訂用帳戶或位置。 移動訂單之後，您可以刪除此資源。


請採取下列步驟來刪除 Azure 入口網站中的裝置和資源。

1. 在 Azure 入口網站中，移至您的資源，然後前往 **[總覽**]。 從命令列中選取 [**刪除**]。

    ![選取 [刪除]](media/azure-stack-edge-return-device/delete-resource-1.png)

2. 在 [**刪除裝置**] 分頁中，輸入您想要刪除的裝置名稱，然後選取 [**刪除**]。

    ![Confirm delete](media/azure-stack-edge-return-device/delete-resource-2.png)

您會在裝置和相關聯的資源成功刪除後收到通知。

## <a name="get-a-replacement-device"></a>取得更換裝置

當現有裝置發生硬體故障或需要升級時，需要更換裝置。 當您的裝置發生硬體問題時，請採取下列步驟：

1. [開啟硬體問題的支援票證](#open-a-support-ticket)。 Microsoft 支援服務將判斷此實例無法使用欄位更換單位（FRU），或裝置需要硬體升級。 不論是哪一種情況，支援將會訂購更換裝置。
2. 為取代裝置[建立新的資源](azure-stack-edge-deploy-prep.md#create-a-new-resource)。 請務必選取 [**我有 Azure Stack Edge 裝置**] 的核取方塊。 
3. 在您收到更換裝置之後，請針對新的資源[安裝](azure-stack-edge-deploy-install.md)並[啟用](azure-stack-edge-deploy-connect-setup-activate.md)更換裝置。
4. 依照所有步驟來傳回原始裝置：
    1. 開啟另一個票證以傳回原始裝置。
    2. [清除裝置上的資料](#erase-data-from-the-device)。
    3. [排程取貨](#schedule-a-pickup)。
    5. 刪除與所傳回裝置相關聯[的資源](#delete-the-resource)。



## <a name="next-steps"></a>後續步驟

- 了解如何[管理頻寬](azure-stack-edge-manage-bandwidth-schedules.md)。
