---
title: Azure 地圖 Web SDK 中的數據驅動樣式運算式 |微軟 Azure 地圖
description: 在本文中,您將瞭解如何在 Microsoft Azure 地圖 Web SDK 中使用數據驅動的樣式運算式。
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d6009a655adcc26ebef31588eff2332a05f3a001
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804719"
---
# <a name="data-driven-style-expressions-web-sdk"></a>資料驅動的樣式表示式(Web SDK)

表達式使您能夠將業務邏輯應用於樣式選項,這些選項觀察數據源中每個形狀中定義的屬性。 表達式可以篩選數據源或圖層中的數據。 表達式可能由條件邏輯組成,如if語句。 並且,它們可用於使用字串運算元、邏輯運算元和數學運算符來操作數據。

數據驅動的樣式減少了圍繞樣式實現業務邏輯所需的代碼量。 當與圖層一起使用時,表達式將在單獨的線程的渲染時計算。 與在 UI 線程上評估業務邏輯相比,此功能提供了更高的性能。

此視頻概述了 Azure 地圖 Web SDK 中的數據驅動的樣式。

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

表達式表示為 JSON 陣組。 陣列中表示式的第一個元素是指定運算式運算名稱的字串。 例如,"+"或"案例"。 下一個元素(如果有)是表達式的參數。 每個參數都是文本值(字串、數位、布爾或 )或其他`null`運算式陣列。 以下偽代碼定義表達式的基本結構。 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure 映射 Web SDK 支援多種類型的運算式。 表達式可以自行使用,也可以與其他表達式結合使用。

