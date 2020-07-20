---
title: Azure 地圖建立工具中的繪圖套件需求
description: 了解繪圖套件需求，以使用 Azure 地圖轉換服務來將您的設備設計檔案轉換為地圖資料
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 1ba9edba97ce89cede54287076e50eb587af10f3
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242469"
---
# <a name="drawing-package-requirements"></a>繪圖套件需求

[Azure 地圖轉換服務](https://docs.microsoft.com/rest/api/maps/conversion)可讓您將上傳的繪圖套件轉換成地圖資料。 本文將說明轉換 API 的繪圖套件需求。 若要查看範例套件，您可以下載[繪圖套件](https://github.com/Azure-Samples/am-creator-indoor-data-examples)範例。

## <a name="prerequisites"></a>Prerequisites

繪圖套件包含以 DWG 格式儲存的繪圖，這是 Autodesk 的 AutoCAD®軟體（ [Autodesk，inc. 的商標）](https://www.autodesk.com/company/legal-notices-trademarks/trademarks/guidelines-for-use#section12)的原生檔案格式。

在繪圖套件中，您可以選擇任何 CAD 軟體來產生繪圖。  

[Azure 地圖轉換服務](https://docs.microsoft.com/rest/api/maps/conversion)會將繪圖套件轉換成地圖資料。  轉換服務已使用 AutoCAD DWG 檔案格式進行開發和測試。 `AC1032` 是 DWG 檔案的內部格式版本。 建議您選取 `AC1032` 作為內部 DWG 檔案格式版本。  

本文件中使用的詞彙解釋。

| 詞彙  | 定義 |
|:-------|:------------|
| 階層 | AutoCAD DWG 圖層。|
| 層級 | 已設定高度的建築物區域。 例如，建築物的樓層。 |
| Xref  |檔案格式為 AutoCAD DWG (.dwg) 並附加至主要繪圖作為外部參考的檔案。  |
| 功能 | 結合幾何與額外中繼資料資訊的物件。 |
| 特徵類別 | 特徵的常見藍圖。 例如，「單位」是特徵類別，而「辦公室」則是一項特徵。 |

## <a name="drawing-package-structure"></a>繪圖套件結構

繪製套件是包含下列檔案的 .zip 封存檔：

* AutoCAD DWG 檔案格式的 DWG 檔案。
* 單一設施的 _manifest.json_ 檔案。

DWG 檔案可以在資料夾內以任何方式組織，但資訊清單檔案必須存留在資料夾的根目錄。 該資料夾必須壓縮成單一封存檔，副檔名為 .zip。 下面各節將詳細說明 DWG 檔案、資訊清單檔案及這些檔案內容的需求。  

## <a name="dwg-files-requirements"></a>DWG 檔案需求

設施的每個樓層都需要一個 DWG 檔案。 樓層的資料必須包含在單一 DWG 檔案中。 任何外部參考 (_xrefs_) 都必須繫結至父系繪圖。 此外，每個 DWG 檔案：

* 必須定義「外部」和「單位」圖層。 其可以選擇性地定義下列選用圖層：_Wall_、_Door_、_UnitLabel_、_Zone_ 和 _ZoneLabel_。
* 不能包含多個樓層中的特徵。
* 不能包含多個設施中的特徵。

[Azure 地圖轉換服務](https://docs.microsoft.com/rest/api/maps/conversion)可以從 DWG 檔案中將下列特徵類別解壓縮：

* Levels
* 單位
* 區域
* 開口
* 牆
* 垂直穿透

所有轉換作業都會產生一組最小的預設類別：room、structure.wall、opening.door、zone 和 facility。 其他類別則適用於物件所參考的每個類別名稱。  

DWG 圖層必須包含單一類別的特徵。 類別不得共用圖層。 例如，「單位」和「牆」不能共用圖層。

DWG 圖層也必須遵循下列準則：

* 所有 DWG 檔案的繪圖來源都必須對齊相同的緯度和經度。
* 每個樓層的方向都必須與其他樓層相同。
* 自我相交的多邊形會自動修復，而 [Azure 地圖轉換服務](https://docs.microsoft.com/rest/api/maps/conversion)會引發警告。 建議您手動檢查已修復的結果，因為其可能不符合預期的結果。

所有圖層實體都必須是下列其中一種類型：線條、聚合線條、多邊形、圓弧、圓形、文字 (單行)。 任何其他實體類型都會被忽略。

下表概述每個圖層支援的實體類型和支援的功能。 如果圖層包含不支援的實體類型，則 [Azure 地圖轉換服務](https://docs.microsoft.com/rest/api/maps/conversion)將會忽略這些實體。  

| 階層 | 實體類型 | 特性 |
| :----- | :-------------------| :-------
| [外部](#exterior-layer) | 多邊形、聚合線條 (封閉)、圓形 | Levels
| [單位](#unit-layer) |  多邊形、聚合線條 (封閉)、圓形 | 垂直穿透、單位
| [牆](#wall-layer)  | 多邊形、聚合線條 (封閉)、圓形 | 不適用。 如需詳細資訊，請參閱[牆圖層](#wall-layer)。
| [門](#door-layer) | 多邊形、聚合線條、線條、圓弧、圓形 | 開口
| [區域](#zone-layer) | 多邊形、聚合線條 (封閉)、圓形 | 區域
| [UnitLabel](#unitlabel-layer) | 文字 (單行) | 不適用。 此圖層只能從單位層將屬性新增至單位特徵。 如需詳細資訊，請參閱 [UnitLabel 圖層](#unitlabel-layer)。
| [ZoneLabel](#zonelabel-layer) | 文字 (單行) | 不適用。 此層只能從 ZonesLayer 將屬性新增至區域特徵。 如需詳細資訊，請參閱 [ZoneLabel 圖層](#zonelabel-layer)

下面各節將詳細說明每個圖層的需求。

### <a name="exterior-layer"></a>外部圖層

每個樓層的 DWG 檔案都必須包含一個圖層，以定義該樓層的周邊。 此圖層稱為「外部」圖層。 例如，如果設施包含兩個樓層，則其需要有兩個 DWG 檔案，而每個檔案都會有一個外部圖層。

不論外部圖層中有多少個實體繪圖，[產生的設施資料集](tutorial-creator-indoor-maps.md#create-a-feature-stateset)都只會為每個 DWG 檔案包含**一個**樓層特徵。 此外：

* 外部項目必須繪製成多邊形、聚合線條 (封閉)、圓形。

* 外部項目可以重疊，但會解析成一個幾何圖形。

如果圖層包含多個重疊的聚合線條，則聚合線條會解析成單一樓層特徵。 或者，如果圖層包含多個 non_overlapping 的聚合線條，則產生的樓層特徵會有多多邊形的表示方式。

外部圖層的範例可視為[繪圖套件範例](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中的 OUTLINE 圖層。

### <a name="unit-layer"></a>單位圖層

每個樓層的 DWG 檔案都應該定義一個包含單位的圖層。  單位是建築物中的可通行空間，例如辦公室、走廊、階梯和電梯。 單位圖層應遵循下列需求：

* 單位必須繪製成多邊形、聚合線條 (封閉)、圓形。
* 單位必須落在設施外部周邊的範圍內。
* 單位不能部分重疊。
* 單位不得包含任何自我相交的幾何圖形。

 藉由在 _unitLabel_ 圖層中建立文字物件來命名單位，然後將物件放在該單位的界限內。 如需詳細資訊，請參閱 [UnitLabel 圖層](#unitlabel-layer)。

單位圖層的範例可視為[繪圖套件範例](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中的 UNITS 圖層。

### <a name="wall-layer"></a>牆圖層

每個樓層的 DWG 檔案都可包含一個圖層來定義牆、柱子和其他建築物結構的實體範圍。

* 牆必須繪製成多邊形、聚合線條 (封閉)、圓形。
* 牆圖層應只包含會解譯為建築物結構的幾何圖形。

牆圖層的範例可視為[繪圖套件範例](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中的 WALLS 圖層。

### <a name="door-layer"></a>門圖層

您可以包含內含門的 DWG 圖層。 每個門都必須與單位圖層中單位的邊緣重疊。

在 Azure 地圖服務資料集中，門的開口會表示為與多個單位界限重疊的單行線段。 下列步驟會將門圖層中的幾何圖形轉換為資料集中的開口特性。

![產生開口的步驟](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>區域圖層

每個樓層的 DWG 檔案都可包含一個區域圖層來定義區域的實體範圍。 區域可以是空的室內空間或後院。

* 區域必須繪製成多邊形、聚合線條 (封閉)、圓形。
* 區域可以重疊。
* 區域可以落在設施外部周邊的內部或外部。

藉由在 _zoneLabel_ 圖層中建立文字物件來命名區域，並將文字物件放在區域的界限內。 如需詳細資訊，請參閱 [ZoneLabel 圖層](#zonelabel-layer)。

牆圖層的範例可視為[繪圖套件範例](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中的 ZONES 圖層。

### <a name="unitlabel-layer"></a>UnitLabel 圖層

每個樓層的 DWG 檔案都可以包含單位標籤圖層。 單位標籤圖層會將名稱屬性新增到從單位圖層擷取的單位。 具有名稱屬性的單位可以在資訊清單檔中指定額外的詳細資料。

* 單位標籤必須是單行文字實體。
* 單位標籤必須落在其單位的界限內。
* 單位不能在單位標籤圖層中包含多個文字實體。

UnitLabel 的範例可視為[繪圖套件範例](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中的 UNITLABELS 圖層。

### <a name="zonelabel-layer"></a>ZoneLabel 圖層

每個樓層的 DWG 檔案都可以包含區域標籤圖層。 此層會將名稱屬性新增至從區域圖層擷取的區域。 具有名稱屬性的區域可以在資訊清單檔中指定額外的詳細資料。

* 區域標籤必須是單行文字實體。
* 區域標籤必須落在其區域的界限內。
* 區域不能在區域標籤圖層中包含多個文字實體。

Zonelabel 的範例可視為[繪圖套件範例](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中的 ZONELABELS 圖層。

## <a name="manifest-file-requirements"></a>資訊清單檔的需求

zip 資料夾必須在目錄的根樓層中包含資訊清單檔，而且該檔案必須命名為 **manifest.json**。 其中會描述可讓 [Azure 地圖轉換服務](https://docs.microsoft.com/rest/api/maps/conversion)剖析其內容的 DWG 檔案。 只有資訊清單所識別的檔案可內嵌。 在 zip 資料夾中但未在資訊清單中正確列出的檔案將會遭到忽略。

在資訊清單檔的 **buildingLevels** 物件中，檔案路徑必須相對於 zip 資料夾的根目錄。 DWG 檔案名稱必須完全符合設施樓層的名稱。 例如，「地下室」樓層的 DWG 檔案會是 "Basement.dwg"。 樓層 2 的 DWG 檔案會命名為 "level_2.dwg"。 如果您的樓層名稱有空格，請使用底線。

雖然在使用資訊清單物件時有一些需求，但並非所有的物件都是必要的。 下表顯示 [Azure 地圖轉換服務](https://docs.microsoft.com/rest/api/maps/conversion) 1.1 版的必要和選用物件。

| Object | 必要 | 描述 |
| :----- | :------- | :------- |
| version | true |資訊清單架構版本。 目前僅支援版本1.1。|
| directoryInfo | true | 概述設施的地理和連絡資訊。 其也可以用來概述住戶的地理和連絡資訊。 |
| buildingLevels | true | 指定建築物的樓層，以及包含樓層設計的檔案。 |
| georeference | true | 包含設施繪圖的數值地理資訊。 |
| dwgLayers | true | 列出圖層的名稱，而每個圖層都會列出其所有特徵的名稱。 |
| unitProperties | false | 可以用來插入單位特徵的其他中繼資料。 |
| zoneProperties | false | 可以用來插入區域特徵的其他中繼資料。 |

下面各節將詳細說明每個物件的需求。

### <a name="directoryinfo"></a>directoryInfo

| 屬性  | type | 必要 | 描述 |
|-----------|------|----------|-------------|
| NAME      | 字串 | true   |  建築物的名稱。 |
| streetAddress|    字串 |    false    | 建築物的地址。 |
|unit     | 字串    |  false    |  建築物中的單位。 |
| 地區 |    字串 |    false |    區域 (area)、鄰近地區或地區 (region) 的名稱。 例如，「Overlake」或「中央區域」。 地區不是郵寄地址的一部分。 |
| adminDivisions |    字串的 JSON 陣列 |    false     | 包含地址名稱 (國家/地區、州、城市) 或 (國家/地區、轄區、城市、城鎮) 的陣列。 使用 ISO 3166 國家/地區代碼和 ISO 3166-2 州/地區代碼。 |
| postalCode |    字串    | false    | 郵寄分類代碼。 |
| hoursOfOperation |    字串 |     false | 遵循 [OSM 開放時間](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification)格式。 |
| 電話    | 字串 |    false |    與建築物相關聯的電話號碼。 必須包含國家/地區代碼。 |
| 網站    | 字串 |    false    | 與建築物相關聯的網站。 以 http 或 https 開頭。 |
| nonPublic |    bool    | false | 指定建築物是否為公開的旗標。 |
| anchorLatitude | NUMERIC |    false | 設施錨點的緯度 (圖釘)。 |
| anchorLongitude | NUMERIC |    false | 設施錨點的經度 (圖釘)。 |
| anchorHeightAboveSeaLevel  | NUMERIC | false | 設施地面在海平面上的高度 (以公尺為單位)。 |
| defaultLevelVerticalExtent | NUMERIC | false | 未定義樓層的 `verticalExtent` 時，用於此設施樓層的預設高度 (厚度)。 |

### <a name="buildinglevels"></a>buildingLevels

`buildingLevels` 物件包含建築物樓層的 JSON 陣列。

| 屬性  | 類型 | 必要 | 描述 |
|-----------|------|----------|-------------|
|LevelName    |字串    |true |    描述性樓層名稱。 例如：1 樓 (Floor 1)、大廳 (Lobby)、藍色停車區 (Blue Parking) 或地下室 (Basement) 等等。|
|序數 | integer |    true | 序數會用來判斷樓層的垂直順序。 每個設施都必須具有序數 0 的樓層。 |
|heightAboveFacilityAnchor | NUMERIC | false |    錨點上方的層級高度（以量為單位）。 |
| verticalExtent | NUMERIC | false | 樓層的地板到天花板高度 (厚度)，以公尺為單位。 |
|filename |    字串 |    true |    建築物樓層 CAD 繪圖的檔案系統路徑。 其必須相對於建築物 zip 檔案的根目錄。 |

### <a name="georeference"></a>georeference

| 屬性  | 類型 | 必要 | 描述 |
|-----------|------|----------|-------------|
|lat    | NUMERIC |    true |    設施繪圖的原始緯度十進位標記法。 原始座標必須在 WGS84 Web Mercator (`EPSG:3857`) 中。|
|lon    |NUMERIC|    true|    設施繪圖的原始經度十進位標記法。 原始座標必須在 WGS84 Web Mercator (`EPSG:3857`) 中。 |
|角度|    NUMERIC|    true|   以度為單位的順時針角度，介於真北部和繪圖的垂直 (Y) 軸之間。   |

### <a name="dwglayers"></a>dwgLayers

| 屬性  | 類型 | 必要 | 描述 |
|-----------|------|----------|-------------|
|exterior    |字串陣列|    true|    定義外部建築物概況的圖層名稱。|
|unit|    字串陣列|    true|    用於定義單位的圖層名稱。|
|wall|    字串的陣列    |false|    用於定義牆的圖層名稱。|
|door    |字串的陣列|    false   | 用於定義門的圖層名稱。|
|unitLabel    |字串的陣列|    false    |用於定義單位名稱的圖層名稱。|
|區域 | 字串的陣列    | false    | 用於定義區域的圖層名稱。|
|zoneLabel | 字串的陣列 |     false |    用於定義區域名稱的圖層名稱。|

### <a name="unitproperties"></a>unitProperties

`unitProperties` 物件包含單位屬性的 JSON 陣列。

| 屬性  | 類型 | 必要 | 描述 |
|-----------|------|----------|-------------|
|unitName    |字串    |true    |要與此 `unitProperty` 記錄相關聯的單位名稱。 只有在 `unitLabel` 圖層中找到符合 `unitName` 的標籤時，此記錄才有效。 |
|categoryName|    字串|    false    |類別名稱。 如需類別的完整清單，請參閱[類別](https://aka.ms/pa-indoor-spacecategories)。 |
|navigableBy| 字串的陣列 |    false    |表示可穿過單位的通行代理項目類型。 例如「行人 (pedestrian)」。 此屬性會通知導向功能。  允許的值為 `pedestrian`、`wheelchair`、`machine`、`bicycle`、`automobile`、`hiredAuto`、`bus`、`railcar`、`emergency`、`ferry`、`boat` 和 `disallowed`。|
|routeThroughBehavior|    字串|    false    |單位的路徑通過行為。 允許的值為 `disallowed`、`allowed` 和 `preferred`。 預設值為 `allowed`。|
|住戶    |directoryInfo 物件的陣列 |false    |單位的住戶清單。 |
|nameAlt|    字串|    false|    單位的替代名稱。 |
|nameSubtitle|    字串    |false|    單位的子標題。 |
|addressRoomNumber|    字串|    false|    單位的房間/單位/公寓/套房編號。|
|verticalPenetrationCategory|    字串|    false| 定義此屬性時，產生的特徵將會是垂直穿透 (VRT)，而不是一個單位。 VRT 可以用來流覽至其上方或下方樓層中的其他 VRT 特徵。 垂直穿透是[類別](https://aka.ms/pa-indoor-spacecategories)名稱。 如果已定義此屬性，則 categoryName 屬性會由 verticalPenetrationCategory 覆寫。 |
|verticalPenetrationDirection|    字串|    false    |如果已定義 `verticalPenetrationCategory`，則可選擇定義有效的行進方向。 允許的值為 `lowToHigh`、`highToLow`、`both` 和 `closed`。 預設值為 `both`。|
| nonPublic | bool | false | 指出單位是否要向外公開。 |
| isRoutable | bool | false | 設定為 `false` 時，將無法通行或穿過單位。 預設值為 `true`。 |
| isOpenArea | bool | false | 允許流覽代理程式輸入單位，而不需要開啟連接到單位。 根據預設，此值會設為， `true` 代表沒有任何空缺的單位; `false` 針對具有開頭的單位。  在沒有開頭的單元上，手動將設定 `isOpenArea` 為，會 `false` 產生警告。 這是因為流覽代理程式無法觸達產生的單位。|

### <a name="the-zoneproperties-object"></a>zoneProperties 物件

`zoneProperties` 物件包含區域屬性的 JSON 陣列。

| 屬性  | 類型 | 必要 | 描述 |
|-----------|------|----------|-------------|
|zoneName        |字串    |true    |要與 `zoneProperty` 記錄相關聯的區域名稱。 只有在區域的 `zoneLabel` 圖層中找到符合 `zoneName` 的標籤時，此記錄才有效。  |
|categoryName|    字串|    false    |類別名稱。 如需類別的完整清單，請參閱[類別](https://aka.ms/pa-indoor-spacecategories)。 |
|zoneNameAlt|    字串|    false    |區域的替代名稱。  |
|zoneNameSubtitle|    字串 |    false    |區域的子標題。 |
|zoneSetId|    字串 |    false    | 設定 ID 以建立多個區域之間的關聯性，讓它們可以查詢或選取為群組。 例如，跨多個層級的區域。 |

### <a name="sample-drawing-package-manifest"></a>繪圖套件資訊清單範例

以下是繪圖套件範例的資訊清單檔範例。 若要下載整個套件，請按一下[繪圖套件範例](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。

#### <a name="manifest-file"></a>資訊清單檔

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddresss": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonWheelchairAccessible": false, 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>後續步驟

一旦您的繪圖套件符合需求，您就可以使用 [Azure 地圖轉換服務](https://docs.microsoft.com/rest/api/maps/conversion)，將套件轉換成地圖資料集。 然後，您可以使用室內地圖模組，以資料集產生室內地圖。 請閱讀下列文章，以深入了解如何使用室內地圖模組：

> [!div class="nextstepaction"]
>[室內地圖的建立工具](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [教學課程：建立建立工具的室內地圖](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [室內地圖動態樣式](indoor-map-dynamic-styling.md)
