---
title: 'Microsoft Azure (常見問題將天氣服務的常見問題對應) '
description: 尋找有關 Azure 地圖服務天氣服務資料和功能的常見問題的解答。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/04/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 2a5a58c1515c647bb76bf35f3a5eaade3d00588a
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96747319"
---
# <a name="azure-maps-weather-services-frequently-asked-questions-faq"></a>Azure 地圖服務天氣服務的常見問題 (常見問題) 

本文將回答有關 [Azure 地圖服務天氣服務](https://docs.microsoft.com/rest/api/maps/weather) 資料和功能的常見問題。 其中包含下列主題：

* 資料來源和資料模型
* 氣象服務涵蓋範圍和可用性
* 資料更新頻率
* 使用 Azure 地圖服務 Sdk 進行開發
* 將天氣資料視覺化的選項，包括 Microsoft Power BI 整合

## <a name="data-sources-and-data-models"></a>資料來源和資料模型

**Azure 地圖服務源氣象資料如何？**

Azure 地圖服務是與世界級的行動和定位技術合作夥伴（包括 AccuWeather）共同作業，這些合作夥伴提供基礎氣象資料。 若要閱讀 Azure 地圖服務與 AccuWeather 共同作業的公告，請參閱 [雨或洞悉： Azure 地圖服務氣象服務會將見解帶到您的企業](https://azure.microsoft.com/blog/rain-or-shine-azure-maps-weather-services-will-bring-insights-to-your-enterprise/)。

AccuWeather 具有世界各地的即時天氣和環境資訊，主要是因為它們與許多國家政府的氣象機關和其他專屬安排的合作關係。 以下提供此基礎資訊的清單。

* 政府機關公開可用的全球表面觀察
* 政府和私用公司的專屬介面觀察資料集
* 超過40個國家/地區的高解析度雷達圖資料
* 最優秀的即時全球閃電資料
* 超過60個國家/地區和區域的政府發出天氣警告
* Geostationary 氣象附屬資料，涵蓋整個世界
* 超過150的數值預測模型（包括內部專屬的模型化）、政府模型（例如美國全球預測系統） (GFS) ，以及私用公司提供的唯一 downscaled 模型
* 空氣品質觀察
* 運輸部門的觀察

結合了數萬個表面觀察及其他資料，以建立並影響使用者目前提供的條件。 這不僅包括可自由使用的標準資料集，還包括在許多國家/地區（包括印度、巴西、加拿大和其他專屬輸入）中，從全國氣象服務取得的獨特觀察。 這些唯一的資料集會增加使用者的目前條件資料的空間和暫時解析度。 

這些資料集會即時進行檢查，以取得數位預測系統的精確度，以利用 AccuWeather 專屬的人工智慧演算法來持續修改預測，以確保它們一律納入最新的資料，進而將其持續的精確度發揮到極致。

**哪些模型會建立天氣預測資料？**

使用許多氣象預測指引系統來制定全球預測。 每一天都會使用超過150的數值預測模型，這兩種模型都是外部和內部資料集。 這包括政府模型，例如歐洲中心 ECMWF 和美國全球預測系統 (GFS) 。 此外，AccuWeather 也併入專屬的高解析度模型，可將預測縮小至特定位置和策略性地區網域，以更精確地預測天氣。 過去數十年來，AccuWeather 的獨特混合和加權演算法已經過開發。 這些演算法會以最佳方式運用大量的預測輸入，以提供高度精確的預測。

## <a name="weather-services-coverage-and-availability"></a>氣象服務涵蓋範圍和可用性

**針對不同的國家/地區，我可以預期何種涵蓋範圍？**

氣象服務涵蓋範圍會因國家/地區而異。 所有的功能在每個國家/地區都無法使用。 如需詳細資訊，請參閱 [涵蓋範圍檔](https://docs.microsoft.com/azure/azure-maps/weather-coverage)集。

## <a name="data-update-frequency"></a>資料更新頻率

**目前的條件資料更新的頻率為何？**

目前的條件資料大約每小時更新一次，但可透過快速變更的情況（例如大型溫度變更、天空條件變更、降雨變更等）更頻繁地更新。 世界各地大部分的觀察站回報條件變更時，每小時會多次報告。 不過，有幾個區域仍然只會在排程的間隔內更新一次、兩次或四次。  

Azure 地圖服務快取目前的條件資料最多10分鐘，以協助在資料發生時取得接近即時的更新頻率。 若要查看快取的回應何時過期，並避免顯示過期的資料，您可以在 Azure 地圖服務 API 回應的 HTTP 標頭中，利用 Expires 標頭資訊。

**每日和每小時預測資料更新的頻率為何？**

隨著收到更新的觀察，每日和每小時的預測資料會每天更新一次。  例如，如果超過預測的高/低溫度，我們的預測資料會在下一個更新週期進行調整。 這可能會發生在不同的時間間隔，但通常會在一小時內發生。 許多突然天氣狀況可能會導致預測資料變更。 例如，在經常性的夏天下午，隔離的 thunderstorm 可能突然出現，帶來大量的雲端涵蓋範圍和 rain。 獨立的風暴可以有效地捨棄最多10度的溫度。 這個新的溫度值會影響當天剩餘時間的每小時和每日預測，因此將會在我們的資料集中更新。

Azure 地圖服務的預測 Api 會快取最多30分鐘。 若要查看快取的回應何時過期，並避免顯示過期的資料，您可以在 Azure 地圖服務 API 回應的 HTTP 標頭中，利用 Expires 標頭資訊。 建議您根據特定產品使用案例和 UI (使用者) 介面，視需要進行更新。

## <a name="developing-with-azure-maps-sdks"></a>使用 Azure 地圖服務 Sdk 進行開發

**Azure 地圖服務 Web SDK 原本就支援氣象服務整合嗎？**

Azure 地圖服務 Web SDK 提供服務模組。 [服務] 模組是協助程式程式庫，可讓您輕鬆地在 web 或 Node.js 應用程式中使用 Azure 地圖服務 REST 服務。 使用 JavaScript 或 TypeScript。 若要開始使用，請參閱我們的 [檔](https://docs.microsoft.com/azure/azure-maps/how-to-use-services-module)。

**Azure 地圖服務 Android SDK 原本就支援氣象服務整合嗎？**

Azure 地圖服務 Android Sdk 支援 Mercator 圖格圖層，其可以有 x/y/縮放標記法、四個按鍵標記法，或 EPSG 3857 周框方塊標記法。

我們計畫建立適用于 JAVA/Android 的服務模組，類似于 web SDK 模組。 Android 服務模組可讓您輕鬆地存取 JAVA 或 Android 應用程式中的所有 Azure 地圖服務服務。  

## <a name="data-visualizations"></a>資料視覺效果  

**Azure 地圖服務 Power BI 的視覺支援 Azure 地圖服務氣象圖格嗎？**

是。 若要瞭解如何將雷達圖和紅外線附屬磚遷移至 Microsoft Power BI 視覺效果，請參閱 [將圖格圖層新增至 Power BI 視覺效果](https://docs.microsoft.com/azure/azure-maps/power-bi-visual-add-tile-layer)。 

**如何? 解讀用於雷達圖和附屬磚的色彩？**

Azure 地圖服務 [天氣概念文章](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts#radar-and-satellite-imagery-color-scale) 包含可協助您解讀用於雷達圖和附屬磚之色彩的指南。 本文涵蓋色彩範例和十六進位色彩代碼。
 
**我可以建立雷達圖和附屬磚動畫嗎？**

是。 除了即時雷達圖和附屬磚之外，Azure 地圖服務客戶還可以要求過去和未來的磚，利用地圖覆迭來增強資料視覺效果。 這可以藉由直接呼叫 [取得地圖底圖 V2 API](https://aka.ms/AzureMapsWeatherTiles ) ，或透過 AZURE 地圖服務 web SDK 來要求磚來完成。 最多可提供過去1.5 小時的雷達圖磚，未來最多可達2小時。 磚和可用時間間隔為5分鐘。 在過去最多可提供三個小時的紅外線磚，並以10分鐘的間隔提供。 如需詳細資訊，請參閱開放原始碼氣象圖磚動畫程式 [代碼範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Animated%20tile%20layer)。  

**您是否提供不同天氣狀況的圖示？**

是。 您可以在 [這裡](https://docs.microsoft.com/azure/azure-maps/weather-services-concepts#weather-icons)找到圖示及其各自的代碼。 請注意，只有部分氣象服務 Api （例如「  [取得目前的條件」 api](https://aka.ms/azuremapsweathercurrentconditions)）會在回應中傳回 *iconCode* 。 如需詳細資訊，請參閱目前的 WeatherConditions 開放原始 [碼程式碼範例](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Get%20current%20weather%20at%20a%20location)。

## <a name="next-steps"></a>後續步驟

如果此常見問題未回答您的問題，您可以透過下列管道來聯繫我們， (按下訂單) ：

* 本文的留言區。
* [MSFT Q&頁面以進行 Azure 地圖服務](https://docs.microsoft.com/answers/topics/azure-maps.html)。
* Microsoft 支援服務。 若要建立新的支援要求，請在 [Azure 入口網站](https://portal.azure.com/)的 [說明] 索引標籤上，選取 [說明 **+** 支援] 按鈕，然後選取 [ **新增支援要求**]。
* [Azure 地圖服務 UserVoice](https://feedback.azure.com/forums/909172-azure-maps) 提交功能要求。

瞭解如何使用 Azure 地圖服務天氣服務來要求即時和預測的氣象資料：
> [!div class="nextstepaction"]
> [要求即時天氣資料 ](how-to-request-weather-data.md)

Azure 地圖服務天氣服務概念文章：
> [!div class="nextstepaction"]
> [天氣服務概念](weather-coverage.md)

探索 Azure 地圖服務的氣象服務 API 檔：

> [!div class="nextstepaction"]
> [Azure 地圖服務天氣服務](/rest/api/maps/weather)
