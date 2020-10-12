---
title: 建立 UI 定義集合函數
description: 描述使用集合（例如陣列和物件）時要使用的函數。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 2a075c5c99f457681cd49e75014487bf9cca263c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096238"
---
# <a name="createuidefinition-collection-functions"></a>CreateUiDefinition 集合函數

這些函式可以搭配集合使用，例如 JSON 字串、陣列和物件。

## <a name="contains"></a>contains

如果字串包含指定的子字串、陣列包含指定的值，或是物件包含指定的索引鍵，則傳回 `true`。

### <a name="example-string-contains"></a>範例：字串 contains

下列範例會傳回 `true`：

```json
"[contains('webapp', 'web')]"
```

### <a name="example-array-contains"></a>範例：陣列 contains

假設 `element1` 傳回 `[1, 2, 3]`。 下列範例會傳回 `false`：

```json
"[contains(steps('demoStep').element1, 4)]"
```

### <a name="example-object-contains"></a>範例：物件包含

假設 `element1` 傳回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

下列範例會傳回 `true`：

```json
"[contains(steps('demoStep').element1, 'key1')]"
```

## <a name="empty"></a>empty

如果字串、陣列或物件為 null 或空白，則傳回 `true`。

### <a name="example-string-empty"></a>範例：字串空白

下列範例會傳回 `true`：

```json
"[empty('')]"
```

### <a name="example-array-empty"></a>範例：陣列空白

假設 `element1` 傳回 `[1, 2, 3]`。 下列範例會傳回 `false`：

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-object-empty"></a>範例：物件空白

假設 `element1` 傳回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

下列範例會傳回 `false`：

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-null-and-undefined"></a>範例： null 和未定義

假設 `element1` 為 `null` 或未定義。 下列範例會傳回 `true`：

```json
"[empty(steps('demoStep').element1)]"
```

## <a name="filter"></a>篩選條件

套用作為 lambda 函數提供的篩選邏輯之後，傳回新的陣列。 第一個參數是要用於篩選的陣列。 第二個參數是指定篩選邏輯的 lambda 函數。

下列範例會傳回陣列 `[ { "name": "abc" } ]` 。

```json
"[filter(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => contains(item.name, 'abc'))]"
```

## <a name="first"></a>first

傳回指定字串的第一個字元、指定陣列的第一個值，或指定物件的第一個索引鍵和值。

### <a name="example-string-first"></a>範例：字串 first

下列範例會傳回 `"c"`：

```json
"[first('contoso')]"
```

### <a name="example-array-first"></a>範例：陣列 first

假設 `element1` 傳回 `[1, 2, 3]`。 下列範例會傳回 `1`：

```json
"[first(steps('demoStep').element1)]"
```

#### <a name="example-object-first"></a>範例：物件 first

假設 `element1` 傳回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

下列範例會傳回 `{"key1": "Linux"}`：

```json
"[first(steps('demoStep').element1)]"
```

## <a name="last"></a>last

傳回指定字串的最後一個字元、指定陣列的最後一個值，或指定物件的最後一個索引鍵和值。

### <a name="example-string-last"></a>範例： last 字串

下列範例會傳回 `"o"`：

```json
"[last('contoso')]"
```

### <a name="example-array-last"></a>範例：陣列 last

假設 `element1` 傳回 `[1, 2, 3]`。 下列範例會傳回 `3`：

```json
"[last(steps('demoStep').element1)]"
```

### <a name="example-object-last"></a>範例：物件 last

假設 `element1` 傳回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

下列範例會傳回 `{"key2": "Windows"}`：

```json
"[last(steps('demoStep').element1)]"
```

## <a name="length"></a>長度

傳回字串中的字元數目、陣列中的值數目或物件中的索引鍵數目。

### <a name="example-string-length"></a>範例：字串長度

下列範例會傳回 `7`：

```json
"[length('Contoso')]"
```

### <a name="example-array-length"></a>範例：陣列長度

假設 `element1` 傳回 `[1, 2, 3]`。 下列範例會傳回 `3`：

```json
"[length(steps('demoStep').element1)]"
```

### <a name="example-object-length"></a>範例：物件長度

假設 `element1` 傳回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

下列範例會傳回 `2`：

```json
"[length(steps('demoStep').element1)]"
```

## <a name="map"></a>map

在提供的陣列上呼叫 lambda 函數之後，傳回新的陣列。 第一個參數是用於 lambda 函數的陣列。 第二個參數是 lambda 函數。

下列範例會傳回每個值加倍的新陣列。 結果為 `[2, 4, 6]`。

```json
"[map(parse('[1, 2, 3]'), (item) => mul(2, item))]"
```

下列範例會傳回新的陣列 `["abc", "xyz"]` 。

```json
"[map(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => item.name)]"
```

## <a name="skip"></a>skip

略過集合中指定數目的元素，然後傳回其餘的元素。

### <a name="example-string-skip"></a>範例：字串 skip

下列範例會傳回 `"app"`：

```json
"[skip('webapp', 3)]"
```

### <a name="example-array-skip"></a>範例：陣列 skip

假設 `element1` 傳回 `[1, 2, 3]`。 下列範例會傳回 `[3]`：

```json
"[skip(steps('demoStep').element1, 2)]"
```

### <a name="example-object-skip"></a>範例： object skip

假設 `element1` 傳回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```
下列範例會傳回 `{"key2": "Windows"}`：

```json
"[skip(steps('demoStep').element1, 1)]"
```

## <a name="split"></a>split

傳回字串陣列，其中包含以分隔符號分隔之輸入的子字串。

下列範例會傳回陣列 `[ "555", "867", "5309" ]` 。

```json
"[split('555-867-5309', '-')]"
```

## <a name="take"></a>take

傳回從字串開頭指定數目的連續字元、從陣列開頭指定數目的連續值，或從物件開頭指定數目的連續索引鍵和值。

### <a name="example-string-take"></a>範例：字串 take

下列範例會傳回 `"web"`：

```json
"[take('webapp', 3)]"
```

### <a name="example-array-take"></a>範例：陣列 take

假設 `element1` 傳回 `[1, 2, 3]`。 下列範例會傳回 `[1, 2]`：

```json
"[take(steps('demoStep').element1, 2)]"
```

### <a name="example-object-take"></a>範例：物件 take

假設 `element1` 傳回：

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

下列範例會傳回 `{"key1": "Linux"}`：

```json
"[take(steps('demoStep').element1, 1)]"
```

## <a name="next-steps"></a>接下來的步驟

* 如需 Azure Resource Manager 的簡介，請參閱 [Azure Resource Manager 概觀](../management/overview.md)。
