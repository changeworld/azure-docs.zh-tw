---
title: 建立 UI 定義字串函數
description: 描述在為 Azure 受控應用程式的 UI 定義進行建立時，所要使用的字串函式
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: c662948542c36cd93f889ca045ee245c15c7bb11
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096230"
---
# <a name="createuidefinition-string-functions"></a>CreateUiDefinition 字串函數

要與 JSON 字串搭配使用的這些函式。

## <a name="concat"></a>concat

串連一或多個字串。

例如，如果 `element1` 的輸出值為 `"Contoso"`，則此範例會傳回字串 `"Demo Contoso app"`：

```json
"[concat('Demo ', steps('step1').element1, ' app')]"
```

## <a name="endswith"></a>endsWith

判斷字串結尾是否為值。

下列範例會傳回 true。

```json
"[endsWith('tuvwxyz', 'xyz')]"
```

## <a name="guid"></a>guid

產生全域唯一字串 (GUID)。

下列範例會傳回如下的值 `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` ：

```json
"[guid()]"
```

## <a name="indexof"></a>indexOf

傳回字串內值的第一個位置，如果找不到，則傳回-1。

下列範例會傳回2。

```json
"[indexOf('abcdef', 'cd')]"
```

## <a name="lastindexof"></a>lastIndexOf

傳回值在字串中的最後一個位置，如果找不到，則傳回-1。

下列範例會傳回3。

```json
"[lastIndexOf('test', 't')]"
```

## <a name="replace"></a>取代

傳回將目前字串中所有指定的字串取代成另一個字串的字串。

下列範例會傳回 `"Contoso.com web app"`：

```json
"[replace('Contoso.net web app', '.net', '.com')]"
```

## <a name="startswith"></a>startsWith

判斷字串開頭是否為值。

下列範例會傳回 true。

```json
"[startsWith('abcdefg', 'ab')]"
```

## <a name="substring"></a>substring

傳回指定字串的子字串。 子字串從指定的索引處開始，而且有指定的長度。

下列範例會傳回 `"web"`：

```json
"[substring('Contoso.com web app', 12, 3)]"
```

## <a name="tolower"></a>toLower

傳回轉換成小寫的字串。

下列範例會傳回 `"contoso"`：

```json
"[toLower('CONTOSO')]"
```

## <a name="toupper"></a>toUpper

傳回轉換成大寫的字串。

下列範例會傳回 `"CONTOSO"`：

```json
"[toUpper('contoso')]"
```

## <a name="next-steps"></a>後續步驟

* 如需 Azure Resource Manager 的簡介，請參閱 [Azure Resource Manager 概觀](../management/overview.md)。

