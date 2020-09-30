---
title: 教學課程：從 Bing 地圖服務遷移至 Azure 地圖服務 | Microsoft Azure 地圖服務
description: 從 Bing 地圖服務遷移至 Microsoft Azure 地圖服務的教學課程。 此指引會引導您切換至 Azure 地圖服務 API 和 SDK。
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 643e49bdba76051c873ed549d5f6c21487f34056
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90107990"
---
# <a name="migrate-from-bing-maps-to-azure-maps"></a>從 Bing 地圖服務遷移至 Azure 地圖服務

本指南將深入解析如何將 Web、行動裝置和伺服器應用程式從 Bing 地圖服務遷移至 Azure 地圖服務平台。 本指南包含比較程式碼範例、移轉建議，以及遷移至 Azure 地圖服務的最佳做法。

## <a name="azure-maps-platform-overview"></a>Azure 地圖服務平台概觀

Azure 地圖服務為來自各個產業的開發人員提供功能強大的地理空間功能，另納入可用的最新地圖資料，可提供網路和行動應用程式的地理空間內容。 Azure 地圖服務是一套符合 Azure One API 標準的 REST API，適用於地圖、搜尋、路線規劃、車流、時區、地理柵欄、地圖資料、氣候資料和許多其他服務，並同時隨附 Web 和 Android SDK，使開發作業更簡單、富有彈性且可攜至多個平台。 [Power BI 也提供 Azure 地圖服務](power-bi-visual-getting-started.md)。

## <a name="high-level-platform-comparison"></a>高階平台比較

下表提供 Bing 地圖服務功能的概略清單，以及這些功能在 Azure 地圖服務中的相對支援。 此清單不包含額外的 Azure 地圖服務功能，例如協助工具、地理柵欄 API、流量服務、空間作業、直接地圖底圖存取和批次服務。

| Bing 地圖服務功能                     | Azure 地圖服務支援 |
|---------------------------------------|:------------------:|
| Web SDK                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | 已規劃            |
| UWP SDK                               | 已規劃            |
| WPF SDK                               | 已規劃            |
| REST 服務 API                     | ✓                  |
| 自動建議                           | ✓                  |
| 指示 (包括卡車)          | ✓                  |
| 距離矩陣                       | ✓                  |
| 提高權限                            | 已規劃            |
| 影像 – 靜態地圖                  | ✓                  |
| 影像中繼資料                      | ✓                  |
| 等時線                            | ✓                  |
| 地點分析                        | ✓                  |
| 本機搜尋                          | ✓                  |
| 位置辨識                  | ✓                  |
| 位置 (轉寄/反向地理編碼) | ✓                  |
| 已最佳化路線            | 已規劃            |
| 貼齊道路                         | ✓                  |
| 空間資料服務 (SDS)           | Partial            |
| 時區                             | ✓                  |
| 交通事故                     | ✓                  |
| 設定導向地圖             | N/A                |

Bing 地圖服務提供基本的金鑰型驗證。 Azure 地圖服務同時提供基本的金鑰型驗證以及高安全的 Azure Active Directory 驗證。

## <a name="licensing-considerations"></a>授權考量

從 Bing 地圖服務遷移至 Azure 地圖服務時，應考量下列有關於授權的事項。

-   Azure 地圖服務會根據載入的地圖底圖數目收取使用互動式地圖的費用，而 Bing 地圖服務則會收取載入地圖控制項 (工作階段) 的費用。 在 Azure 地圖服務中會自動快取地圖底圖，以降低開發人員的成本。 每載入 15 個地圖底圖，就會產生一筆 Azure 地圖服務交易。 互動式 Azure 地圖服務 SDK 使用 512 像素的底圖，平均每個頁面檢視會產生一筆或更少的交易。

-   Azure 地圖服務允許將其平台中的資料儲存在 Azure 中。 您也可以根據[使用規定](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)，在別處快取最久達六個月前的資料。

以下是 Azure 地圖服務的一些授權相關資源：

-   [Azure 地圖服務定價頁面](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure 地圖服務使用規定](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (包含在 Microsoft Online Services 條款中)
-   [在 Azure 地圖服務中選擇正確的定價層](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>建議的移轉計劃

高階移轉計劃如下。

1.  清查您的應用程式所使用的 Bing 地圖服務 SDK 和服務，並確認 Azure 地圖服務提供替代的 SDK 和服務可讓您遷移。
2.  經由 <https://azure.com> 建立 Azure 訂用帳戶 (如果您還沒有的話)。
3.  建立 Azure 地圖服務帳戶 ([文件](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) 和驗證金鑰或 Azure Active Directory ([文件](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication))。
4.  遷移應用程式的程式碼。
5.  測試已遷移的應用程式。
6.  將已遷移的應用程式部署至生產環境。

## <a name="azure-maps-technical-resources"></a>Azure 地圖服務技術資源

以下列出 Azure 地圖服務的實用技術資源。

-   概觀： https://azure.com/maps
-   文件：<https://aka.ms/AzureMapsDocs>
-   Web SDK 程式碼範例：<https://aka.ms/AzureMapsSamples>
-   開發人員論壇：<https://aka.ms/AzureMapsForums>
-   影片：<https://aka.ms/AzureMapsVideos>
-   部落格：<https://aka.ms/AzureMapsBlog>
-   Azure 地圖服務意見反應 (UserVoice)：<https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>移轉支援

開發人員可透過[論壇](https://aka.ms/AzureMapsForums)或透過眾多 Azure 支援選項之一尋求移轉支援：<https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>新術語 

以下是常見的 Bing 地圖服務字詞清單，而這些字詞在 Azure 地圖服務中所知為不同字詞。

| Bing 地圖服務字詞                    | Azure 地圖服務字詞                                                |
|-----------------------------------|----------------------------------------------------------------|
| [空照圖]                            | 衛星或空照圖                                            |
| 方向                        | 可能也稱為路線規劃                             |
| 實體                          | 幾何或功能                                         |
| `EntityCollection`                | 資料來源或圖層                                           |
| `Geopoint`                        | 位置                                                       |
| `GeoXML`                          | 空間 IO 模組中的 XML 檔案                             |
| 地面重疊                    | 影像圖層                                                    |
| 混合式 (地圖類型的參考) | 包含道路的衛星                                           |
| Infobox                           | 快顯                                                          |
| Location                          | 位置                                                       |
| `LocationRect`                    | 週框方塊                                                   |
| 對應類型                          | 地圖樣式                                                      |
| 導覽列                    | 地圖樣式選擇器、縮放控制、音調控制、羅盤控制 |
| 圖釘                           | 氣泡圖層、符號圖層或 HTML 標記                      |

## <a name="next-steps"></a>下一步

參考下列文章以詳細了解如何遷移您的 Bing 地圖服務應用程式：

> [!div class="nextstepaction"]
> [遷移 Web 應用程式](migrate-from-bing-maps-web-app.md)

> [!div class="nextstepaction"]
> [遷移 Web 服務](migrate-from-bing-maps-web-services.md)
