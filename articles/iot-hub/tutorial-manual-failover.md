---
title: 手動容錯移轉 Azure IoT 中樞 | Microsoft Docs
description: 了解如何將 IoT 中樞手動容錯移轉至不同區域的，並確認它可正常運作，然後將其移轉回原始區域並再次加以檢查。
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: bf37f7b27e3f8923a229cc0617365d912d47aec2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "77110671"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>教學課程：執行手動容錯移轉 IoT 中樞

手動容錯移轉是 IoT 中樞服務的一項功能，可讓客戶將其中樞的作業從主要區域[容錯移轉](https://en.wikipedia.org/wiki/Failover)到對應的 Azure 異地配對區域。 發生區域性災難或延伸服務中斷的情況時，可以進行手動容錯移轉。 您也可以執行規劃的容錯移轉來測試您的災害復原功能，但建議使用測試 IoT 中樞，而不是生產環境中執行的 IoT 中樞。 對客戶提供手動容錯移轉功能無需額外費用。

在本教學課程中，您會執行下列工作：

> [!div class="checklist"]
> * 使用 Azure 入口網站來建立 IoT 中樞。 
> * 執行容錯移轉。 
> * 查看在次要位置中執行的中樞。
> * 執行容錯回復以將 IoT 中樞的作業傳回主要位置。 
> * 確認中樞在正確的位置中正確執行。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 請確定您的防火牆已開啟連接埠 8883。 本教學課程中的裝置範例會使用 MQTT 通訊協定，其會透過連接埠 8883 進行通訊。 某些公司和教育網路環境可能會封鎖此連接埠。 如需此問題的詳細資訊和解決方法，請參閱[連線至 IoT 中樞 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

1. 登入 [Azure 入口網站](https://portal.azure.com)。 

2. 按一下 [+ 建立資源]  ，然後依序選取 [物聯網]  、[IoT 中樞]  。

   ![顯示建立 IoT 中樞的螢幕擷取畫面](./media/tutorial-manual-failover/create-hub-01.png)

3. 選取 [基本]  索引標籤。填寫下列欄位。

    **訂用帳戶**：選取您需要使用的 Azure 訂用帳戶。

    **資源群組**：按一下 [建立新項目]  ，然後在資源群組指定 **ManlFailRG**。

    **區域**：選取您附近的區域。 本教學課程使用 `West US 2`。 只能在 Azure 異地配對區域之間進行容錯移轉。 與「美國西部 2」異地配對的區域是 WestCentralUS。
    
   **IoT 中樞名稱**：指定 IoT 中樞的名稱。 中樞名稱必須是全域唯一的。 

   ![顯示建立 IoT 中樞 [基本] 窗格的螢幕擷取畫面](./media/tutorial-manual-failover/create-hub-02-basics.png)

   按一下 [檢閱 + 建立]  。 (它會使用大小和級別的預設值。) 

4. 請檢閱資訊，然後按一下 [建立]  來建立 IoT 中樞。 

   ![顯示建立 IoT 中樞最後一個步驟的螢幕擷取畫面](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>執行手動容錯移轉

請注意，IoT 中樞每天有兩次容錯移轉和兩次容錯回復的限制。

1. 按一下 [資源群組]  ，然後選取資源群組 **ManlFailRG**。 在資源清單中，按一下您的中樞。 

1. 在 [IoT 中樞] 窗格的 [設定]  底下，按一下 [容錯移轉]  。

   ![顯示 [IoT 中樞屬性] 窗格的螢幕擷取畫面](./media/tutorial-manual-failover/trigger-failover-01.png)

1. 在 [手動容錯移轉] 窗格中，您會看到 [目前位置]  和 [容錯移轉位置]  。 目前位置一律會指出中樞目前作用所在的位置。 容錯移轉位置是標準 [Azure 異地配對區域](../best-practices-availability-paired-regions.md)，會與目前位置配對。 您無法變更位置的值。 在本教學課程中，目前位置是 `West US 2`，而容錯移轉位置是 `West Central US`。

   ![顯示 [手動容錯移轉] 窗格的螢幕擷取畫面](./media/tutorial-manual-failover/trigger-failover-02.png)

1. 在 [手動容錯移轉] 窗格頂端，按一下 [啟動容錯移轉]  。 

1. 在 [確認] 窗格中，填寫您 IoT 中樞的名稱，以確認它是您想要容錯移轉的 IoT 中樞。 然後，若要起始容錯移轉，請按一下 [容錯移轉]  。

   執行手動容錯移轉所花費的時間與您中樞註冊的裝置數目成正比。 例如，如果您有 100,000 部裝置，可能需要 15 分鐘，但如果您有 5,000,000 部裝置，則可能需要一小時或更久。

   ![顯示 [手動容錯移轉] 窗格的螢幕擷取畫面](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   執行手動容錯移轉程序時會出現橫幅，告知您正在進行手動容錯移轉。 

   ![顯示 [手動容錯移轉] 進行中的螢幕擷取畫面](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   如果您關閉 [IoT 中樞] 窗格，然後按一下 [資源群組] 窗格再次將它開啟，您會看到橫幅，告知您中樞正在進行手動容錯移轉。 

   ![顯示 IoT 中樞容錯移轉進行中的螢幕擷取畫面](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   完成之後，會翻轉 [手動容錯移轉] 頁面上的目前區域和容錯移轉區域，且中樞會回到使用中狀態。 在此範例中，目前位置現在是 `WestCentralUS`，而容錯移轉位置是 `West US 2`。 

   ![顯示容錯移轉已完成的螢幕擷取畫面](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   [概觀] 頁面也會顯示橫幅，指出容錯移轉已完成，且 IoT 中樞正在 `West Central US` 中執行。

   ![螢幕擷取畫面：顯示 [概觀] 頁面中容錯移轉已完成](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>執行容錯移轉 

在您執行手動容錯移轉之後，可以將中樞的作業切換回原始的主要區域 -- 這稱為容錯回復。 如果您剛執行完容錯移轉，必須等候大約一小時，才能要求容錯回復。 如果您嘗試在短時間內執行容錯回復，就會顯示一則錯誤訊息。

容錯回復的執行方式就如同手動容錯移轉一樣。 步驟如下： 

1. 若要執行容錯回復，請返回您 Iot 中樞的 [Iot 中樞] 窗格。

2. 在 [IoT 中樞] 窗格的 [設定]  底下，按一下 [容錯移轉]  。 

3. 在 [手動容錯移轉] 窗格頂端，按一下 [啟動容錯移轉]  。 

4. 在 [確認] 窗格中，填寫您 IoT 中樞的名稱，以確認它是您想要容錯回復的 IoT 中樞。 接著，若要起始容錯回復，請按一下 [確定]。 

   ![手動容錯回復要求的螢幕擷取畫面](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   橫幅的顯示方式會如同＜執行容錯移轉＞一節中所述。 容錯回復完成之後，它會如同最初的設定，再次顯示 `West US 2` 作為目前位置，以及顯示 `West Central US` 作為容錯移轉位置。

## <a name="clean-up-resources"></a>清除資源 

若要將您已針對本教學課程所建立的資源移除，請刪除資源群組。 此動作會同時刪除群組內含的所有資源。 在此情況下，它會移除 IoT 中樞和資源群組本身。 

1. 按一下 [資源群組]  。 

2. 請找出並選取資源群組 **ManlFailRG**。 按一下開啟它。 

3. 按一下 [刪除資源群組]  。 系統出現提示時，請輸入資源群組的名稱，然後按一下 [刪除]  以確認。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何設定及執行手動容錯移轉，以及如何執行下列工作來要求容錯回復：

> [!div class="checklist"]
> * 使用 Azure 入口網站來建立 IoT 中樞。 
> * 執行容錯移轉。 
> * 查看在次要位置中執行的中樞。
> * 執行容錯回復以將 IoT 中樞的作業傳回主要位置。 
> * 確認中樞在正確的位置中正確執行。

前進至下一個教學課程，以了解如何管理 IoT 裝置的狀態。 

> [!div class="nextstepaction"]
> [管理 IoT 裝置的狀態](tutorial-device-twins.md)
