---
title: 快速入門：使用 Azure 地圖服務的互動式地圖搜尋 | Microsoft Azure 地圖服務
description: 了解如何使用 Microsoft Azure Maps Web SDK 建立互動式地圖搜尋的示範 Web 應用程式。
author: farah-alyasari
ms.author: v-faalya
ms.date: 1/14/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 74539a7ed74d0216f1a4bb2d719983895d8b320f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359565"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>快速入門：使用 Azure 地圖服務建立互動式地圖搜尋

本文示範 Azure 地圖服務建立地圖的功能，為使用者建立提供互動式搜尋體驗的地圖。 文中會逐步引導您完成下列基本步驟：
* 建立您自己的 Azure 地圖服務帳戶。
* 取得帳戶金鑰以便在 Web 應用程式示範中使用。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>使用 Azure 地圖服務建立帳戶

使用下列步驟建立新的地圖服務帳戶：

1. 按一下 [Azure 入口網站](https://portal.azure.com)左上角的 [建立資源]  。
2. 在 [搜尋 Marketplace]  方塊中，輸入 **Maps**。
3. 從 [結果]  中，選取 [地圖服務]  。 按一下地圖下方顯示的 [建立]  按鈕。
4. 在 [建立地圖服務帳戶]  頁面上輸入下列值：
    * 您想要使用於此帳戶的 [訂用帳戶]  。
    * 此帳戶的 [資源群組]  名稱。 您可以選擇 [建立新的]  或 [使用現有的]  資源群組。
    * 新帳戶的 [名稱]  。
    * 此帳戶的 [定價層]  。
    * 閱讀 [授權]  和 [隱私權聲明]  ，然後選取核取方塊以接受條款。
    * 按一下 [ **建立** ] 按鈕。

![在入口網站中建立地圖服務帳戶](./media/quick-demo-map-app/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>取得帳戶的主要金鑰

成功建立地圖服務帳戶後，擷取金鑰以便能查詢地圖服務 API。 我們建議在呼叫 Azure 地圖服務時，使用您帳戶的主要金鑰作為訂用帳戶金鑰。

1. 在入口網站中開啟地圖服務帳戶。
2. 在 [設定] 區段上，選取 [驗證]  。
3. 將 [主索引鍵]  複製到剪貼簿。 將其儲存在本機，以供本教學課程稍後使用。

![在 Azure 入口網站中取得主要金鑰 Azure 地圖服務金鑰](./media/quick-demo-map-app/get-key.png)

## <a name="download-the-application"></a>下載應用程式

1. 移至 [Interactivesearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) 並按一下該檔案，以在 GitHub 使用者介面內檢視其內容。 以滑鼠右鍵按一下 [原始]  按鈕並複製檔案的內容，或按一下 [另存新檔] 以下載檔案。
2. 將此檔案的內容在本機另存為 **AzureMapDemo.html**。 在文字編輯器中將其開啟。
3. 搜尋字串 `<Your Azure Maps Key>`。 以上一節的 **主要金鑰** 值加以取代。

## <a name="open-the-application"></a>開啟應用程式

1. 在您選擇的瀏覽器中開啟 **AzureMapDemo.html** 檔案。
2. 查看地圖所顯示的洛杉磯市。 縮放地圖大小，以查看地圖依據縮放層級搭配較多或較少資訊縮放時，自動轉譯的情況。 
3. 變更地圖的預設中心。 在 **AzureMapDemo.html** 檔案中，搜尋名為 **center** 的變數。 以新的值 **[-74.0060, 40.7128]** 取代此變數的 [經度, 緯度] 值組。 儲存檔案並重新整理瀏覽器。
4. 試用互動式搜尋體驗。 在示範 Web 應用程式左上角的搜尋方塊中，搜尋**餐廳**。
5. 將滑鼠移到搜尋方塊下所出現的地址和位置清單。 注意地圖上對應的圖釘如何彈出該位置的相關資訊。 為了保護私人公司的隱私權，在此顯示的是虛構的名稱和地址。

    ![互動式地圖搜尋 Web 應用程式](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>清除資源

教學課程詳述了如何利用您的帳戶來使用及設定 Azure 地圖服務。 如果您打算繼續進行教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，則請採取下列步驟來清除資源：

1. 關閉執行 **AzureMapDemo.html** Web 應用程式的瀏覽器。
2. 從 Azure 入口網站的左側功能表中，選取 [所有資源]  。 然後選取您的 Azure 地圖服務帳戶。 在 [所有資源]  刀鋒視窗的頂端，選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立 Azure 地圖服務帳戶，並建立了示範應用程式。 查看下列教學課程，以了解 Azure 地圖服務：

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務來搜尋附近景點](tutorial-search-location.md)

如需更多程式碼範例和互動式編碼體驗，請參閱以下指南：

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務搜尋服務來尋找地址](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [使用 Azure 地圖服務的地圖控制項](how-to-use-map-control.md)
