---
title: 快速入門：使用 Azure 地圖服務的互動式地圖搜尋
description: 了解如何建立互動式、可搜尋的地圖。 了解如何建立 Azure 地圖服務帳戶、取得主要金鑰，以及使用 Web SDK 來設定地圖應用程式。
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: fa5cab35416f2629d39ec04803f90fae1a2e5f6d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031041"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>快速入門：使用 Azure 地圖服務建立互動式地圖搜尋

本文說明如何使用 Azure 地圖服務建立地圖的功能，為使用者建立提供互動式搜尋體驗的地圖。 文中會逐步引導您完成下列基本步驟：

* 建立您自己的 Azure 地圖服務帳戶。
* 取得主要金鑰以便在 Web 應用程式示範中使用。
* 下載並開啟示範地圖應用程式。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 登入 [Azure 入口網站](https://portal.azure.com)。

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>建立 Azure 地圖服務帳戶

使用下列步驟建立新的 Azure 地圖服務帳戶：

1. 按一下 [Azure 入口網站](https://portal.azure.com)左上角的 [建立資源]。
2. 在 [搜尋 Marketplace] 方塊中，輸入 **Azure 地圖服務**。
3. 從 [結果] 中，選取 [Azure 地圖服務]。 按一下地圖下方顯示的 [建立] 按鈕。
4. 在 [建立地圖服務帳戶] 頁面上輸入下列值：
    * 您想要使用於此帳戶的 [訂用帳戶]。
    * 此帳戶的 [資源群組] 名稱。 您可以選擇 [建立新的] 或 [使用現有的] 資源群組。
    * 新帳戶的 [名稱]。
    * 此帳戶的 [定價層]。
    * 閱讀 [授權] 和 [隱私權聲明]，然後選取核取方塊以接受條款。
    * 按一下 [ **建立** ] 按鈕。

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="在入口網站中建立地圖服務帳戶":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>取得帳戶的主要金鑰

成功建立地圖服務帳戶後，擷取主要金鑰以便能查詢地圖服務 API。

1. 在入口網站中開啟地圖服務帳戶。
2. 在 [設定] 區段上，選取 [驗證]。
3. 將 [主索引鍵] 複製到剪貼簿。 將其儲存在本機，以供本教學課程稍後使用。

>[!NOTE]
> 如果您使用訂用帳戶金鑰而非主要金鑰，您的地圖將無法正確轉譯。 此外，為了安全起見，建議您在主要和次要金鑰之間輪替。 若要輪替金鑰，將您的應用程式更新為使用次要金鑰，部署，然後按下主要金鑰旁的循環/重新整理按鈕，以產生新的主要金鑰。 舊的主要金鑰將會停用。 如需金鑰輪替的詳細資訊，請參閱[使用金鑰輪替和稽核來設定 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/key-rotation-log-monitoring)

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="在 Azure 入口網站中取得主要金鑰 Azure 地圖服務金鑰":::

## <a name="download-the-demo-application"></a>下載示範應用程式

1. 移至 [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html)。 複製檔案的內容。
2. 將此檔案的內容在本機另存為 **AzureMapDemo.html**。 在文字編輯器中將其開啟。
3. 搜尋字串 `<Your Azure Maps Key>`。 以上一節的 **主要金鑰** 值加以取代。

## <a name="open-the-demo-application"></a>開啟示範應用程式

1. 在您選擇的瀏覽器中開啟 **AzureMapDemo.html** 檔案。
2. 查看地圖所顯示的洛杉磯市。 縮放地圖大小，以查看地圖依據縮放層級搭配較多或較少資訊縮放時，自動轉譯的情況。
3. 變更地圖的預設中心。 在 **AzureMapDemo.html** 檔案中，搜尋名為 **center** 的變數。 以新的值 **[-74.0060, 40.7128]** 取代此變數的 [經度, 緯度] 值組。 儲存檔案並重新整理瀏覽器。
4. 試用互動式搜尋體驗。 在示範 Web 應用程式左上角的搜尋方塊中，搜尋**餐廳**。
5. 將滑鼠移到搜尋方塊下所出現的地址和位置清單。 注意地圖上對應的圖釘如何彈出該位置的相關資訊。 為了保護私人公司的隱私權，在此顯示的是虛構的名稱和地址。

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="互動式地圖搜尋 Web 應用程式":::


## <a name="clean-up-resources"></a>清除資源

>[!WARNING]
>[後續步驟](#next-steps)一節中所列出的教學課程詳述了如何利用您的帳戶來使用及設定 Azure 地圖服務。 如果您打算繼續進行教學課程，請勿清除在此快速入門中建立的資源。

如果您不打算繼續教學課程，則請採取下列步驟來清除資源：

1. 關閉執行 **AzureMapDemo.html** Web 應用程式的瀏覽器。
2. 瀏覽至 Azure 入口網站頁面。 從主要入口網站頁面中選取 [所有資源]。 或者，按一下左上角的功能表圖示。 選取 [所有資源]。
3. 按一下您的 Azure 地圖服務帳戶。 按一下頁面頂端的 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 Azure 地圖服務帳戶，並建立了示範應用程式。 查看下列教學課程，以深入了解 Azure 地圖服務：

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務來搜尋附近景點](tutorial-search-location.md)

如需更多程式碼範例和互動式編碼體驗，請參閱以下指南：

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務的搜尋服務來尋找地址](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務的地圖控制項](how-to-use-map-control.md)
