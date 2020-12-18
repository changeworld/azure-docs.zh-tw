---
title: Android 地圖中的資料驅動樣式表達式 |Microsoft Azure 對應
description: 深入瞭解資料驅動樣式表達式。 瞭解如何在 Azure 地圖服務 Android SDK 中使用這些運算式來調整地圖中的樣式。
author: rbrundritt
ms.author: richbrun
ms.date: 12/1/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 61d7a295d86fd7da74dee03cd35c79feea0218ed
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681491"
---
# <a name="data-driven-style-expressions-android-sdk"></a>資料驅動樣式表達式 (Android SDK) 

運算式可讓您將商務邏輯套用至樣式選項，以觀察資料來源中每個圖形中定義的屬性。 運算式可以篩選資料來源或圖層中的資料。 運算式可能包含條件式邏輯，例如 if 語句。 而且，您可以使用：字串運算子、邏輯運算子和數學運算子來運算元據。

資料驅動的樣式可減少在設定樣式時執行商務邏輯所需的程式碼數量。 與圖層搭配使用時，運算式會在轉譯時間于個別執行緒上進行評估。 相較于在 UI 執行緒上評估商務邏輯，這種功能可提供更高的效能。

Azure 地圖服務 Android SDK 幾乎支援所有與 Azure 地圖服務 Web SDK 相同的樣式表達式，因此，在資料驅動的樣式表達式中所述的所有相同概念 [ (WEB sdk) ](data-driven-style-expressions-web-sdk.md) 都可以帶入 Android 應用程式。 Azure 地圖服務 Android SDK 中的所有樣式表達式都可在 `com.microsoft.azure.maps.mapcontrol.options.Expression` 命名空間底下取得。 樣式表達式有許多不同的類型。

