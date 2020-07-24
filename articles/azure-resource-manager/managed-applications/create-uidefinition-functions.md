---
title: 建立 UI 定義函式
description: 描述建構 Azure 受控應用程式的 UI 定義時要使用的函式
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: e4255f0d42e28a72ad55d9b7f81d0dc49b2950cb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87041001"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition 函式

本文提供 CreateUiDefinition 支援的功能總覽。

## <a name="function-syntax"></a>函數語法

若要使用函式，請使用方括弧括住調用。 例如:

```json
"[function()]"
```

您可以參考字串和其他函式做為函式的參數，但必須以單引號括住字串。 例如:

```json
"[fn1(fn2(), 'demo text')]"
```

如果適用，您可以使用點運算子參考函式輸出的屬性。 例如:

```json
"[func().prop1]"
```

## <a name="collection-functions"></a>集合函式

這些函式可以搭配集合使用，例如 JSON 字串、陣列和物件。

* [contains](create-ui-definition-collection-functions.md#contains)
* [empty](create-ui-definition-collection-functions.md#empty)
* [出](create-ui-definition-collection-functions.md#filter)
* [first](create-ui-definition-collection-functions.md#first)
* [last](create-ui-definition-collection-functions.md#last)
* [length](create-ui-definition-collection-functions.md#length)
* [地圖](create-ui-definition-collection-functions.md#map)
* [skip](create-ui-definition-collection-functions.md#skip)
* [分割](create-ui-definition-collection-functions.md#split)
* [take](create-ui-definition-collection-functions.md#take)

## <a name="conversion-functions"></a>轉換函數

這些函式可用來轉換 JSON 資料類型與編碼之間的值。

* [bool](create-ui-definition-conversion-functions.md#bool)
* [decodeBase64](create-ui-definition-conversion-functions.md#decodebase64)
* [decodeUriComponent](create-ui-definition-conversion-functions.md#decodeuricomponent)
* [encodeBase64](create-ui-definition-conversion-functions.md#encodebase64)
* [encodeUriComponent](create-ui-definition-conversion-functions.md#encodeuricomponent)
* [float](create-ui-definition-conversion-functions.md#float)
* [int](create-ui-definition-conversion-functions.md#int)
* [分析](create-ui-definition-conversion-functions.md#parse)
* [string](create-ui-definition-conversion-functions.md#string)

## <a name="date-functions"></a>日期函式

* [addHours](create-ui-definition-date-functions.md#addhours)
* [addMinutes](create-ui-definition-date-functions.md#addminutes)
* [addSeconds](create-ui-definition-date-functions.md#addseconds)
* [utcNow](create-ui-definition-date-functions.md#utcnow)

## <a name="logical-functions"></a>邏輯函式

這些函式可在條件中使用。 有些函式可能不支援所有 JSON 資料類型。

* [and](create-ui-definition-logical-functions.md#and)
* [coalesce](create-ui-definition-logical-functions.md#coalesce)
* [equals](create-ui-definition-logical-functions.md#equals)
* [greater](create-ui-definition-logical-functions.md#greater)
* [greaterOrEquals](create-ui-definition-logical-functions.md#greaterorequals)
* [if](create-ui-definition-logical-functions.md#if)
* [less](create-ui-definition-logical-functions.md#less)
* [lessOrEquals](create-ui-definition-logical-functions.md#lessorequals)
* [not](create-ui-definition-logical-functions.md#not)
* [or](create-ui-definition-logical-functions.md#or)

## <a name="math-functions"></a>數學函數

* [add](create-ui-definition-math-functions.md#add)
* [ceil](create-ui-definition-math-functions.md#ceil)
* [div](create-ui-definition-math-functions.md#div)
* [車間](create-ui-definition-math-functions.md#floor)
* [max](create-ui-definition-math-functions.md#max)
* [min](create-ui-definition-math-functions.md#min)
* [mod](create-ui-definition-math-functions.md#mod)
* [mul](create-ui-definition-math-functions.md#mul)
* [rand](create-ui-definition-math-functions.md#rand)
* [range](create-ui-definition-math-functions.md#range)
* [sub](create-ui-definition-math-functions.md#sub)

## <a name="referencing-functions"></a>參考函式

* [操作](create-ui-definition-referencing-functions.md#basics)
* [location](create-ui-definition-referencing-functions.md#location)
* [resourceGroup](create-ui-definition-referencing-functions.md#resourcegroup)
* [步驟](create-ui-definition-referencing-functions.md#steps)
* [訂用帳戶](create-ui-definition-referencing-functions.md#subscription)

## <a name="string-functions"></a>字串函數

* [concat](create-ui-definition-string-functions.md#concat)
* [endsWith](create-ui-definition-string-functions.md#endswith)
* [guid](create-ui-definition-string-functions.md#guid)
* [indexOf](create-ui-definition-string-functions.md#indexof)
* [lastIndexOf](create-ui-definition-string-functions.md#lastindexof)
* [replace](create-ui-definition-string-functions.md#replace)
* [startsWith](create-ui-definition-string-functions.md#startswith)
* [substring](create-ui-definition-string-functions.md#substring)
* [toLower](create-ui-definition-string-functions.md#tolower)
* [toUpper](create-ui-definition-string-functions.md#toupper)

## <a name="next-steps"></a>接下來的步驟

* 如需 Azure Resource Manager 的簡介，請參閱 [Azure Resource Manager 概觀](../management/overview.md)。
