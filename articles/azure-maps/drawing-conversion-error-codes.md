---
title: Azure 地圖服務繪製轉換錯誤和警告
description: 了解您在使用 Azure 地圖轉換服務時可能會遇到的轉換錯誤和警告。 請參閱相關建議和範例，以了解如何解決錯誤和警告。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: d79c42f3bdf84efcdf2187741ac270087be05272
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681985"
---
# <a name="drawing-conversion-errors-and-warnings"></a>繪製轉換錯誤和警告

[Azure 地圖轉換服務](https://docs.microsoft.com/rest/api/maps/conversion)可讓您將上傳的繪圖套件轉換成地圖資料。 繪圖套件必須符合[繪圖套件需求](drawing-requirements.md)。 如果不符合一或多項需求，轉換服務就會傳回錯誤或警告。 本文列出轉換錯誤和警告的代碼，並提供解決這些問題的建議。 此外也會提供一些可能導致轉換服務傳回這些代碼的繪製範例。

出現任何轉換警告時，轉換服務仍會成功。 但建議您檢閱所有警告並加以解決。 出現警告時，表示已忽略或自動修正轉換的一部分。 若無法解決警告，可能會導致後續的程序發生錯誤。

## <a name="general-warnings"></a>一般警告

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>*geometryWarning 的描述*

當繪圖包含無效的實體時，就會發生 **geometryWarning**。 無效的實體是不符合幾何條件約束的實體。 舉例來說，僅支援封閉幾何圖形的圖層中包含的自我相交多邊形或非封閉聚合線條，都是無效實體。

轉換服務無法從無效的實體建立地圖功能，而會加以忽略。

#### <a name="examples-for-geometrywarning"></a>*geometryWarning 的範例*

* 以下二圖顯示自我相交多邊形的範例。

     ![自我相交的多邊形範例](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![自我相交的多邊形範例](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* 下圖顯示非封閉聚合線條。 假設圖層僅支援封閉幾何圖形。

    ![非封閉聚合線條範例](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*如何修正 geometryWarning*

檢查每個實體的 **geometryWarning**，以確認實體符合幾何條件約束。

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*unexpectedGeometryInLayer 的描述*

當繪圖包含與指定圖層預期的幾何類型不相容的幾何圖形時，就會發生 **unexpectedGeometryInLayer** 警告。 轉換服務傳回 **unexpectedGeometryInLayer** 警告時，將會忽略該幾何圖形。

#### <a name="example-for-unexpectedgeometryinlayer"></a>*unexpectedGeometryInLayer 的範例*

下圖顯示非封閉聚合線條。 假設圖層僅支援封閉幾何圖形。

![非封閉聚合線條範例](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*如何修正 unexpectedGeometryInLayer*

檢查每個 **unexpectedGeometryInLayer** 警告，並將不相容的幾何圖形移至相容的圖層。 如果該圖形與任何其他圖層都不相容，則應加以移除。

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*unsupportedFeatureRepresentation 的描述*

當繪圖包含不支援的實體類型時，就會發生 **unsupportedFeatureRepresentation** 警告。

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*unsupportedFeatureRepresentation 的範例*

下圖顯示在標籤圖層上以不支援的實體類型作為多行文字物件的情形。
  
![標籤圖層上的多行文字物件範例](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*如何修正 unsupportedFeatureRepresentation*

請確定您的 DWG 檔案僅包含支援的實體類型。 [繪圖套件需求一文中的繪圖檔案需求小節](drawing-requirements.md#drawing-package-requirements)列出了支援的類型。

### <a name="automaticrepairperformed"></a>**automaticRepairPerformed**

#### <a name="description-for-automaticrepairperformed"></a>*automaticRepairPerformed 的描述*

當轉換服務自動修復無效的幾何圖形時，就會發生 **automaticRepairPerformed** 警告。

#### <a name="examples-for-automaticrepairperformed"></a>*automaticRepairPerformed 的範例*

* 下圖顯示轉換服務如何將自我相交多邊形修復成有效的幾何圖形。

    ![已修復的自我相交多邊形範例](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* 下圖顯示轉換服務如何將非封閉聚合線條的第一個和最後一個頂點貼齊，以建立封閉聚合線條；其中，第一個和最後一個頂點的距離不到 1 公釐。  

    ![已貼齊的聚合線條範例](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* 下圖顯示如何轉換服務在僅支援封閉聚合線條的圖層中修復多個非封閉聚合線條。 為了避免捨棄非封閉多線條，服務會將其結合成單一封閉聚合線條。

    ![結合成單一封閉聚合線條的非封閉聚合線條範例](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*如何修正 automaticRepairPerformed*

若要修正 **automaticRepairPerformed** 警告，請採取下列動作：

1. 檢查每個警告的幾何圖形和特定警告文字。
2. 確認自動修復是否正確。
3. 如果修復正確，請繼續操作。 否則，請移至設計檔案並手動解決警告。

>[!TIP]
>若要在未來隱藏警告，請對原始繪圖進行變更，使原始繪圖符合已修復的繪圖。

## <a name="manifest-warnings"></a>資訊清單警告

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>*redundantAttribution 的描述*

當資訊清單包含多餘或衝突的物件屬性時，就會發生 **redundantAttribution** 警告。

#### <a name="examples-for-redundantattribution"></a>*redundantAttribution 的範例*

* 下列 JSON 程式碼片段包含兩個或更多具有相同 `name` 的 `unitProperties` 物件。

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* 在下列 JSON 程式碼片段中，有兩個或更多 `zoneProperties` 物件具有相同的 `name`。

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*如何修正 redundantAttribution*

若要修正 **redundantAttribution* 警告，請移除多餘或衝突的物件屬性。

### <a name="manifestwarning"></a>**manifestWarning**

#### <a name="description-for-manifestwarning"></a>*manifestWarning 的描述*

當資訊清單包含在轉換期間未使用的 unitProperties 或 zoneProperties 物件時，就會發生 **manifestWarning**。

#### <a name="examples-for-manifestwarning"></a>*manifestWarning 的範例*

* 資訊清單包含一個 `unitProperties` 物件，其 `unitName` 在 `unitLabel` 圖層中沒有相符的標籤。

* 資訊清單包含一個 `zoneProperties` 物件，其 `zoneName` 在 `zoneLabel` 圖層中沒有相符的標籤。

#### <a name="how-to-fix-manifestwarning"></a>*如何修正 manifestWarning*

若要修正 **manifestWarning**，請從資訊清單中移除未使用的 `unitProperties` 或 `zoneProperties` 物件，或將單元/區域標籤新增至繪圖，以便在轉換期間使用屬性物件。

## <a name="wall-warnings"></a>牆警告

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*wallOutsideLevel 的描述*

當繪圖包含的牆幾何圖形超出層級外框的範圍時，就會發生 **wallOutsideLevel** 警告。

#### <a name="example-for-walloutsidelevel"></a>*wallOutsideLevel 的範例*

* 下圖顯示在黃色層級界限以外的內牆 (以紅色表示)。

    ![層級界限以外的內牆範例](./media/drawing-conversion-error-codes/wall-outside-level.png)

* 下圖顯示在黃色層級界限以外的外牆 (以紅色表示)。

    ![層級界限以外的外牆範例](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*如何修正 wallOutsideLevel*

若要修正 **wallOutsideLevel** 警告，請擴展層級幾何圖形以包含所有牆。 或者，修改牆邊界，使其容納於層級界限內。

## <a name="unit-warnings"></a>單元警告

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*unitOutsideLevel 的描述*

當繪圖包含的單元幾何圖形超出層級外框的範圍時，就會發生 **unitOutsideLevel** 警告。

#### <a name="example-for-unitoutsidelevel"></a>*unitOutsideLevel 的範例*

 在下圖中，單元幾何圖形 (以紅色表示) 超出黃色層級界限的範圍。

 ![超出層級界限的單元範例](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*如何修正 unitOutsideLevel*

若要修正 **unitOutsideLevel** 警告，請擴展層級界限以包含所有單元。 或者，修改單元幾何圖形，使其容納於層級界限內。

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*partiallyOverlappingUnit 的描述*

當繪圖包含與另一個單元幾何圖形部分重疊的單元幾何圖形時，就會發生 **partiallyOverlappingUnit** 警告。 轉換服務會捨棄重疊的單元。

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*範例案例 partiallyOverlappingUnit*

在下圖中，重疊的單元會以紅色醒目提示。 `UNIT110` 和 `HALLWAY` 會被捨棄。

![重疊單元的範例](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*如何修正 partiallyOverlappingUnit*

若要修正 **partiallyOverlappingUnit** 警告，請重繪每個部分重疊的單元，使其不會與任何其他單元重疊。

## <a name="door-warnings"></a>門警告

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*doorOutsideLevel 的描述*

當繪圖包含的門幾何圖形超出層級幾何圖形的範圍時，就會發生 **doorOutsideLevel** 警告。

#### <a name="example-for-dooroutsidelevel"></a>*doorOutsideLevel 的範例*

在下圖中，以紅色醒目提示的門幾何圖形與黃色的層級界限重疊。

![與層級界限重疊的門範例](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*如何修正 doorOutsideLevel*

若要修正 **doorOutsideLevel** 警告，請重繪您的門幾何圖形，使其位於層級界限內。

## <a name="zone-warnings"></a>區域警告

### <a name="zonewarning"></a>**zoneWarning**

#### <a name="description-for-zonewarning"></a>*zoneWarning 的描述*

當區域未包含標籤時，就會發生 **zoneWarning**。 轉換服務會捨棄未加上標籤的區域。

#### <a name="example-for-zonewarning"></a>*zoneWarning 的範例*

下圖顯示未包含標籤的區域。

![未包含標籤的區域範例](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*如何修正 zoneWarning*

若要修正 **zoneWarning**，請確認每個區域都有單一標籤。

## <a name="label-warnings"></a>標籤警告

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>*labelWarning 的描述*

當繪圖包含不明確或衝突的標籤功能時，就會發生 **labelWarning**。

**labelWarning** 可能由於下列一或多個原因而發生：

* 單元標籤不在任何單元中。
* 區域標籤不在任何區域中。
* 區域標籤位於兩個或更多區域內。

#### <a name="example-for-labelwarning"></a>*labelWarning 的範例*

下圖顯示位於兩個區域內的標籤。

![位於兩個區域內的標籤範例 ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*如何修正 labelWarning*

若要修正 **labelWarning**，請確定：

* 所有單元標籤都位於單元內。
* 所有區域標籤都位於區域內。
* 所有區域標籤都僅位於單一區域中。

## <a name="drawing-package-errors"></a>繪圖套件錯誤

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*invalidArchiveFormat 的描述*

當繪圖套件的封存格式無效 (如 GZIP 或 7-Zip) 時，就會發生 **invalidArchiveFormat** 錯誤。 目前僅支援 ZIP 封存格式。

如果 ZIP 封存是空的，也會發生 **invalidArchiveFormat** 錯誤。

#### <a name="how-to-fix-invalidarchiveformat"></a>*如何修正 invalidArchiveFormat*

若要修正 **invalidArchiveFormat** 錯誤，請確認：

* 封存檔案名稱的結尾是 _.zip_。
* ZIP 封存包含資料。
* 您可以開啟 ZIP 封存。

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>*invalidUserData 的描述*

當轉換服務無法從儲存體讀取使用者資料物件時，就會發生 **invalidUserData** 錯誤。

#### <a name="example-scenario-for-invaliduserdata"></a>*invalidUserData 的範例案例*

您嘗試上傳的繪圖套件具有不正確的 `udid` 參數。

#### <a name="how-to-fix-invaliduserdata"></a>*如何修正 invalidUserData*

若要修正 **invalidUserData** 錯誤，請確認：

* 您已為上傳的套件提供了正確的 `udid`。
* 您已為用來上傳繪圖套件的 Azure 地圖服務帳戶啟用 Azure 地圖服務建立工具。
* 對轉換服務的 API 要求包含您用來上傳繪圖套件的 Azure 地圖服務帳戶的訂用帳戶金鑰。

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*dwgError 的描述*

當繪圖套件包含上傳的 ZIP 封存中一或多個 DWG 檔案的問題時，就會發生 **dwgError**。

當繪圖套件包含因無效或已損毀而無法開啟的 DWG 檔案時，就會發生 **dwgError**。

* DWG 檔案不是有效的 AutoCAD DWG 檔案格式繪圖。
* DWG 檔案已損毀。
* DWG 檔案列在 _manifest.json_ 檔案中，但 ZIP 封存未包含該檔案。

#### <a name="how-to-fix-dwgerror"></a>*如何修正 dwgError*

若要修正 **dwgError**，請檢查您的 _manifest.json_ 檔案以確認：

* ZIP 封存中的所有 DWG 檔案都是有效的 AutoCAD DWG 格式繪圖。請在 AutoCAD 中開啟每個檔案。 移除或修正所有無效的繪圖。
* _manifest.json_ 中的 DWG 檔案清單與 ZIP 封存中的 DWG 檔案相符。

## <a name="manifest-errors"></a>資訊清單錯誤

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>invalidJsonFormat 的描述

無法讀取 _manifest.json_ 檔案時，就會發生 **invalidJsonFormat** 錯誤。

_manifest. json_file 因 JSON 格式或語法錯誤而無法讀取。 若要深入了解 JSON 格式和語法，請參閱 [JavaScript 物件標記法 (JSON) 資料交換格式](https://tools.ietf.org/html/rfc7159)

#### <a name="how-to-fix-invalidjsonformat"></a>*如何修正 invalidJsonFormat*

若要修正 **invalidJsonFormat** 錯誤，請使用 JSON linter 來偵測並解決任何 JSON 錯誤。

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*missingRequiredField 的描述*

當 _manifest.json_ 檔案遺漏必要的資料時，就會發生 **missingRequiredField** 錯誤。

#### <a name="how-to-fix-missingrequiredfield"></a>*如何修正 missingRequiredField*

若要修正 **missingRequiredField** 錯誤，請確認資訊清單包含所有必要的屬性。 如需必要資訊清單物件的完整清單，請參閱[繪圖套件需求中的資訊清單小節](drawing-requirements.md#manifest-file-requirements)  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>*missingManifest 的描述*

當 ZIP 封存中遺漏 _manifest.json_ 檔案時，就會發生 **missingManifest** 錯誤。

**missingManifest** 錯誤可能由於下列一或多個原因而發生：

* The _manifest.json_ 檔案的拼字錯誤。
* 遺漏 _manifest.json_。
* _manifest.json_ 不在 ZIP 封存的根目錄中。

#### <a name="how-to-fix-missingmanifest"></a>*如何修正 missingManifest*

若要修正 **missingManifest** 錯誤，請確認封存在 ZIP 封存的根層級中有名為 _manifest.json_ 的檔案。

### <a name="conflict"></a>**conflict**

#### <a name="description-for-conflict"></a>*conflict 的描述*

當 _manifest.json_ 檔案包含衝突的資訊時，就會發生 **conflict** 錯誤。

#### <a name="example-scenario-for-conflict"></a>*conflict 的範例案例*

如果以相同的層級序數定義了多個層級，轉換服務就會傳回 **conflict** 錯誤。 下列 JSON 程式碼片段顯示兩個以相同序數定義的層級。

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*如何修正 conflict*

若要修正 **conflict** 錯誤，請檢查您 _manifest.json_ 並移除任何衝突的資訊。

### <a name="invalidgeoreference"></a>**invalidGeoreference**

#### <a name="description-for-invalidgeoreference"></a>*invalidGeoreference 的描述*

當 _manifest.json_ 檔案包含無效的地理座標參考時，就會發生 **invalidGeoreference** 錯誤。

**invalidGeoreference** 錯誤可能由於下列一或多個原因而發生：

* 使用者對超出範圍的緯度或經度值進行地理座標參考。
* 使用者對超出範圍的旋轉值進行地理座標參考。

#### <a name="example-scenario-for-invalidgeoreference"></a>*invalidGeoreference 的範例案例*

在下列 JSON 程式碼片段中，緯度高於上限。

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*如何修正 invalidGeoreference*

若要修正 **invalidGeoreference** 錯誤，請確認地理座標參考值位於範圍內。

>[!IMPORTANT]
>在 GeoJSON 中，座標依序為經度和緯度。 若未使用正確的順序，您可能會非預期地參照超出範圍的緯度或經度值。

## <a name="wall-errors"></a>牆錯誤

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*wallError 的描述*

如果繪圖在嘗試建立牆功能時包含錯誤，就會發生 **wallError**。

#### <a name="example-scenario-for-wallerror"></a>*wallError 的範例案例*

下圖顯示未與任何單元重疊的牆功能。

![未與任何單元重疊的牆功能範例](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*如何修正 wallError*

若要修正 **wallError** 錯誤，請重繪牆而使其與至少一個單元重疊。 或者，建立一個與牆重疊的新單元。

## <a name="vertical-penetration-errors"></a>垂直穿透錯誤

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>*verticalPenetrationError 的描述*

當繪圖包含不明確的垂直穿透功能時，就會發生 **verticalPenetrationError**。

**verticalPenetrationError** 錯誤可能由於下列一或多個原因而發生：

* 繪圖包含垂直穿透區域，且在其上或其下的任何層級上都沒有重疊的垂直穿透區域。
* 繪圖套件包含的某個層級具有兩個或更多垂直穿透功能，且都與該層級的上一層或下一層的單一垂直穿透功能重疊。

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*verticalPenetrationError 的範例案例*

下圖顯示一個垂直穿透區域，且在其上或其下的層級上並沒有重疊的垂直穿透區域。

![垂直穿透範例 1](./media/drawing-conversion-error-codes/vrt-2.png)

下圖顯示與相鄰層級上的多個垂直穿透區域重疊的垂直穿透區域。

![垂直穿透範例 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>如何修正 verticalPenetrationError

若要修正 **verticalPenetrationError** 錯誤，請參閱[繪圖套件需求](drawing-requirements.md)一文中有關於如何使用垂直穿透功能的部分。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何使用 Azure 地圖服務繪製錯誤視覺化檢視](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [室內位置對應的建立工具](creator-indoor-maps.md)