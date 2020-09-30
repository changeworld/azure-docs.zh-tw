---
title: 教學課程 - 在入口網站中為雲端設定 Azure IoT 中樞裝置佈建服務
description: 本教學課程說明如何使用 IoT 中樞裝置佈建服務 (DPS)，在 [Azure 入口網站](https://portal.azure.com)中設定雲端資源以進行裝置佈建
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f45c3def84c548ba12221efa59e9ebbd4699df71
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316064"
---
# <a name="tutorial-configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>教學課程：設定雲端資源以使用 IoT 中樞裝置佈建服務來佈建裝置

本教學課程會示範如何使用 IoT 中樞裝置佈建服務來為雲端設定自動裝置佈建功能。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用 Azure 入口網站建立 IoT 中樞裝置佈建服務並取得識別碼範圍
> * 建立 IoT 中樞
> * 將 IoT 中樞連結至裝置佈建服務
> * 對裝置佈建服務設定配置原則

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>建立裝置佈建服務執行個體並取得識別碼範圍

請遵循下列步驟來建立新的裝置佈建服務執行個體。

1. 按一下 Azure 入口網站左上角的 [建立資源]  。

2. 在搜尋方塊中輸入**裝置佈建**。 

3. 按一下 [IoT 中樞裝置佈建服務]  。

4. 在 [IoT 中樞裝置佈建服務]  表單中填寫下列資訊：
    
   | 設定       | 建議的值 | 描述 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **名稱** | 任何唯一的名稱 | -- | 
   | **訂用帳戶** | 您的訂用帳戶  | 如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。 |
   | **資源群組** | myResourceGroup | 如需有效的資源群組名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。 |
   | **位置** | 任何有效位置 | 如需區域的相關資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。 |   

   ![在入口網站中輸入關於裝置佈建服務的基本資訊](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. 按一下頁面底部的 [新增]  。 幾分鐘後即會建立裝置佈建服務執行個體，並顯示 [概觀]  頁面。

6. 在新的服務執行個體的 [概觀]  頁面上，複製 [識別碼範圍]  的值以供後續使用。 該值可用來識別註冊識別碼，並確保註冊識別碼是唯一的。

7. 此外，請複製 [服務端點]  值以供後續使用。 

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>擷取 IoT 中樞的連接字串

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

您現在已經建立 IoT 中樞，因此您已具有完成本教學課程的其餘部分所需的主機名稱和 IoT 中樞連接字串。

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>將裝置佈建服務連結至 IoT 中樞

下一個步驟是連結裝置佈建服務與 IoT 中樞，讓 IoT 中樞裝置佈建服務可以向該中樞註冊裝置。 這項服務只能將裝置佈建到已連結至裝置佈建服務的 IoT 中樞。 請遵循下列步驟。

1. 在 [所有資源]  頁面上，按一下您先前建立的裝置佈建服務執行個體。

2. 在 [裝置佈建服務] 頁面中，按一下 [連結的 IoT 中樞]  。

3. 按一下 [新增]  。

4. 在 [新增 IoT 中樞的連結]  頁面中提供下列資訊，然後按一下 [儲存]  ：

    * **訂用帳戶︰** 確定已選取包含 IoT 中樞的訂用帳戶。 您可以連結至位於不同訂用帳戶的 IoT 中樞。

    * **IoT 中樞：** 選擇要與此裝置佈建服務執行個體連結的 IoT 中樞名稱。

    * **存取原則：** 選取 [iothubowner]  作為認證，以用來建立 IoT 中樞的連結。

   ![在入口網站中連結中樞名稱，以連結至裝置佈建服務](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>對裝置佈建服務設定配置原則

配置原則是一個 IoT 中樞裝置佈建服務設定，可判斷如何將裝置指派給 IoT 中樞。 共有三個支援的配置原則： 

1. **最低延遲**：根據對裝置具有最低延遲的 IoT 中樞，將裝置佈建到該中樞。

2. **權重相等的分佈** (預設)：裝置佈建到每個已連結 IoT 中樞的機率都一樣。 這項設定是預設值。 如果您只要將裝置佈建到一個 IoT 中樞，可以保留此設定。 

3. **透過註冊清單進行靜態設定**：註冊清單中所需 IoT 中樞的規格，其優先順序高於裝置佈建服務層級的配置原則。

若要設定配置原則，請在 [裝置佈建服務] 頁面中按一下 [管理配置原則]  。 請確實地將配置原則設為 [平均加權分佈]  \(預設值)。 如果有進行任何變更，請於變更完成後按一下 [儲存]  。

![管理配置原則](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>清除資源

此集合中的其他教學課程會以本教學課程為基礎。 如果您打算繼續進行後續的快速入門或教學課程，請勿清除在此教學課程中建立的資源。 如果您不打算繼續，請使用下列步驟，在 Azure 入口網站中刪除本教學課程所建立的所有資源。

1. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]  ，然後選取 IoT 中樞裝置佈建服務執行個體。 在 [所有資源]  頁面的頂端，按一下 [刪除]  。  

2. 從 Azure 入口網站的左側功能表中，按一下 [所有資源]  ，然後選取您的 IoT 中樞。 在 [所有資源]  頁面的頂端，按一下 [刪除]  。
 
## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 使用 Azure 入口網站建立 IoT 中樞裝置佈建服務並取得識別碼範圍
> * 建立 IoT 中樞
> * 將 IoT 中樞連結至裝置佈建服務
> * 對裝置佈建服務設定配置原則

前進至下一個教學課程，以了解如何設定裝置的佈建功能。

> [!div class="nextstepaction"]
> [設定裝置的佈建功能](tutorial-set-up-device.md)
