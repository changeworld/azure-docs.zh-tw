---
title: 使用 Azure 地圖服務繪製錯誤視覺化檢視
description: 在本文中，您將了解如何將建立工具轉換 API 所傳回的警告和錯誤視覺化。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7e43995c4221127580333c3f013fcb209730f863
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791109"
---
# <a name="using-the-azure-maps-drawing-error-visualizer"></a>使用 Azure 地圖服務繪製錯誤視覺化檢視

「繪製錯誤視覺化檢視」是一個獨立的 Web 應用程式，會顯示在轉換程序期間偵測到[繪圖套件警告和錯誤](drawing-conversion-error-codes.md)。 錯誤視覺化檢視 Web 應用程式由靜態頁面所組成，可以在不連接網際網路的情況下使用。  您可以使用錯誤視覺化檢視，根據[繪圖套件需求](drawing-requirements.md)來修正錯誤和警告。 只有在偵測到錯誤時，[Azure 地圖服務轉換 API](https://docs.microsoft.com/rest/api/maps/conversion) 才會傳回含有錯誤視覺化連結的回應。

## <a name="prerequisites"></a>Prerequisites

您必須先執行下列動作才能下載繪製錯誤視覺化檢視：

1. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。
3. [建立一個建立工具資源](how-to-manage-creator.md)

本教學課程使用 [Postman](https://www.postman.com/) 應用程式，但您可以選擇不同的 API 開發環境。

## <a name="download"></a>下載

1. 將您的繪圖套件上傳至 Azure 地圖建立工具服務，以取得所上傳套件的 `udid`。 如需如何上傳套件的相關步驟，請參閱[上傳繪圖套件](tutorial-creator-indoor-maps.md#upload-a-drawing-package)。

2. 在繪圖套件上傳後，我們將對上傳的套件使用 `udid`，以將套件轉換成地圖資料。 如需如何轉換套件的相關步驟，請參閱[轉換繪圖套件](tutorial-creator-indoor-maps.md#convert-a-drawing-package)。

    >[!NOTE]
    >轉換程序成功時，您將不會收到錯誤視覺化檢視的連結。

3. 在 Postman 應用程式的回應 [標頭] 索引標籤底下，尋找轉換 API 所傳回的 `diagnosticPackageLocation` 屬性。 回應看起來應該會類似於下列 JSON：

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. 對 `diagnosticPackageLocation` URL 提出 `HTTP-GET` 要求，以下載繪圖套件錯誤視覺化檢視。

## <a name="setup"></a>安裝程式

在從 `diagnosticPackageLocation` 連結下載的壓縮套件中，您會發現兩個檔案。

* _VisualizationTool.zip_：包含繪製錯誤視覺化檢視的原始程式碼、媒體和網頁。
* _ConversionWarningsAndErrors.json_：包含警告、錯誤和繪製錯誤視覺化檢視所使用之其他詳細資料的格式化清單。

解壓縮 _VisualizationTool.zip_ 資料夾。 其中包含下列項目：

* _assets_ 資料夾：包含影像和媒體檔案
* _static_ 資料夾：原始程式碼
* _index.html_ 檔案：Web 應用程式。

使用下列任何一種瀏覽器 (具有各自的版本號碼)，開啟 _index.html_ 檔案。 若有不同的版本提供與所列版本相同的行為，您可以使用該版本。

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>使用繪製錯誤視覺化檢視工具

啟動繪製錯誤視覺化檢視工具後，您將會看到 [上傳] 頁面。 [上傳] 頁面包含一個拖放方塊。 拖放方塊也可作為啟動 [檔案總管] 對話方塊的按鈕。

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="繪製錯誤視覺化檢視應用程式 - 起始頁面":::

_ConversionWarningsAndErrors.json_ 檔案已放在下載目錄的根目錄中。 若要載入 _ConversionWarningsAndErrors.json_，您可以將檔案放拖到方塊上，或按一下方塊，在 [檔案總管] 對話方塊中尋找檔案，然後上傳檔案。

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="繪製錯誤視覺化檢視應用程式 - 藉由拖放載入資料":::

_ConversionWarningsAndErrors.json_ 檔案載入後，您會看到繪圖套件錯誤和警告清單。 每個錯誤或警告皆由圖層、層級和詳細訊息所指定。 若要查看有關錯誤或警告的詳細資訊，請按一下 [**詳細資料**] 連結。 棘手區段就會出現在清單下方。 此時您可以瀏覽至每個錯誤，以深入了解如何解決錯誤。

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="繪製錯誤視覺化檢視應用程式 - 錯誤和警告":::

## <a name="next-steps"></a>後續步驟

一旦您的[繪圖套件符合需求](drawing-requirements.md)，您就可以使用 [Azure 地圖資料集服務](https://docs.microsoft.com/rest/api/maps/conversion)，將繪圖套件轉換成資料集。 然後，您可以使用室內地圖 Web 模組來開發您的應用程式。 請閱讀下列文章以深入了解：

> [!div class="nextstepaction"]
> [繪製轉換錯誤碼](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [室內地圖的建立工具](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [使用室內地圖模組](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [實作室內地圖動態樣式](indoor-map-dynamic-styling.md)