| 運算式的類型 | 描述 |
|---------------------|-------------|
| [布林運算式](#boolean-expressions) | 布林運算式提供一組布林運算子運算式來評估布林值比較。 |
| [色彩運算式](#color-expressions) | 色彩運算式可讓您更輕鬆地建立和操作色彩值。 |
| [條件運算式](#conditional-expressions) | 條件運算式會提供類似 if 語句的邏輯作業。 |
| [日期運算式](#data-expressions) | 提供功能中屬性資料的存取權。 |
| [插補和步驟運算式](#interpolate-and-step-expressions) | 插補和步驟運算式可用來計算插補曲線或步驟函數的值。 |
| [以 JSON 為基礎的運算式](#json-based-expressions) | 可讓您輕鬆地重複使用以 Android SDK 為 Web SDK 建立的樣式原始 JSON 型運算式。 |  
| [圖層特定運算式](#layer-specific-expressions) | 僅適用于單一層的特殊運算式。 |
| [數學運算式](#math-expressions) | 提供數學運算子來執行運算式架構內的資料驅動計算。 |
| [字串運算子運算式](#string-operator-expressions) | 字串運算子運算式會針對字串執行轉換作業，例如串連和轉換大小寫。 |
| [類型表示方式](#type-expressions) | 類型運算式提供用來測試和轉換不同資料類型（例如字串、數位和布林值）的工具。 |
| [變數系結運算式](#variable-binding-expressions) | 變數系結運算式會將計算的結果儲存在變數中，並在運算式中的其他位置多次參考，而不需重新計算儲存的值。 |
| [Zoom 運算式](#zoom-expression) | 在轉譯時期抓取地圖目前的縮放層級。 |

本檔的這一節中的所有範例都會使用下列功能來示範可以使用這些運算式的不同方式。

```json
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
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

下列程式碼說明如何在應用程式中手動建立此 GeoJSON 功能。

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

下列程式碼說明如何將 JSON 物件的 stringified 版本還原序列化為應用程式中的 GeoJSON 功能。

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

## <a name="json-based-expressions"></a>以 JSON 為基礎的運算式

Azure 地圖服務 Web SDK 也支援使用 JSON 陣列表示的資料驅動樣式表達式。 您可以使用 Android SDK 中的原生類別來重新建立這些相同 `Expression` 的運算式。 或者，這些以 JSON 為基礎的運算式可以使用類似的 web 函式轉換成字串 `JSON.stringify` ，並傳遞給 `Expression.raw(String rawExpression)` 方法。 例如，請採用下列 JSON 運算式。

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

上述運算式的 stringified 版本會是 `"['get','title']"` ，並可讀入 Android SDK，如下所示。

```java
Expression exp = Expression.raw("['get','title']")
```

使用這種方法可讓您輕鬆地在使用 Azure 地圖服務的行動和 web 應用程式之間重複使用樣式表達式。

這段影片提供 Azure 地圖服務中資料驅動樣式的總覽。

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>日期運算式

日期運算式可提供功能中屬性資料的存取權。

| 運算式 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `accumulated()` | number | 取得到目前為止累積的群集屬性值。 |
| `at(number | Expression, Expression)` | 值 | 從陣列抓取專案。 |
| `geometryType()` | 字串 | 取得特徵的幾何類型： Point、MultiPoint、LineString、MultiLineString、多邊形、MultiPolygon。 |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | 值 | 從提供之物件的屬性取得屬性值。 如果遺漏要求的屬性，則傳回 null。 |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | 判斷功能的屬性是否具有指定的屬性。 |
| `id()` | 值 | 取得功能的識別碼（如果有的話）。 |
| `in(string | number | Expression, Expression)` | boolean | 判斷專案是否存在於陣列中 |
| `length(string | Expression)` | number | 取得字串或陣列的長度。 |
| `properties()`| 值 | 取得功能屬性物件。 |

Android SDK 不支援下列 Web SDK 樣式表達式：

- 索引-
- slice

**範例**

您可以使用運算式，直接在運算式中存取功能的屬性 `get` 。 這個範例會使用 `zoneColor` 功能的值來指定反升圖圖層的色彩屬性。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

如果所有點特徵都有屬性，上述範例將會正常運作 `zoneColor` 。 如果沒有，色彩可能會切換回「黑色」。 若要修改回復色彩，請使用 `switchCase` 運算式搭配 `has` 運算式來檢查屬性是否存在。 如果屬性不存在，則傳回回的色彩。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

依預設，反升和符號圖層會轉譯資料來源中所有形狀的座標。 此行為可以反白顯示多邊形或線條的頂點。 您 `filter` 可以使用布林運算式內的運算式，將圖層的選項用來限制其轉譯之功能的幾何類型 `geometryType` 。 下列範例會限制一個反升圖層，只 `Point` 呈現特徵。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    filter(eq(geometryType(), "Point"))
);
```

下列範例允許轉譯 `Point` 和 `MultiPoint` 功能。 

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

同樣地，多邊形的外框會線上條圖層中呈現。 若要線上條圖層中停用此行為，請新增只允許 `LineString` 和功能的篩選 `MultiLineString` 。  

以下是一些如何使用日期運算式的其他範例：

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>數學運算式

數學運算式提供數學運算子，可在運算式架構內執行資料驅動計算。

| 運算式 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `abs(number | Expression)` | number | 計算指定之數字的絕對值。 |
| `acos(number | Expression)` | number | 計算指定數位的反余弦值。 |
| `asin(number | Expression)` | number | 計算指定數位的反正弦值。 |
| `atan(number | Expression)` | number | 計算指定數位的反正切值。 |
| `ceil(number | Expression)` | number | 將數位四捨五入到下一個整數。 |
| `cos(number | Expression)` | number | 計算指定數位的 cos。 |
| `division(number, number)` \| `division(Expression, Expression)` | number | 將第一個數位除以第二個數字。 Web SDK 對等運算式： `/` |
| `e()` | number | 傳回數學常數 `e` 。 |
| `floor(number | Expression)` | number | 將數位四捨五入至先前的整數。 |
| `log10(number | Expression)` | number | 計算指定數位的底數（以10為底數）。 |
| `log2(number | Expression)` | number | 計算指定數位的底數（以兩為底數）。 |
| `ln(number | Expression)` | number | 計算指定數位的自然對數。 |
| `ln2()` | number | 傳回數學常數 `ln(2)` 。 |
| `max(numbers... | expressions...)` | number | 計算指定數位集合中的最大數目。 |
| `min(numbers... | expressions...)` | number | 計算指定數位集合中的最小數目。 |
| `mod(number, number)` \| | `mod(Expression, Expression)` | number | 計算將第一個數位除以第二個數字時的餘數。 Web SDK 對等運算式： `%` |
| `pi()` | number | 傳回數學常數 `PI` 。 |
| `pow(number, number)` \| `pow(Expression, Expression)` | number | 計算第一個值的值，此值會產生第二個數字的乘冪。 |
| `product(numbers... | expressions...)` | number | 將指定的數位相乘。 Web SDK 對等運算式： `*` |
| `round(number | Expression)` | number | 將數位四捨五入到最接近的整數。 中間值會從零四捨五入。 例如，會 `round(-1.5)` 評估為 `-2` 。 |
| `sin(number | Expression)` | number | 計算指定數位的正弦。 |
| `sqrt(number | Expression)` | number | 計算指定之數字的平方根。 |
| `subtract(number | Expression` | number | 以指定的數位減去0。 |
| `subtract(number | Expression, number | Expression)` | number | 將第一個數位減去第二個數字。 |
| `sum(numbers... | expressions...)` | number | 計算指定之數位的總和。 |
| `tan(number | Expression)` | number | 計算指定數位的正切值。 |

## <a name="boolean-expressions"></a>布林運算式

布林運算式提供一組布林運算子運算式來評估布林值比較。

比較值時，會嚴格地輸入比較。 不同類型的值一律視為不相等。 在剖析階段已知類型不同的情況會被視為無效，而且會產生剖析錯誤。

| 運算式 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | `true`如果所有輸入皆為，則傳回 `true` ， `false` 否則傳回。 |
| `any(Expression...)` | boolean | `true`如果任何輸入為，則傳回 `true` ， `false` 否則傳回。 |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`如果輸入值相等，則傳回， `false` 否則傳回。 引數必須是字串或兩個數字都是。 |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`如果第一個輸入嚴格大於第二個輸入，則傳回， `false` 否則傳回。 引數必須是字串或兩個數字都是。 |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`如果第一個輸入大於或等於第二個輸入，則傳回， `false` 否則傳回。 引數必須是字串或兩個數字都是。 |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`如果第一個輸入嚴格小於第二個輸入，則傳回， `false` 否則傳回。 引數必須是字串或兩個數字都是。 |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`如果第一個輸入小於或等於第二個輸入，則傳回， `false` 否則傳回。 引數必須是字串或兩個數字都是。 |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | `true`如果輸入值不相等，則傳回， `false` 否則傳回。 |
| `not(Expression | boolean)` | boolean | 邏輯否定。 `true`如果輸入是，則傳回 `false` ， `false` 如果輸入為，則傳回 `true` 。 |

## <a name="conditional-expressions"></a>條件運算式

條件運算式會提供類似 if 語句的邏輯作業。

下列運算式會對輸入資料執行條件式邏輯作業。 例如， `switchCase` 運算式會提供 "if/then/else" 邏輯，而 `match` 運算式就像是「switch 語句」。 

### <a name="switch-case-expression"></a>切換 case 運算式

`switchCase`運算式是條件運算式的類型，可提供 "if/then/else" 邏輯。 這種類型的運算式會逐步解說布林值條件的清單。 它會傳回評估為 true 的第一個布林值條件的輸出值。

下列虛擬程式碼會定義運算式的結構 `switchCase` 。

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**範例**

下列範例會逐步解說不同的布林條件，直到找到評估為的 `true` ，然後再傳回該相關聯的值為止。 如果沒有布林條件評估為 `true` ，則會傳回回溯值。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

### <a name="match-expression"></a>Match 運算式

`match`運算式是一種條件運算式，可提供類似邏輯的 switch 語句。 輸入可以是任何會傳回 `get( "entityType")` 字串或數位的運算式。 每個停止都必須有一個屬於單一常值或常值陣列的標籤，其值必須是所有字串或所有數位。 如果陣列中的任何值相符，則輸入會相符。 每個停止標籤都必須是唯一的。 如果輸入類型與標籤的類型不符，則結果將會是預設的回溯值。

下列虛擬程式碼會定義運算式的結構 `match` 。

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**範例**

下列範例會查看反升 `entityType` 圖圖層中的點特徵屬性，以搜尋是否相符。 如果找到相符的值，則會傳回指定的值，或傳回此值。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

下列範例會使用陣列來列出一組應該會傳回相同值的標籤。 這種方法比個別列出每個標籤更有效率。 在此情況下，如果 `entityType` 屬性為 "餐廳" 或 "grocery_store"，則會傳回 "red" 色彩。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

### <a name="coalesce-expression"></a>聯合運算式

`coalesce`運算式會逐步執行一組運算式，直到取得第一個非 null 值並傳回該值為止。

下列虛擬程式碼會定義運算式的結構 `coalesce` 。

```java
coalesce(Expression... input)
```

**範例**

下列範例會使用 `coalesce` 運算式來設定 `textField` 符號圖層的選項。 如果 `title` 功能中缺少屬性或將其設定為 `null` ，則運算式會嘗試尋找 `subTitle` 屬性（如果其遺失或 `null` ，則會切換回空字串）。 

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

## <a name="type-expressions"></a>類型表示方式

類型運算式提供用來測試和轉換不同資料類型（例如字串、數位和布林值）的工具。

| 運算式 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `array(Expression)` | Object [] | 判斷提示輸入是否為數組。 |
| `bool(Expression)` | boolean | 判斷提示輸入值是否為布林值。 |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | 分頁 | 傳回用於地區設定相依比較作業的排序程式。 區分大小寫和區分變音符號的選項預設為 false。 Locale 引數會指定要使用之地區設定的 IETF 語言標記。 如果未提供任何值，則會使用預設的地區設定。 如果無法使用所要求的地區設定，則排序器會使用系統定義的回溯地區設定。 使用已解析的地區設定來測試地區設定回復行為的結果。  |
| `literal(boolean \| number \| string \| Object \| Object[])` | 布林 \| 值 \| 字串 \| 物件 \| 物件 [] | 傳回常值陣列或物件值。 您可以使用此運算式來防止陣列或物件被評估為運算式。 當運算式需要傳回陣列或物件時，這是必要的。 |
| `number(Expression)` | number | 判斷提示輸入值為數字。 |
| `object(Expression)` | Object | 判斷提示輸入值是否為物件。 |
| `string(Expression)` | 字串 | 判斷提示輸入值是否為字串。 |
| `toArray(Expression)` | Object [] | 將運算式轉換為 JSON 物件陣列。 |
| `toBool(Expression)` | boolean | 將輸入值轉換為布林值。 |
| `toNumber(Expression)` | number | 盡可能將輸入值轉換成數位。 |
| `toString(Expression)` | 字串 | 將輸入值轉換成字串。 |
| `typeoOf(Expression)` | 字串 | 傳回字串，描述指定值的型別。 |

## <a name="color-expressions"></a>色彩運算式

色彩運算式可讓您更輕鬆地建立和操作色彩值。

| 運算式 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `color(int)` | color | 將色彩整數值轉換成色彩運算式。 |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | 從 *紅色*、 *綠色* 和 *藍色* 元件建立色彩值，而這些元件必須介於 `0` 和之間 `255` ，以及的 Alpha 元件之間 `1` 。 如果有任何元件超出範圍，則運算式會是錯誤。 |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | 從 *紅色*、 *綠色*、 *藍色* 元件建立色彩值，這些元件必須介於 `0` 和之間 `255` ，以及在和範圍內的 Alpha `0` 元件 `1` 。 如果有任何元件超出範圍，則運算式會是錯誤。 |
| `toColor(Expression)` | color  | 將輸入值轉換成色彩。 |
| `toRgba(Expression)` | color | 傳回四個元素的陣列，其中包含輸入色彩的紅色、綠色、藍色和 Alpha 元件（依該順序）。 |

**範例**

下列範例會建立具有 *紅色* 值的 RGB 色彩值 `255` ，以及透過乘以屬性值所計算的 *綠色* 和 *藍色* 值 `2.5` `temperature` 。 當溫度變化時，色彩會變更為不同的 *紅色* 陰影。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

如果所有色彩參數都是數位，就不需要使用運算式來包裝它們 `literal` 。 例如：

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

> [!TIP]
> 您可以使用方法，將字串色彩值轉換成色彩 `android.graphics.Color.parseColor` 。 下列程式會將十六進位色彩字串轉換成可搭配圖層使用的色彩運算式。
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>字串運算子運算式

字串運算子運算式會針對字串執行轉換作業，例如串連和轉換大小寫。 

| 運算式 | 傳回類型 | 描述 |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | 字串 | 將多個字串串連在一起。 每個值都必須是字串。 若有需要，請使用 `toString` 類型運算式將其他實數值型別轉換為字串。 |
| `downcase(string)` \| `downcase(Expression)` | 字串 | 將指定字串轉換為小寫。 |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | 判斷輸入字串是否使用目前的字型堆疊所支援的字元集。 例如：`isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | 字串 | 傳回所提供之排序器所使用之地區設定的 IETF 語言標記。 這可以用來判斷預設的系統地區設定，或判斷是否已成功載入要求的地區設定。 |
| `upcase(string)` \| `upcase(Expression)` | 字串 | 將指定字串轉換為大寫。 |

**範例**

下列範例 `temperature` 會將點特徵的屬性轉換成字串，然後將 "° f" 串連至它的結尾。

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

上述運算式會在地圖上呈現 pin，並在其上方重迭文字 "64 ° F"，如下圖所示。

![字串運算子運算式範例](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>插補和步驟運算式

插補和步驟運算式可用來計算插補曲線或步驟函數的值。 例如，這些運算式會採用傳回數值做為其輸入的運算式 `get("temperature")` 。 輸入值會針對輸入和輸出值的配對進行評估，以判斷最適合插入曲線或步驟函式的值。 輸出值稱為「停止」。 每個停止的輸入值都必須是數位，而且是以遞增順序排列。 輸出值必須是數位、數位的陣列或色彩。

### <a name="interpolate-expression"></a>插值運算式

`interpolate`運算式可以用來在停止值之間插插，以計算連續且平滑的值集。 傳回 `interpolate` 色彩值的運算式會產生從選取結果值的色彩漸層。 `interpolate`運算式的格式如下：

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

運算式中可以使用三種類型的插補方法 `interpolate` ：

| 名稱 | 描述 | 
|------|-------------|
| `linear()` | 在兩組停止之間以線性方式進行插補。  |
| `exponential(number)` \| `exponential(Expression)` | 在停止時以指數方式進行插補。 指定「基底」，並控制輸出增加的速率。 較高的值可讓輸出更接近範圍的最高。 接近1的「基底」值會產生輸出，並以線性方式增加。|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | 使用指定控制點所定義的 [三次方貝茲曲線](https://developer.mozilla.org/docs/Web/CSS/timing-function) 進行插補。 |

`stop`運算式的格式為 `stop(stop, value)` 。
 
以下是這些不同類型的插補外觀範例。 

| 線性  | 指數 | 三次方貝茲 |
|---------|-------------|--------------|
| ![線性插補圖形](media/how-to-expressions/linear-interpolation.png) | ![指數插補圖形](media/how-to-expressions/exponential-interpolation.png) | ![三次方貝塞爾插值圖](media/how-to-expressions/bezier-curve-interpolation.png) |

**範例**

下列範例會 `linear interpolate` 根據點特徵的屬性，使用運算式來設定反升 `bubbleColor` 圖層的屬性 `temperature` 。 如果 `temperature` 值小於60，則會傳回 "blue"。 如果介於60到小於70之間，則會傳回黃色。 如果介於70到80之間，則會傳回「橙色」 (`#FFA500`) 。 如果是80或更高版本，則會傳回 "red"。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

下圖將示範如何選擇上述運算式的色彩。

![插值運算式範例](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>步驟運算式

`step`運算式可以用來計算由停止所定義的[分段常數](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)函式，以計算離散的結果值。 

`interpolate`運算式的格式如下：

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

步驟運算式會傳回在輸入值之前停止的輸出值，如果輸入小於第一個停止則傳回第一個輸入值。 

**範例**

下列範例會 `step` 根據點特徵的屬性，使用運算式來設定反升 `bubbleColor` 圖層的屬性 `temperature` 。 如果 `temperature` 值小於60，則會傳回 "blue"。 如果介於60到小於70之間，則會傳回「黃色」。 如果介於70到小於80之間，則會傳回 "橙色"。 如果是80或更高版本，則會傳回 "red"。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

下圖將示範如何選擇上述運算式的色彩。
 
![步驟運算式範例](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>圖層特定的運算式

只適用于特定層的特殊運算式。

### <a name="heat-map-density-expression"></a>熱度圖密度運算式

熱度圖密度運算式會抓取熱度圖圖層中每個圖元的熱度圖密度值，並定義為 `heatmapDensity` 。 這個值是和之間的 `0` 數位 `1` 。 它會與 or 運算式搭配使用 `interpolation` `step` ，以定義用來為熱度圖著色的色彩漸層。 此運算式只能用於 `heatmapColor` 熱度圖層的選項。

> [!TIP]
> 位於索引0、內插補點運算式或步驟色彩預設色彩的色彩，會定義沒有資料的區域色彩。 位於索引0的色彩可以用來定義背景色彩。 許多人偏好將此值設定為透明或半透明的黑色。

**範例**

此範例會使用指令插補點運算式來建立平滑色彩漸層，以呈現熱度圖。 

```java
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

除了使用平滑漸層來標示熱度圖，也可以使用運算式在一組範圍內指定色彩。 `step` 使用 `step` 標示熱度圖的運算式會以視覺方式將密度細分為類似于輪廓或雷達圖樣式地圖的範圍。  

```java 
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

如需詳細資訊，請參閱 [新增熱度圖圖層](map-add-heat-map-layer-android.md) 檔。

### <a name="text-field-format-expression"></a>文字欄位格式運算式

`format`運算式可以搭配 `textField` 符號圖層的選項使用，以提供混合的文字格式。 此運算式會接受一或多個 `formatEntry` 運算式，以指定 `formatOptions` 要附加至文字欄位的字串和集合。

| 運算式 | 描述 |
|------------|-------------|
| `format(Expression...)` | 傳回包含批註的格式化文字，以用於混合格式的文字欄位專案。 |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | 傳回要在運算式中使用的格式化字串專案 `format` 。 |

可用的格式選項如下：

| 運算式 | 描述 |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | 指定字型大小的縮放比例。 如果有指定，此值會覆寫 `textSize` 個別字串的屬性。 |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | 指定呈現時要套用至文字的色彩。 |

**範例**

下列範例會藉由加入粗體字型，以及相應增加功能屬性的字型大小來格式化文字欄位 `title` 。 此範例也會 `subTitle` 在新行上新增功能的屬性，並縮小字型大小。

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

這一層會轉譯點功能，如下圖所示：

![具有格式化文字欄位之 Point 功能的影像](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Zoom 運算式

`zoom`運算式是用來在轉譯時期取得地圖目前的縮放層級，並定義為 `zoom()` 。 此運算式會傳回地圖的最小和最大縮放層級範圍之間的數位。 Web 和 Android Azure 地圖服務的互動式地圖控制項支援25個縮放層級，編號0到24。 使用 `zoom` 運算式可在地圖的縮放層級變更時，動態修改樣式。 `zoom`運算式只能搭配 `interpolate` 和 `step` 運算式使用。

**範例**

根據預設，熱度圖圖層中轉譯的資料點半徑在所有縮放層級中都有固定的圖元半徑。 當地圖放大時，資料匯總在一起，而熱度圖層看起來會不同。 `zoom`運算式可以用來調整每個縮放層級的半徑，讓每個資料點都涵蓋地圖的相同實體區域。 它會讓熱度圖圖層看起來更為靜態且一致。 地圖的每個縮放層級都有兩倍垂直和水準的圖元作為先前的縮放層級。 調整半徑，使其每個縮放層級加倍，將會建立在所有縮放層級上看起來一致的熱度圖。 您可以使用運算式搭配運算式來完成這項作業 `zoom` `base 2 exponential interpolation` ，並設定最小縮放層級的圖元半徑，以及最大縮放層級的調整半徑，如下 `2 * Math.pow(2, minZoom - maxZoom)` 所示。

```java 
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

## <a name="variable-binding-expressions"></a>變數系結運算式

變數系結運算式會將計算的結果儲存在變數中。 因此，您可以在運算式中的其他位置多次參考計算結果。 這對牽涉到許多計算的運算式而言是很實用的優化。

| 運算式 | 傳回類型 | 描述 |
|--------------|---------------|--------------|
| `let(Expression... input)` | | 將一或多個值儲存為變數，以供傳回 `var` 結果之子運算式中的運算式使用。 |
| `var(Expression expression)` \| `var(string variableName)` | Object | 參考使用運算式建立的變數 `let` 。 |

**範例**

這個範例會使用計算相對於溫度比率之收益的運算式，然後使用 `case` 運算式來評估此值的不同布林值運算。 `let`運算式是用來儲存相對於溫度比率的收入，因此只需要計算一次。 `var`運算式會視需要經常參考此變數，而不需要重新計算。

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

## <a name="next-steps"></a>後續步驟

深入瞭解支援運算式的層級：

> [!div class="nextstepaction"]
> [新增符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [新增線條圖層](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [新增熱度圖](map-add-heat-map-layer-android.md)