| 運算式類型 | 描述 |
|---------------------|-------------|
| [彙總運算式](#aggregate-expression) | 定義透過一組資料處理的運算的運算式,`clusterProperties`可以與`DataSource`選項一起使用。 |
| [布林運算式](#boolean-expressions) | 布爾運算式提供了一組用於評估布爾比較的布爾運算符表達式。 |
| [顏色運算式](#color-expressions) | 顏色運算式使創建和操作顏色值變得更加容易。 |
| [條件運算式](#conditional-expressions) | 條件運算式提供的邏輯操作類似於if語句。 |
| [資料運算式](#data-expressions) | 提供對要素中屬性數據的訪問。 |
| [插值和步進表示式](#interpolate-and-step-expressions) | 插值和步進表達式可用於計算沿插值曲線或步進函數的值。 |
| [特定於層次的運算式](#layer-specific-expressions) | 僅適用於單個圖層的特殊運算式。 |
| [數學運算式](#math-expressions) | 提供數學運算符,用於在運算式框架中執行數據驅動的計算。 |
| [字串運算子表示式](#string-operator-expressions) | 字串運算符表示式對字串執行轉換操作,例如串聯和轉換大小寫。 |
| [類型運算式](#type-expressions) | 類型運算式提供用於測試和轉換不同資料類型(如字串、數位和布林值)的工具。 |
| [變數繫結表示式](#variable-binding-expressions) | 變數綁定表示式將計算結果儲存在變數中,並在表達式的其他位置多次引用,而無需重新計算存儲的值。 |
| [調整式運算式](#zoom-expression) | 在渲染時檢索地圖的當前縮放級別。 |

本文檔中的所有範例都使用以下功能來演示使用不同類型的表達式的不同方式。 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>資料運算式

數據表達式提供對要素中屬性數據的訪問。 

| 運算是 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['at', number, array]` | 物件 (object) | 從陣列中檢索項。 |
| `['geometry-type']` | 字串 | 獲取要素的幾何類型:點、多點、線串、多行串、多邊形、多面形。 |
| `['get', string]` | value | 從當前要素的屬性獲取屬性值。 如果請求的屬性丟失,則傳回 null。 |
| `['get', string, object]` | value | 從提供的物件的屬性獲取屬性值。 如果請求的屬性丟失,則傳回 null。 |
| `['has', string]` | boolean | 確定要素的屬性是否具有指定的屬性。 |
| `['has', string, object]` | boolean | 確定物件的屬性是否具有指定的屬性。 |
| `['id']` | value | 如果要素有該功能的 ID,則獲取該功能的 ID。 |
| `['length', string | array]` | number | 獲取字串或陣列的長度。 |
| `['in', boolean | string | number, array]` | boolean | 確定陣列中是否存在項 |
| `['in', substring, string]` | boolean | 確定子字串是否存在於字串中 |

**範例**

可以使用`get`運算式直接在表達式中訪問要素的屬性。 此示例使用要素的"區域顏色"值指定氣泡圖層的顏色屬性。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

如果所有點要素都具有該`zoneColor`屬性,則上述示例將工作正常。 如果他們不這樣做,顏色可能會回落到"黑色"。 要修改回退顏色,請使用`case`表達式與`has`運算式結合使用,以檢查該屬性是否存在。 如果屬性不存在,則返回回退顏色。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

預設情況下,氣泡和符號圖層將呈現數據源中所有形狀的座標。 此行為可以突出顯示多邊形或線的頂點。 圖層`filter`的選項可用於通過使用布爾運算式中的`['geometry-type']`運算式來限制其呈現的要素的幾何類型。 下面的示例限制氣泡圖層,以便僅`Point`呈現要素。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

以下示例允許呈現和`Point``MultiPoint`特徵。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

同樣,多邊形的輪廓將在線圖層中呈現。 要在行圖層中禁用此行為,請添加只允許`LineString`和`MultiLineString`功能的篩選器。  

## <a name="math-expressions"></a>數學運算式

數學運算式提供數學運算符,用於在運算式框架中執行數據驅動的計算。

| 運算是 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | 計算指定數位的總和。 |
| `['-', number]` | number | 按指定數位減去 0。 |
| `['-', number, number]` | number | 將第一個數字減去第二個數位。 |
| `['*', number, number, …]` | number | 將指定的數位相乘在一起。 |
| `['/', number, number]` | number | 將第一個數位除以第二個數位。 |
| `['%', number, number]` | number | 將第一個數位除以第二個數位時計算餘數。 |
| `['^', number, number]` | number | 計算第一個值的值,以第二個數位的功率提升。 |
| `['abs', number]` | number | 計算指定之數字的絕對值。 |
| `['acos', number]` | number | 計算指定數位的弧形。 |
| `['asin', number]` | number | 計算指定數位的弧線。 |
| `['atan', number]` | number | 計算指定數位的弧形。 |
| `['ceil', number]` | number | 將數位向上舍入到下一個整數。 |
| `['cos', number]` | number | 計算指定數位的cos。 |
| `['e']` | number | 傳回數學常`e`數 。 |
| `['floor', number]` | number | 將數位向下舍入到以前的整數。 |
| `['ln', number]` | number | 計算指定數位的自然對數。 |
| `['ln2']` | number | 傳回數學常`ln(2)`數 。 |
| `['log10', number]` | number | 計算指定數位的基數 10 對數。 |
| `['log2', number]` | number | 計算指定數位的基數-二對數。 |
| `['max', number, number, …]` | number | 計算指定數位集中的最大數位。 |
| `['min', number, number, …]` | number | 計算指定數位集中的最小數位。 |
| `['pi']` | number | 傳回數學常`PI`數 。 |
| `['round', number]` | number | 將數位舍入到最接近的整數。 中間值從零四捨五入。 例如,`['round', -1.5]`計算為 -2。 |
| `['sin', number]` | number | 計算指定數位的子值。 |
| `['sqrt', number]` | number | 計算指定之數字的平方根。 |
| `['tan', number]` | number | 計算指定數位的切線。 |

## <a name="aggregate-expression"></a>彙總運算式

聚合表示式定義透過一組資料處理的計算,`clusterProperties`並可與`DataSource`選項一起使用。 這些表達式的輸出必須是數位或布林。 

聚合表示式採用三個值:運算元值和初始值,以及用於從數據中的每個要素檢索屬性以應用聚合操作的運算式。 此表示式具有以下格式:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- 運算符:運算式函數,然後應用於針對群集`mapExpression`中每個點計算的所有值。 支援的運算子: 
    - 對於數位: `+` `*` `max`, , ,`min`
    - 對於布爾斯: `all`,`any`
- 初始值:聚合第一個計算值的初始值。
- mapExpression:應用於數據集中每個點的運算式。

**範例**

如果數據集中的所有要素都有一個`revenue`屬性,這是一個數位。 然後,可以計算從數據集創建的群集中所有點的總收入。 此計算使用以下整合式運算時完成:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>布林運算式

布爾運算式提供了一組用於評估布爾比較的布爾運算符表達式。

比較值時,將嚴格鍵入比較。 不同類型的值始終被視為不相等。 已知類型在分析時不同的情況被視為無效,並將生成分析錯誤。 

| 運算是 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | 邏輯否定。 如果`true`輸入為`false`,則`false`傳回 ,`true`如果輸入為 。 |
| `['!= ', value, value]` | boolean | 如果`true`輸入值不相等,則返回,`false`否則。 |
| `['<', value, value]` | boolean | 如果`true`第一個輸入嚴格小於第二個輸入`false`, 則返回,否則。 參數必須是字串或兩個數位。 |
| `['<=', value, value]` | boolean | 如果`true`第一個輸入小於或等於第二個輸入,`false`則返回,否則。 參數必須是字串或兩個數位。 |
| `['==', value, value]` | boolean | 如果`true`輸入值相等,則返回,`false`否則。 參數必須是字串或兩個數位。 |
| `['>', value, value]` | boolean | 如果`true`第一個輸入嚴格大於第二個輸入`false`, 則返回,否則。 參數必須是字串或兩個數位。 |
| `['>=' value, value]` | boolean | 如果`true`第一個輸入大於或等於第二個輸入,`false`則返回,否則。 參數必須是字串或兩個數位。 |
| `['all', boolean, boolean, …]` | boolean | 如果`true`所有輸入`true`都是`false`,則返回,否則。 |
| `['any', boolean, boolean, …]` | boolean | 如果`true`任何輸入`true`為 ,`false`則返回 ,否則。 |

## <a name="conditional-expressions"></a>條件運算式

條件運算式提供的邏輯操作類似於if語句。

以下運算式對輸入數據執行條件邏輯操作。 例如,`case`運算式提供「if/then/else」邏輯`match`, 而表達式類似於「開關語句」。 

### <a name="case-expression"></a>案例運算式

運算`case`式 是提供「if/然後/else」邏輯的條件表達式的類型。 這種類型的運算式步步通過布林條件清單。 它將第一個布爾條件的輸出值返回以進行 true。

以下偽程式定義表示式的結構`case`。 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**範例**

下面的示例步步遍了不同的布爾條件,直到它找到一個計算到`true`的布爾條件,然後返回該關聯值。 如果沒有布爾條件計算到`true`,將返回回退值。 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>符合運算式

運算`match`式 是提供開關語句(如邏輯)的條件表達式的類型。 輸入可以是任何表達式,例如`['get', 'entityType']`返回字串或數位的運算式。 每個標籤必須是單個文本值或文本值陣列,其值必須是所有字串或所有數位。 如果陣列中的任何值匹配,則輸入匹配。 每個標籤必須是唯一的。 如果輸入類型與標籤的類型不匹配,則結果將是回退值。

以下偽程式定義表示式的結構`match`。 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**範例**

下面的範例查看氣泡圖層中`entityType`搜索匹配項的 Point 要素的屬性。 如果找到匹配項,則返回該指定值或返回回退值。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

下面的示例使用陣列出一組標籤,這些標籤應全部返回相同的值。 此方法比單獨列出每個標籤更有效。 在這種情況下,`entityType`如果屬性是「餐廳」或「grocery_store」,則返回顏色「紅色」。。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

下面的範例使用匹配表達式執行"在陣列"或"數位包含"類型篩選器。 在這種情況下,表達式篩選具有允許 ID 清單中的 ID 值的數據。 將運算式與篩選器一起使用時,結果需要為布爾值。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>凝聚運算式

表達式`coalesce`步步遍一組表達式,直到獲得第一個非空值並返回該值。 

以下偽程式定義表示式的結構`coalesce`。 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**範例**

下面的範例使用`coalesce`運算式設定符號圖`textField`層 的選項。 如果`title`屬性從要素中丟失或`null`設置為 ,則運算式將`subtitle`嘗試查找 該屬性(如果`null`缺少該屬性或 ,則該屬性將回退回空字串)。 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

下面的範例使用`coalesce`運算式從指定影像名稱清單中檢索地圖子畫面中可用的第一個可用圖像圖示。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>類型運算式

類型運算式提供用於測試和轉換不同資料類型(如字串、數位和布林值)的工具。

| 運算是 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | 陣列\|物件 | 返回文本陣列或物件值。 使用此運算式可防止將陣列或物件計算為運算式。 當陣列或物件需要由表達式返回時,這是必需的。 |
| `['image', string]` | 字串 | 檢查是否將指定的圖像 ID 載入到地圖影像精靈中。 如果是,則返回 ID,否則返回 null。 |
| `['to-boolean', value]` | boolean | 將輸入值轉換為布林。 `false`結果是當輸入為`0`空字串 時, `false`、 `null` `NaN` 、 、否則其`true`。 |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | 將輸入值轉換為顏色。 如果提供了多個值,則按順序計算每個值,直到獲得第一個成功轉換。 如果無法轉換任何輸入,則表達式是錯誤。 |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | 如果可能,將輸入值轉換為數位。 如果輸入為`null``false`或 ,則結果為 0。 如果輸入為`true`,則結果為 1。 如果輸入是字串,則使用 ECMAScript 語言規範的[ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type)字串函數將其轉換為數位。 如果提供了多個值,則按順序計算每個值,直到獲得第一個成功轉換。 如果無法轉換任何輸入,則表達式是錯誤。 |
| `['to-string', value]` | 字串 | 將輸入值轉換為字串。 如果輸入為`null`,則結果`""`為 。 如果輸入是布林,則結果`"true"`是`"false"`或 。 如果輸入是數位,則使用 ECMAScript 語言規範的[ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type)編號函數將其轉換為字串。 如果輸入是一種顏色,則將其轉換為 CSS RGBA 顏色`"rgba(r,g,b,a)"`字串 。 否則,使用 ECMAScript 語言規範的[JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify)函數將輸入轉換為字串。 |
| `['typeof', value]` | 字串 | 傳回描述給定值類型的字串。 |

> [!TIP]
> 如果瀏覽器控制台中出現類似於`Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].`的錯誤消息,則意味著代碼中有一個表達式,該表達式具有第一個值沒有字串的陣列。 如果希望表達式返回陣列,則用`literal`表達式換行陣組。 下面的示例設置符號圖層的`offset`圖示選項,該圖示必須是包含兩個數位的陣列,通過使用表達式根據點`match``entityType`要素的屬性的值在兩個偏移值之間進行選擇。
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>顏色運算式

顏色運算式使創建和操作顏色值變得更加容易。

| 運算是 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | 從*紅色*、*綠色*和*藍色*分量創建顏色值,這些分量必須`0``255`在和之間範圍`1`,以及的Alpha分量。 如果任何元件的範圍不大,則表達式是錯誤。 |
| `['rgba', number, number, number, number]` | color | 從*紅色*、*綠色*、*藍色*分量創建顏色值,這些`0`分量`255`必須在和之間`0`範圍`1`,並在和的範圍內創建 Alpha 分量。 如果任何元件的範圍不大,則表達式是錯誤。 |
| `['to-rgba']` | \[數位、數位、數位、數位\] | 按該順序返回包含輸入顏色*的紅色*、*綠色*、*藍色*和*Alpha*分量的四元素陣列。 |

**範例**

下面的範例建立 RGB 顏色值,*red*該值的`255`紅色 值為,*綠色*和`2.5``temperature`*藍色*值透過乘以 屬性的值來計算。 隨著溫度的變化,顏色將變為不同的*紅色*色調。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>字串運算子表示式

字串運算符表示式對字串執行轉換操作,例如串聯和轉換大小寫。 

| 運算是 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `['concat', string, string, …]` | 字串 | 將多個字串串聯在一起。 每個值都必須是一個字串。 如果需要,`to-string`請使用類型表示式將其他值類型轉換為字串。 |
| `['downcase', string]` | 字串 | 將指定字串轉換為小寫。 |
| `['upcase', string]` | 字串 | 將指定字串轉換為大寫。 |

**範例**

下面的示例將`temperature`點要素的屬性轉換為字串,然後將"+F"串聯到它的末尾。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

上面的運算式在地圖上渲染一個圖釘,上面覆蓋著文本"64°F",如下圖所示。

<center>

![字串運算子表示式範例](media/how-to-expressions/string-operator-expression.png)</center>

## <a name="interpolate-and-step-expressions"></a>插值和步進表示式

插值和步進表達式可用於計算沿插值曲線或步進函數的值。 這些表示式採用傳回數值作為輸入的運算式,`['get',  'temperature']`例如 。 根據輸入和輸出值對計算輸入值,以確定最適合插值曲線或步進函數的值。 輸出值稱為"停止」。。 每個停靠站的輸入值必須為數位,並且按升冪排列。 輸出值必須是數位、數位陣元或顏色。

### <a name="interpolate-expression"></a>插值運算式

運算式`interpolate`可用於透過在停止值之間插值來計算連續、平滑的值集。 返回`interpolate`顏色值的運算式將生成顏色漸變,其中從中選擇結果值。

有三種型態的插值方法可`interpolate`用於 運算式:
 
* `['linear']`- 插值在兩個停止之間線性進行。
* `['exponential', base]`- 插值在停靠點之間呈指數級。 該`base`值控制輸出增加的速率。 值越高,輸出會更多地向範圍的高端增長。 接近`base`1 的值會產生線性增長較多的輸出。
* `['cubic-bezier', x1, y1, x2, y2]`- 使用由給定控制點定義的[立方貝氏曲線](https://developer.mozilla.org/docs/Web/CSS/timing-function)進行插值。

下面是這些不同類型的插值示例。 

| 線性  | 指數 | 三次方貝茲 |
|---------|-------------|--------------|
| ![線性插值圖](media/how-to-expressions/linear-interpolation.png) | ![指數插值圖](media/how-to-expressions/exponential-interpolation.png) | ![立方貝氏斯圖](media/how-to-expressions/bezier-curve-interpolation.png) |

以下偽程式定義表示式的結構`interpolate`。 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**範例**

下面的範例使用`linear interpolate`運算式根據點`color``temperature`要素的屬性設置氣泡圖層的屬性。 如果`temperature`值小於 60,則返回"藍色"。 如果介於 60 和小於 70 之間,則黃色將返回。 如果介於 70 和小於 80 之間,則返回"橙色"。 如果為 80 或更高,則返回"紅色"。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

下圖演示了如何為上述表達式選擇顏色。
 
<center>

![插值運算式範例](media/how-to-expressions/interpolate-expression-example.png)</center>

### <a name="step-expression"></a>步驟運算式

運算式`step`可用於透過計算由停靠點定義的[分段常數函數](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)來計算離散的階梯式結果值。 

以下偽程式定義表示式的結構`step`。 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

步長運算式返回在輸入值之前的止損的輸出值,或者如果輸入小於第一個停止,則返回第一個輸入值。 

**範例**

下面的範例使用`step`運算式根據點`color``temperature`要素的屬性設置氣泡圖層的屬性。 如果`temperature`值小於 60,則返回"藍色"。 如果介於 60 和小於 70 之間,則返回"黃色」。。 如果介於 70 和小於 80 之間,則返回"橙色"。 如果為 80 或更高,則返回"紅色"。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

下圖演示了如何為上述表達式選擇顏色。
 
<center>

![步驟表示式範例](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>特定於層次的運算式

僅適用於特定圖層的特殊運算式。

### <a name="heat-map-density-expression"></a>熱圖密度運算式

熱圖密度表示式檢索熱圖層次中每個像素的熱圖密度值,並定義為`['heatmap-density']`。 此值是和`0``1`之間的數位。 它與`interpolation``step`或運算式結合使用,用於定義用於著色熱圖的顏色漸變。 此表達式只能在熱圖圖層[的顏色選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color)中使用。

> [!TIP]
> 索引 0 處的顏色(插值運算式中)或單步顏色的預設顏色定義沒有資料的區域的顏色。 索引 0 處的顏色可用於定義背景顏色。 許多人偏好將此值設定為透明或半透明的黑色。

**範例**

本示例使用襯線插值表達式創建平滑的顏色漸變以渲染熱圖。 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

除了使用平滑漸變對熱圖著色外,還可以使用`step`表達式在一組範圍內指定顏色。 使用`step`表達式對熱圖進行著色,直觀地將密度分解為類似於輪廓或雷達樣式圖的範圍。  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

有關詳細資訊,請參閱[添加熱圖圖層](map-add-heat-map-layer.md)文檔。

### <a name="line-progress-expression"></a>行進度運算式

行進度運算式檢索沿線層次中的漸變線的進度,並定義為`['line-progress']`。 此值是介於 0 和 1 之間的數位。 它與`interpolation``step`或表達式結合使用。 此運算式只能與線圖層的[描邊漸變選項]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient)一起使用。 

> [!NOTE]
> 線`strokeGradient`圖層的選項需要將數據`lineMetrics`來源設定`true`為的選項。

**範例**

此示例使用`['line-progress']`運算式將顏色漸變應用於線條的描邊。

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[檢視即時範例](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>文字欄位格式表示式

文字欄位格式表示式可與符號圖層`textField``textOptions`屬性的選項一起使用,以提供混合文本格式。 此表示式允許指定一組輸入字串和格式選項。 可以為此運算式中的每個輸入字串指定以下選項。

 * `'font-scale'`- 指定字型大小的縮放因數。 如果指定,此值將覆蓋`size`各個字串的屬性。 `textOptions`
 * `'text-font'`- 指定應用於此字串的一個或多個字體系列。 如果指定,此值將覆蓋`font`各個字串的屬性。 `textOptions`
 * `'text-color'`- 指定在渲染時要應用於文字的顏色。 

以下偽代碼定義文本欄位格式表達式的結構。 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**範例**

下面的範例透過添加粗體字型並縮小該功能`title`屬性的字體大小來設置文本欄位的格式。 此示例還會在`subtitle`newline 上添加該功能的屬性,其字體大小縮小為紅色。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

此圖層將呈現點特徵,如下圖所示:
 
<center>

![具有格式化文字欄位](media/how-to-expressions/text-field-format-expression.png)的點要素影像</center>

### <a name="number-format-expression"></a>數位格式運算式

表達式`number-format`只能與符號圖`textField`層 的選項一起使用。 此表示式將提供的號碼轉換為格式化字串。 此表示式包裝 JavaScript 的[編號.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)函數,並支援以下選項集。

 * `locale`- 指定這個選項,用於以與指定語言對齊的方式將數位轉換為字串。 將[BCP 47 語言標記](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation)傳遞到此選項。
 * `currency`- 將數位轉換為表示貨幣的字串。 可能的值是[ISO 4217 貨幣代碼](https://en.wikipedia.org/wiki/ISO_4217),如美元的"美元"、歐元的"歐元"或人民幣的"CNY"。
 * `'min-fraction-digits'`- 指定要包含在數位字串版本中的小數位數的最小數。
 * `'max-fraction-digits'`- 指定要包含在數位字串版本中的十進位的最大數。

以下偽代碼定義文本欄位格式表達式的結構。 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**範例**

下面的範例使用`number-format`運算式修改點要素的屬性在符`revenue`號`textField`圖層 選項中呈現的方式,使其顯示為美元值。

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

此圖層將呈現點特徵,如下圖所示:

<center>

![數位格式表示式範例](media/how-to-expressions/number-format-expression.png)</center>

### <a name="image-expression"></a>影像運算式

圖像運算式可與符號圖層`image`和多`textField`邊 形`fillPattern`圖層 的選項一起使用。 此表示式檢查請求的圖像是否存在樣式,並將返回已解析的圖像名稱或`null`,具體取決於影像當前是否位於樣式中。 此驗證過程是同步的,要求在映射參數中請求映射之前將映射添加到樣式中。

**範例**

下面的範例使用`image`運算式添加與符號圖層中文本的內聯圖示。 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

此層次將在符號圖層中渲染文字欄位,如下圖所示:

<center>

![影像運算式範例](media/how-to-expressions/image-expression.png)</center>

## <a name="zoom-expression"></a>調整式運算式

表示式`zoom`用於在成像時檢索地圖的目前縮放等級,並`['zoom']`定義為 。 此運算式在地圖的最小縮放級別和最大縮放級別範圍之間返回一個數位。 Azure 地圖 Web 和 Android 的互動式地圖控制項支援 25 個縮放等級,編號為 0 到 24。 使用表達式`zoom`允許在更改地圖的縮放級別時動態修改樣式。 表達式`zoom`只能與`interpolate``step`和表達式一起使用。

**範例**

默認情況下,熱貼圖圖層中呈現的數據點的半徑為所有縮放級別的固定圖元半徑。 放大地圖時,數據聚合在一起,熱圖圖層看起來不同。 運算式`zoom`可用於縮放每個縮放級別的半徑,以便每個數據點覆蓋地圖的相同物理區域。 這將使熱圖圖層看起來更靜態且一致。 地圖的每個縮放級別垂直和水平的圖元數是上一個縮放級別的兩倍。 縮放半徑(使其與每個縮放級別加倍)將創建在所有縮放級別上看起來一致的熱圖。 可以使用具有`zoom``base 2 exponential interpolation`表達式的運算式完成,為最小縮放級別設置圖元半徑,並針對最大縮放級別的縮放半徑(如下所`2 * Math.pow(2, minZoom - maxZoom)`示) 計算。

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[檢視即時範例](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>變數繫結表示式

變數綁定表示式將計算結果存儲在變數中。 因此,計算結果可以在表達式的其他地方多次引用。 對於涉及許多計算的表達式,它是一種有用的優化。

| 運算是 | 傳回類型 | 描述 |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"讓我們",<br/>&nbsp;&nbsp;&nbsp;&nbsp;名稱1:字串,<br/>&nbsp;&nbsp;&nbsp;&nbsp;值1:任何,<br/>&nbsp;&nbsp;&nbsp;&nbsp;名稱2:字串,<br/>&nbsp;&nbsp;&nbsp;&nbsp;值2:任何,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;子運算式<br/>\] | | 將一個或多個值儲存為變數,`var`供傳回結果的子運算式中的運算式使用。 |
| `['var', name: string]` | 任意 | 引用使用`let`運算式創建的變數。 |

**範例**

此示例使用一個表達式,該表達式計算收入相對於溫度比,然後使用`case`表達式計算此值上的不同布爾操作。 該`let`表達式用於存儲相對於溫度比的收入,因此只需計算一次。 表達式`var`根據需要經常引用此變數,而無需重新計算它。

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>後續步驟

有關實現表達式的更多代碼範例,請參閱以下文章:

> [!div class="nextstepaction"] 
> [新增符號層次](map-add-pin.md)

> [!div class="nextstepaction"] 
> [新增氣泡圖層](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)

> [!div class="nextstepaction"] 
> [新增熱度圖圖層](map-add-heat-map-layer.md)

詳細瞭解支援表示式的圖層選項:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [線圖層選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [多邊形圖層選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
