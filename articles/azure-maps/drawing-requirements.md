---
title: Azure 地圖建立工具中的繪圖套件需求
description: 瞭解轉換設備設計檔案以對應資料的繪圖套件需求
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 2c3e46bf386e70cbe35d96728ede896d6bf0dc7d
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895829"
---
# <a name="drawing-package-requirements"></a>繪圖套件需求

您可以使用 [Azure 地圖服務轉換服務](/rest/api/maps/conversion)，將已上傳的繪圖封裝轉換成對應資料。 本文將說明轉換 API 的繪圖套件需求。 若要查看範例套件，您可以下載[繪圖套件](https://github.com/Azure-Samples/am-creator-indoor-data-examples)範例。

## <a name="prerequisites"></a>Prerequisites

繪圖套件包含以 DWG 格式儲存的繪圖，也就是 Autodesk 的 AutoCAD®軟體的原生檔案格式。

您可以選擇任何 CAD 軟體來產生繪圖套件中的繪圖。  

[Azure 地圖轉換服務](/rest/api/maps/conversion)會將繪圖套件轉換成地圖資料。 轉換服務適用于 AutoCAD DWG 檔案格式。 `AC1032` 是 DWG 檔案的內部格式版本，建議您 `AC1032` 針對內部 DWG 檔案格式版本選取。  

## <a name="glossary-of-terms"></a>名詞解釋

為了方便參考，以下是您在閱讀本文時很重要的一些術語和定義。

| 詞彙  | 定義 |
|:-------|:------------|
| 階層 | AutoCAD DWG 圖層。|
| 層級 | 已設定高度的建築物區域。 例如，建築物的樓層。 |
| Xref  |以 AutoCAD DWG 檔案格式)  ( 的檔案，會附加至主要繪圖作為外部參考。  |
| 功能 | 結合幾何與額外中繼資料資訊的物件。 |
| 功能類別 | 特徵的常見藍圖。 例如，某個 *單位* 是功能類別，而 *辦公室* 是一項功能。 |

## <a name="drawing-package-structure"></a>繪圖套件結構

繪製套件是包含下列檔案的 .zip 封存檔：

* AutoCAD DWG 檔案格式的 DWG 檔案。
* 單一設施的 _manifest.json_ 檔案。

您可以在資料夾內以任何方式組織 DWG 檔案，但資訊清單檔必須存留在資料夾的根目錄中。 您必須將資料夾壓縮成 .zip 副檔名的單一保存檔案。 下一節將詳細說明 DWG 檔案的需求、資訊清單檔案，以及這些檔案的內容。  

## <a name="dwg-files-requirements"></a>DWG 檔案需求

設施的每個樓層都需要一個 DWG 檔案。 樓層的資料必須包含在單一 DWG 檔案中。 任何外部參考 ( _xrefs_ ) 都必須繫結至父系繪圖。 此外，每個 DWG 檔案：

* 必須定義「外部」和「單位」圖層。 它可以選擇性地定義下列選用圖層： _牆_ 、 _門_ 、 _UnitLabel_ 、 _Zone_ 和 _ZoneLabel_ 。
* 不能包含多個樓層中的特徵。
* 不能包含多個設施中的特徵。

[Azure 地圖轉換服務](/rest/api/maps/conversion)可以從 DWG 檔案中將下列特徵類別解壓縮：

* Levels
* 單位
* 區域
* 開口
* 牆
* 垂直 penetrations

所有轉換作業都會產生一組最小的預設類別：room、structure.wall、opening.door、zone 和 facility。 其他類別是針對物件所參考的每個類別目錄名稱。  

DWG 圖層必須包含單一類別的特徵。 類別不得共用圖層。 例如，「單位」和「牆」不能共用圖層。

DWG 圖層也必須遵循下列準則：

* 所有 DWG 檔案的繪圖來源都必須對齊相同的緯度和經度。
* 每個樓層的方向都必須與其他樓層相同。
* 自動相交的多邊形會自動修復，而 [Azure 地圖服務轉換服務](/rest/api/maps/conversion) 會引發警告。 您應手動檢查修復的結果，因為它們可能不符合預期的結果。

所有圖層實體都必須是下列其中一種類型：直線、折線、多邊形、圓弧、圓形或文字 (單一線條) 。 系統會忽略任何其他實體類型。

下表概述支援的實體類型，以及每個圖層的支援功能。 如果圖層包含不支援的實體類型，則 [Azure 地圖服務轉換服務](/rest/api/maps/conversion) 會忽略這些實體。  

| 階層 | 實體類型 | 特性 |
| :----- | :-------------------| :-------
| [外部](#exterior-layer) | 多邊形、聚合線條 (封閉)、圓形 | Levels
| [單位](#unit-layer) |  多邊形、聚合線條 (封閉)、圓形 | 垂直 penetrations，單位
| [牆](#wall-layer)  | 多邊形、聚合線條 (封閉)、圓形 | 不適用。 如需詳細資訊，請參閱[牆圖層](#wall-layer)。
| [門](#door-layer) | 多邊形、聚合線條、線條、圓弧、圓形 | 開口
| [區域](#zone-layer) | 多邊形、聚合線條 (封閉)、圓形 | 區域
| [UnitLabel](#unitlabel-layer) | 文字 (單行) | 不適用。 此圖層只能從單位層將屬性新增至單位特徵。 如需詳細資訊，請參閱 [UnitLabel 圖層](#unitlabel-layer)。
| [ZoneLabel](#zonelabel-layer) | 文字 (單行) | 不適用。 此層只能從 ZonesLayer 將屬性新增至區域特徵。 如需詳細資訊，請參閱 [ZoneLabel 圖層](#zonelabel-layer)。

下面各節將詳細說明每個圖層的需求。

### <a name="exterior-layer"></a>外部圖層

每個樓層的 DWG 檔案都必須包含一個圖層，以定義該樓層的周邊。 這一層稱為「 *外部* 」層。 例如，如果設施包含兩個樓層，則其需要有兩個 DWG 檔案，而每個檔案都會有一個外部圖層。

無論外部層有多少個實體繪圖， [產生的設備資料集](tutorial-creator-indoor-maps.md#create-a-feature-stateset) 都只會包含一個適用于每個 DWG 檔案的層級功能。 此外：

* Exteriors 必須以多邊形、聚合線條 (封閉) 或圓形的形式繪製。
* Exteriors 可以重迭，但會被解析成一個幾何。

如果圖層包含多個重迭的多個線條，則會將多線條解析成單一層級功能。 或者，如果圖層包含多個非重迭的多個線條，則產生的層級功能會有多多邊形標記法。

您可以在 [範例繪圖套件](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中看到外部圖層的範例，作為大綱層。

### <a name="unit-layer"></a>單位圖層

每個層級的 DWG 檔案會定義包含單位的圖層。 單位是建築物中的可通行空間，例如辦公室、走廊、階梯和電梯。 單位圖層應遵循下列需求：

* 單位必須繪製為多邊形、聚合線條 (封閉) 或圓形。
* 單位必須落在設施外部周邊的範圍內。
* 單位不能部分重疊。
* 單位不得包含任何自我相交的幾何圖形。

藉由在 UnitLabel 圖層中建立文字物件來命名單位，然後將物件放在單元的界限內。 如需詳細資訊，請參閱 [UnitLabel 圖層](#unitlabel-layer)。

您可以在 [範例繪圖套件](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中看到單位層的範例。

### <a name="wall-layer"></a>牆圖層

每個層級的 DWG 檔案都可以包含圖層，以定義背景牆、資料行和其他建築物結構的實體範圍。

* 您必須以多邊形、聚合線條 (封閉的) 或圓形來繪製牆壁。
* 牆壁圖層應只包含被解釋為建立結構的幾何。

您可以在 [範例繪圖套件](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中看到牆壁圖層的範例。

### <a name="door-layer"></a>門圖層

您可以包含包含門的 DWG 層。 每門都必須與單位層的單位重迭。

Azure 地圖服務資料集中的門，會表示為與多個單位界限重迭的單一行區段。 下列影像顯示如何轉換門層中的幾何，以開啟資料集中的功能。

![四個圖形，顯示產生開頭的步驟](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>區域圖層

每個層級的 DWG 檔案可以包含定義區域實體範圍的區域層。 區域可以是空的室內空間或後院。

* 區域必須以多邊形、聚合線條 (封閉) 或圓形的形式繪製。
* 區域可以重迭。
* 區域可以落在設備的外部周邊或外部。

藉由在 ZoneLabel 圖層中建立文字物件來命名區域，並將文字物件放在區域的界限內。 如需詳細資訊，請參閱 [ZoneLabel 圖層](#zonelabel-layer)。

您可以在 [範例繪圖套件](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中看到區域圖層的範例。

### <a name="unitlabel-layer"></a>UnitLabel 圖層

每個層級的 DWG 檔案可以包含 UnitLabel 圖層。 UnitLabel 圖層會將名稱屬性新增至從單元層解壓縮的單位。 具有名稱屬性的單位可以在資訊清單檔中指定額外的詳細資料。

* 單位標籤必須是單行文字實體。
* 單位標籤必須落在其單位的界限內。
* 單位不得包含 UnitLabel 圖層中的多個文字實體。

您可以在 [範例繪圖套件](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中看到 UnitLabel 層的範例。

### <a name="zonelabel-layer"></a>ZoneLabel 圖層

每個層級的 DWG 檔案可以包含 ZoneLabel 圖層。 此層會將名稱屬性新增至從區域圖層擷取的區域。 具有名稱屬性的區域可以在資訊清單檔中指定額外的詳細資料。

* 區域標籤必須是單行文字實體。
* 區域標籤必須落在其區域的界限內。
* 區域不得包含 ZoneLabel 圖層中的多個文字實體。

您可以在 [範例繪圖套件](https://github.com/Azure-Samples/am-creator-indoor-data-examples)中看到 ZoneLabel 層的範例。

## <a name="manifest-file-requirements"></a>資訊清單檔的需求

zip 資料夾必須在目錄的根樓層中包含資訊清單檔，而且該檔案必須命名為 **manifest.json** 。 其中會描述可讓 [Azure 地圖轉換服務](/rest/api/maps/conversion)剖析其內容的 DWG 檔案。 只有資訊清單所識別的檔案會內嵌。 Zip 資料夾中的檔案，但未正確列在資訊清單中，會被忽略。

資訊清單檔之物件中的檔案路徑 `buildingLevels` 必須相對於 ZIP 檔案夾的根目錄。 DWG 檔案名稱必須完全符合設施樓層的名稱。 例如，「下一層」層級的 DWG 檔案為「下 dwg」。 層級2的 DWG 檔案會命名為 "level_2。 如果您的樓層名稱有空格，請使用底線。

雖然使用資訊清單物件時有一些需求，但並非所有物件都是必要的。 下表顯示 [Azure 地圖服務轉換服務](/rest/api/maps/conversion)1.1 版的必要和選擇性物件。

| Object | 必要 | 描述 |
| :----- | :------- | :------- |
| `version` | true |資訊清單架構版本。 目前只支援1.1 版。|
| `directoryInfo` | true | 概述設施的地理和連絡資訊。 其也可以用來概述住戶的地理和連絡資訊。 |
| `buildingLevels` | true | 指定建築物的樓層，以及包含樓層設計的檔案。 |
| `georeference` | true | 包含設施繪圖的數值地理資訊。 |
| `dwgLayers` | true | 列出圖層的名稱，而每個圖層都會列出其所有特徵的名稱。 |
| `unitProperties` | false | 可以用來插入單位特徵的其他中繼資料。 |
| `zoneProperties` | false | 可以用來插入區域特徵的其他中繼資料。 |

下面各節將詳細說明每個物件的需求。

### `directoryInfo`

| 屬性  | 類型 | 必要 | 描述 |
|-----------|------|----------|-------------|
| `name`      | 字串 | true   |  建築物的名稱。 |
| `streetAddress`|    字串 |    false    | 建築物的地址。 |
|`unit`     | 字串    |  false    |  建築物中的單位。 |
| `locality` |    字串 |    false |    區域 (area)、鄰近地區或地區 (region) 的名稱。 例如，「Overlake」或「中央區域」。 地區不是郵寄地址的一部分。 |
| `adminDivisions` |    字串的 JSON 陣列 |    false     | 包含地址名稱 (國家/地區、州、城市) 或 (國家/地區、轄區、城市、城鎮) 的陣列。 使用 ISO 3166 國家/地區代碼和 ISO 3166-2 州/地區代碼。 |
| `postalCode` |    字串    | false    | 郵寄分類代碼。 |
| `hoursOfOperation` |    字串 |     false | 遵循 [OSM 開放時間](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification)格式。 |
| `phone`    | 字串 |    false |    與建築物相關聯的電話號碼。 必須包含國家/地區代碼。 |
| `website`    | 字串 |    false    | 與建築物相關聯的網站。 必須以 HTTP 或 HTTPs 開頭。 |
| `nonPublic` |    bool    | false | 指定建築物是否為公開的旗標。 |
| `anchorLatitude` | NUMERIC |    false | 設施錨點的緯度 (圖釘)。 |
| `anchorLongitude` | NUMERIC |    false | 設施錨點的經度 (圖釘)。 |
| `anchorHeightAboveSeaLevel`  | NUMERIC | false | 設施地面在海平面上的高度 (以公尺為單位)。 |
| `defaultLevelVerticalExtent` | NUMERIC | false | 未定義樓層的 `verticalExtent` 時，用於此設施樓層的預設高度 (厚度)。 |

### `buildingLevels`

`buildingLevels` 物件包含建築物樓層的 JSON 陣列。

| 屬性  | 類型 | 必要 | 描述 |
|-----------|------|----------|-------------|
|`levelName`    |字串    |true |    描述性樓層名稱。 例如：地板1、大廳、藍色停車或下廳。|
|`ordinal` | integer |    true | 決定層級的垂直順序。 每個設施都必須具有序數 0 的樓層。 |
|`heightAboveFacilityAnchor` | NUMERIC | false |    層級的高度高度（以量為單位）。 |
| `verticalExtent` | NUMERIC | false | 層對端高度 (計量的粗細) 。 |
|`filename` |    字串 |    true |    建築物樓層 CAD 繪圖的檔案系統路徑。 其必須相對於建築物 zip 檔案的根目錄。 |

### `georeference`

| 屬性  | 類型 | 必要 | 描述 |
|-----------|------|----------|-------------|
|`lat`    | NUMERIC |    true |    設施繪圖的原始緯度十進位標記法。 原始座標必須在 WGS84 Web Mercator (`EPSG:3857`) 中。|
|`lon`    |NUMERIC|    true|    設施繪圖的原始經度十進位標記法。 原始座標必須在 WGS84 Web Mercator (`EPSG:3857`) 中。 |
|`angle`|    NUMERIC|    true|   以度為單位的順時針角度（以度為單位），介於 true 北部和繪圖的垂直 (Y) 軸之間。   |

### `dwgLayers`

| 屬性  | 類型 | 必要 | 描述 |
|-----------|------|----------|-------------|
|`exterior`    |字串陣列|    true|    定義外部建築物設定檔的圖層名稱。|
|`unit`|    字串陣列|    true|    定義單位的圖層名稱。|
|`wall`|    字串陣列    |false|    定義牆的圖層名稱。|
|`door`    |字串陣列|    false   | 定義門的圖層名稱。|
|`unitLabel`    |字串陣列|    false    |定義單位名稱的圖層名稱。|
|`zone` | 字串陣列    | false    | 定義區域的圖層名稱。|
|`zoneLabel` | 字串陣列 |     false |    定義區功能變數名稱稱的圖層名稱。|

### `unitProperties`

`unitProperties` 物件包含單位屬性的 JSON 陣列。

| 屬性  | 類型 | 必要 | 描述 |
|-----------|------|----------|-------------|
|`unitName`    |字串    |true    |要與此 `unitProperty` 記錄相關聯的單位名稱。 只有在 `unitName` 圖層中找到標籤相符的情況下，此記錄才有效 `unitLabel` 。 |
|`categoryName`|    字串|    false    |類別目錄名稱。 如需類別的完整清單，請參閱[類別](https://aka.ms/pa-indoor-spacecategories)。 |
|`navigableBy`| 字串陣列 |    false    |表示可穿過單位的通行代理項目類型。 這個屬性會通知導向功能。 允許的值為：、、、、、、、、、、 `pedestrian` `wheelchair` `machine` `bicycle` `automobile` `hiredAuto` `bus` `railcar` `emergency` `ferry` `boat` 和 `disallowed` 。|
|`routeThroughBehavior`|    字串|    false    |單位的路徑通過行為。 允許的值為 `disallowed`、`allowed` 和 `preferred`。 預設值是 `allowed`。|
|`occupants`    |directoryInfo 物件的陣列 |false    |單位的住戶清單。 |
|`nameAlt`|    字串|    false|    單位的替代名稱。 |
|`nameSubtitle`|    字串    |false|    單位的子標題。 |
|`addressRoomNumber`|    字串|    false|    單位的房間、單位、單位或套件編號。|
|`verticalPenetrationCategory`|    字串|    false| 定義這個屬性時，產生的功能是垂直滲透 (VRT) 而非單位。 您可以使用 Vrt 之一移至其上方或下方層級中的其他 VRT 功能。 垂直滲透是 [類別目錄](https://aka.ms/pa-indoor-spacecategories) 名稱。 如果定義了此屬性，則 `categoryName` 會以覆寫屬性 `verticalPenetrationCategory` 。 |
|`verticalPenetrationDirection`|    字串|    false    |如果已定義 `verticalPenetrationCategory`，則可選擇定義有效的行進方向。 允許的值為： `lowToHigh` 、 `highToLow` 、 `both` 和 `closed` 。 預設值是 `both`。|
| `nonPublic` | bool | false | 指出單位是否要向外公開。 |
| `isRoutable` | bool | false | 當這個屬性設定為時 `false` ，您無法移至或透過單位。 預設值是 `true`。 |
| `isOpenArea` | bool | false | 允許流覽代理程式進入單位，而不需要將開啟連接至單位。 依預設，此值會設定為， `true` 而不含任何下傳的單位，以及具有下傳 `false` 的單位。 在 `isOpenArea` `false` 沒有任何開啟結果的單位上手動將設定為，會產生警告。 這是因為所產生的單元將無法由流覽代理程式來觸達。|

### `zoneProperties`

`zoneProperties` 物件包含區域屬性的 JSON 陣列。

| 屬性  | 類型 | 必要 | 描述 |
|-----------|------|----------|-------------|
|zoneName        |字串    |true    |要與 `zoneProperty` 記錄相關聯的區域名稱。 只有在區域的 `zoneLabel` 圖層中找到符合 `zoneName` 的標籤時，此記錄才有效。  |
|categoryName|    字串|    false    |類別目錄名稱。 如需類別的完整清單，請參閱[類別](https://aka.ms/pa-indoor-spacecategories)。 |
|zoneNameAlt|    字串|    false    |區域的替代名稱。  |
|zoneNameSubtitle|    字串 |    false    |區域的子標題。 |
|zoneSetId|    字串 |    false    | 設定識別碼以建立多個區域之間的關聯性，讓它們可以查詢或選取為群組。 例如，跨多個層級的區域。 |

### <a name="sample-drawing-package-manifest"></a>繪圖套件資訊清單範例

以下是範例繪圖套件的範例資訊清單檔。 若要下載整個封裝，請參閱 [範例繪圖套件](https://github.com/Azure-Samples/am-creator-indoor-data-examples)。

#### <a name="manifest-file"></a>資訊清單檔

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
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

當您的繪圖套件符合需求時，您可以使用 [Azure 地圖服務轉換服務](/rest/api/maps/conversion) 將封裝轉換成地圖資料集。 然後，您可以使用「室內地圖」模組，使用資料集來產生室內地圖。

> [!div class="nextstepaction"]
>[室內地圖的建立工具](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [教學課程：建立建立工具的室內地圖](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [室內地圖動態樣式](indoor-map-dynamic-styling.md)