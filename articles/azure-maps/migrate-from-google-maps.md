---
title: 教學課程：從 Google 地圖遷移至 Azure 地圖服務 | Microsoft Azure 地圖服務
description: 從 Google 地圖遷移至 Microsoft Azure 地圖服務的教學課程。 此指引會引導您切換至 Azure 地圖服務 API 和 SDK。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 1f6f282406c6813b2b126c300f21bda21e8f9464
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988969"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>從 Google 地圖遷移至 Azure 地圖服務

本教學課程將深入解析如何將 Web、行動裝置和伺服器應用程式從 Google 地圖遷移至 Microsoft Azure 地圖服務平台。 本教學課程包含比較程式碼範例、移轉建議，以及遷移至 Azure 地圖服務的最佳做法。

## <a name="azure-maps-platform-overview"></a>Azure 地圖服務平台概觀

Azure 地圖服務可為各個產業的開發人員提供功能強大的地理空間功能，同時也納入定期更新的地圖資料，以提供 Web 和行動應用程式的地理位置內容。 Azure 地圖服務有 REST API 的 Azure One API 相容集合。 這些 REST API 會提供地圖、搜尋、路由、流量、時區、地理位置、地理柵欄、地圖資料、天氣、行動性和空間作業。 這些作業都會隨附在網頁和 Android SDK 中，如此可讓開發工作變得更簡單、更具彈性，且可跨多個平台使用。

## <a name="high-level-platform-comparison"></a>高階平台比較

下表提供 Azure 地圖服務功能的概略清單，這些功能可與 Google 地圖功能相對應。 這份清單不會顯示所有 Azure 地圖服務功能。 額外的 Azure 地圖服務包含協助工具、地理柵欄 API、等時路線規劃、空間作業、直接地圖底圖存取、批次服務，以及資料涵蓋範圍比較 (也就是影像涵蓋範圍)。

| Google 地圖功能         | Azure 地圖服務支援                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | 已規劃                                |
| REST 服務 API           | ✓                                      |
| 路線 (路線規劃)        | ✓                                      |
| 距離矩陣             | ✓                                      |
| Elevation                   | 已規劃                                |
| 地理編碼 (順向/反向) | ✓                                      |
| 地理位置                 | N/A                                    |
| 地點搜尋               | ✓                                      |
| 地點詳細資料              | N/A –可用的網站和電話號碼 |
| 地點相片               | N/A                                    |
| 地點自動完成          | ✓                                      |
| 靜態地圖                 | ✓                                      |
| 靜態街道檢視          | N/A                                    |
| 時區                   | ✓                                      |
| 地圖內嵌 API           | N/A                                    |
| 地圖 URL                    | N/A                                    |

Google 地圖提供基本的金鑰型驗證。 Azure 地圖服務同時提供基本的金鑰型驗證以及高度安全的 Azure Active Directory 驗證。

## <a name="licensing-considerations"></a>授權考量

從 Google 地圖遷移至 Azure 地圖服務時，應考量下列有關於授權的要點。

- Azure 地圖服務會根據載入的地圖底圖數目收取使用互動式地圖的費用，而 Google 地圖則會收取載入地圖控制項的費用。 在互動式 Azure 地圖服務 SDK 中會自動快取地圖底圖，以降低開發人員的成本。 每載入 15 個地圖底圖，就會產生一筆 Azure 地圖服務交易。 互動式 Azure 地圖服務 SDK 使用 512 像素的底圖，平均每個頁面檢視會產生一筆或更少的交易。
- 將 Google 地圖 Web 服務的靜態地圖影像取代為 Azure 地圖服務 Web SDK 通常會更符合成本效益。 Azure 地圖服務 Web SDK 會使用地圖底圖，且除非使用者移動瀏覽及縮放地圖，否則通常只會根據地圖載入量產生少量交易。 Azure 地圖服務 Web SDK 有停用移動瀏覽和縮放的選項。 此外，Azure 地圖服務 Web SDK 所提供的視覺效果選項遠比靜態地圖 Web 服務來得多。
- Azure 地圖服務允許將其平台中的資料儲存在 Azure 中。 您也可以根據[使用規定](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)，在別處快取最久達六個月前的資料。

以下是 Azure 地圖服務的一些相關資源：

- [Azure 地圖服務定價頁面](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure 地圖服務使用規定](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46) (包含在 Microsoft Online Services 條款中)
- [在 Azure 地圖服務中選擇正確的定價層](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>建議的移轉計劃

高階移轉計劃如下。

1. 清查您的應用程式所使用的 Google 地圖 SDK 和服務，並確認 Azure 地圖服務提供替代的 SDK 和服務可讓您遷移。
2. 經由 [https://azure.com](https://azure.com) 建立 Azure 訂用帳戶 (如果您還沒有的話)。
3. 建立 Azure 地圖服務帳戶 ([文件](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)) 和驗證金鑰或 Azure Active Directory ([文件](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication))。
4. 遷移應用程式的程式碼。
5. 測試已遷移的應用程式。
6. 將已遷移的應用程式部署至生產環境。

## <a name="azure-maps-technical-resources"></a>Azure 地圖服務技術資源

以下列出 Azure 地圖服務的實用技術資源。

- 概觀：[https://azure.com/maps](https://azure.com/maps)
- 文件：[https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Web SDK 程式碼範例：[https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- 開發人員論壇：[https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- 影片：[https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- 部落格：[https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Azure 地圖服務意見反應 (UserVoice)：[https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>移轉支援

開發人員可透過[論壇](https://aka.ms/AzureMapsForums)或透過眾多 Azure 支援選項之一尋求移轉支援：[https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>後續步驟

參考下列文章以了解如何遷移您的 Google 地圖應用程式：

> [!div class="nextstepaction"]
> [遷移 Web 應用程式](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [遷移 Android 應用程式](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [遷移 Web 服務](migrate-from-google-maps-web-services.md)
