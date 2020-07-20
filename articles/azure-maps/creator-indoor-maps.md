---
title: 在 Azure 地圖服務建立工具中使用室內地圖
description: 本文介紹適用於 Azure 地圖服務建立工具服務的概念。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d55a0726000a6ed55f049ac4a4146f66421a7085
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242571"
---
# <a name="creator-for-indoor-maps"></a>室內地圖的建立工具

本文介紹適用於 Azure 地圖服務建立工具的概念和工具。 建議您先閱讀這篇文章，再開始使用 Azure 地圖服務建立工具 API 和 SDK。

您可以使用建立工具，依據室內地圖資料來開發具有地圖功能的應用程式。 本文說明上傳、轉換、建立和使用地圖資料的流程。 下圖說明整個工作流程。

![建立工具地圖資料工作流程](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator"></a>建立 Azure 地圖服務建立工具

若要使用建立工具服務，必須在 Azure 地圖服務帳戶中建立 Azure 地圖服務建立工具。 如需如何在 Azure 地圖服務中建立 Azure 地圖服務建立工具的詳細資訊，請參閱[管理 Azure 地圖服務建立工具](how-to-manage-creator.md)。

## <a name="upload-a-drawing-package"></a>上傳繪圖套件

建立工具會藉由轉換上傳的繪圖套件來收集室內地圖資料。 繪圖套件代表已結構化或重新塑造的設施。 如需有關繪圖套件需求的詳細資訊，請參閱[繪圖套件需求](drawing-requirements.md)。

使用 [Azure 地圖服務資料上傳 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)，上傳繪圖套件。  成功上傳時，資料上傳 API 會傳回使用者資料識別碼 (`udid`)。 下一個步驟會使用 `udid`，將已上傳的套件轉換為室內地圖資料。

## <a name="convert-a-drawing-package"></a>轉換繪圖套件

[Azure 地圖轉換服務](https://docs.microsoft.com/rest/api/maps/conversion)會將上傳的繪圖套件轉換成室內地圖資料。 轉換服務也會驗證套件。 驗證問題分為錯誤和警告兩種類型。 如果偵測到任何錯誤，轉換流程會失敗。 若偵測到警告，轉換則會成功。 但建議您檢閱所有警告並加以解決。 出現警告時，表示已忽略或自動修正轉換的一部分。 若無法解決警告，可能會導致後續的程序發生錯誤。 如需詳細資訊，請參閱[繪圖套件警告和錯誤](drawing-conversion-error-codes.md)。

發生錯誤時，轉換服務會提供 [Azure 地圖服務繪圖錯誤視覺化檢視](drawing-error-visualizer.md)獨立 Web 應用程式的連結。 您可以使用繪圖錯誤視覺化檢視，檢查在轉換流程期間發生的[繪圖套件警告和錯誤](drawing-conversion-error-codes.md)。 修正錯誤後，您就可以嘗試上傳和轉換套件。

## <a name="create-indoor-map-data"></a>建立室內地圖資料

Azure 地圖服務建立工具提供三項服務：

* [資料集服務](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)。
使用資料集服務，從已轉換的繪圖套件資料建立資料集。
* [地圖底圖集服務](https://docs.microsoft.com/rest/api/maps/tileset/createpreview)。
使用地圖底圖集服務來建立資料集的向量型標記法。 應用程式可以使用地圖底圖集，呈現以視覺效果地圖底圖為基礎的資料集檢視。
* [功能狀態服務](https://docs.microsoft.com/rest/api/maps/featurestate)。使用功能狀態服務來支援動態地圖樣式。 動態地圖樣式可讓應用程式反映 IoT 系統所提供空間的即時事件。

### <a name="datasets"></a>資料集

資料集是室內地圖功能的集合。 室內地圖功能代表已轉換之繪圖套件中定義的設施。 建立具有[資料集服務](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)的資料集後，您可以建立任意數目的[地圖底圖集](#tilesets)或[功能狀態集](#feature-statesets)。

[資料集服務](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)可讓開發人員隨時新增或移除現有資料集的設施。 如需如何使用 API 更新現有資料集的詳細資訊，請參閱[資料集服務](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)中的附加選項。 如需如何更新資料集的範例，請參閱[資料維護](#data-maintenance)。

### <a name="tilesets"></a>地圖底圖集

地圖底圖集是向量資料的集合，代表一組統一的方格地圖底圖。 開發人員可以使用[地圖底圖集服務](https://docs.microsoft.com/rest/api/maps/tileset/createpreview)，從資料集建立地圖底圖集。

若要反映不同的內容階段，您可以從相同的資料集建立多個地圖底圖集。 例如，您可以使用傢俱和設備來建立一個地圖底圖集，並建立另一個沒有傢俱和設備的地圖底圖集。  您可以選擇使用最新的資料更新產生一個地圖底圖集，並產生另一個並非使用最新資料更新的地圖底圖集。

除了向量資料以外，地圖底圖集還提供地圖轉譯最佳化的中繼資料。 例如，地圖底圖集中繼資料包含地圖底圖集的最小和最大縮放層級。 中繼資料也會提供定義地圖底圖集地理範圍的周框方塊。 周框方塊可讓應用程式以程式設計方式設定正確的中心點。 如需有關地圖底圖集中繼資料的詳細資訊，請參閱[地圖底圖集清單 API](https://docs.microsoft.com/rest/api/maps/tileset/listpreview)。

建立地圖底圖集後，[Render V2 服務](#render-v2-service)就可以加以擷取。

如果地圖底圖集已過時且不再有用，您可以刪除該地圖底圖集。 如需有關如何刪除地圖底圖集的詳細資訊，請參閱[資料維護](#data-maintenance)。

>[!NOTE]
>地圖底圖集並不依賴其建立來源的資料集。 如果您從資料集建立地圖底圖集，接著更新該資料集，則系統不會更新地圖底圖集。 若要反映資料集中的變更，您必須建立新的地圖底圖集。 同樣地，如果您刪除地圖底圖集，資料集將不會受到影響。

### <a name="feature-statesets"></a>功能狀態集

功能狀態集是指派給資料集功能 (例如空間或設備) 的動態屬性集合 (「狀態」)。 「狀態」的範例可能是溫度或使用量。 每個「狀態」都是索引鍵/值組，其中包含屬性名稱、值和上次更新的時間戳記。

[功能狀態服務](https://docs.microsoft.com/rest/api/maps/featurestate/createstatesetpreview)可讓您建立和管理資料集的功能狀態集。 狀態集是由一或多個 「狀態」所定義。 每項功能 (例如空間) 都可以附加一個「狀態」。

IoT 裝置或其他應用程式可以更新或擷取狀態集中每個「狀態」的值。  例如，使用[功能狀態更新 API](https://docs.microsoft.com/rest/api/maps/featurestate/updatestatespreview)，測量空間佔用量的裝置可以有系統地張貼空間的狀態變更。

應用程式可以使用功能狀態集，根據其目前狀態和各自的地圖樣式，動態呈現設施中的功能。 如需在轉譯地圖中使用功能狀態集樣式功能的詳細資訊，請參閱[室內 Web SDK 模組](#indoor-maps-module)。

>[!NOTE]
>如同地圖底圖集，變更資料集並不會影響現有的功能狀態集，且刪除功能狀態集將不會影響其所附加的資料集。

## <a name="using-indoor-maps"></a>使用室內地圖

### <a name="render-v2-service"></a>Render V2 服務

Azure 地圖服務 [Render V2 服務-取得地圖底圖 API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) 已擴充為支援建立工具地圖底圖集。

[ V2 服務-取得地圖狀態地圖底圖 API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) 允許應用程式要求地圖底圖集。 然後，您可以將地圖底圖集整合到地圖控制項或 SDK 中。 如需使用 Render V2 服務之地圖控制項的範例，請參閱[室內地圖模組](#indoor-maps-module)。

### <a name="web-feature-service-api"></a>Web 功能服務 API

資料集可使用 [Web 功能服務 (WFS) API](https://docs.microsoft.com/rest/api/maps/wfs) 來查詢。 WFS 會依循[開放地理空間協會 API 功能](http://docs.opengeospatial.org/DRAFTS/17-069r1.html)。 WFS API 可讓您查詢資料集內的功能。 例如，您可以使用 WFS 來尋找特定設施和樓層的所有中型會議室。

### <a name="indoor-maps-module"></a>室內地圖模組

[Azure 地圖服務 Web SDK](https://docs.microsoft.com/azure/azure-maps/) 包括室內地圖模組。 此模組為 Azure 地圖服務「地圖控制項」程式庫提供擴充功能。 室內地圖模組會轉譯在建立工具中建立的室內地圖。 其會將小工具 (例如「樓層選擇器」) 整合在一起，協助使用者將不同的樓層視覺化。

室內地圖模組可讓您建立 Web 應用程式，將室內地圖資料與其他各項 [Azure 地圖服務](https://docs.microsoft.com/azure/azure-maps/)進行整合。 最常見的應用程式設定可能包括將知識新增至來自其他地圖的室內地圖，例如道路、影像、天氣和運輸。

室內地圖模組也支援動態地圖樣式。 如需如何在應用程式中執行功能狀態集動態樣式的逐步解說，請參閱[如何使用室內地圖模組](how-to-use-indoor-module.md)

### <a name="azure-maps-integration"></a>Azure 地圖服務整合

開始開發適用於室內地圖的解決方案時，您可以探索整合現有 Azure 地圖服務功能的方式。 例如，您可以使用 [Azure 地圖服務地理柵欄 API](https://docs.microsoft.com/rest/api/maps/spatial/postgeofence) 搭配建立工具室內地圖，來執行資產追蹤或安全性案例。 地理柵欄 API 可用來判斷員工是否進入或離開特定的室內區域。 如需有關如何使用 IoT 遙測與 Azure 地圖服務連線的詳細資訊，請參閱[這裡](tutorial-iot-hub-maps.md)。

### <a name="data-maintenance"></a>資料維護

 Azure 地圖服務建立工具清單、更新和刪除 API 可讓您列出、更新和刪除您的資料集、地圖底圖集和功能狀態集。

>[!NOTE]
>每當檢閱項目清單，並決定將其刪除時，您必須考慮該刪除動作對所有相依 API 或應用程式的影響。 例如，如果您要透過 [Render V2 - 取得地圖底圖 API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) 刪除目前由應用程式使用的地圖底圖集，刪除該地圖底圖集會導致應用程式無法轉譯該地圖底圖集。

### <a name="example-updating-a-dataset"></a>範例：更新資料集

下列範例示範如何更新資料集、建立新的地圖底圖集，以及刪除舊的地圖底圖集。

1. 遵循[上傳繪圖套件](#upload-a-drawing-package)和[繪圖套件](#convert-a-drawing-package)章節中的步驟，上傳和轉換新的繪圖套件。

2. 使用[資料集建立 API](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)，將已轉換的資料附加至現有的資料集。

3. 使用[地圖底圖建立 API](https://docs.microsoft.com/rest/api/maps/tileset/createpreview)，從更新的資料集產生新的地圖底圖集。 儲存步驟 4 的新地圖底圖集識別碼。

4. 更新應用程式中的地圖底圖集識別碼，以啟用已更新之校園資料集的視覺效果。 如果不再使用舊的地圖底圖集，您可以將其刪除。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：建立「建立工具室內地圖」](tutorial-creator-indoor-maps.md)
