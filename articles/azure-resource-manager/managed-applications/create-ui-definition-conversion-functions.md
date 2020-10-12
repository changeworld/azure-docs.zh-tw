---
title: 建立 UI 定義轉換函式
description: 描述在資料類型與編碼之間轉換值時所要使用的函數。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: b69cd35b27b343da08727b4c4ee9b4fd025e1df7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096237"
---
# <a name="createuidefinition-conversion-functions"></a>CreateUiDefinition 轉換函式

這些函式可用來轉換 JSON 資料類型與編碼之間的值。

## <a name="bool"></a>bool

將參數轉換為布林值。 此函式支援數值、字串和布林值類型的參數。 類似于 JavaScript 中的布林值，除了 `0` 或傳回的任何值 `'false'` `true` 。

下列範例會傳回 `true`：

```json
"[bool(1)]"
```

下列範例會傳回 `false`：

```json
"[bool(0)]"
```

下列範例會傳回 `true`：

```json
"[bool(true)]"
```

下列範例會傳回 `true`：

```json
"[bool('true')]"
```

## <a name="decodebase64"></a>decodeBase64

將參數從 base-64 編碼字串解碼。 此函式只支援字串類型的參數。

下列範例會傳回 `"Contoso"`：

```json
"[decodeBase64('Q29udG9zbw==')]"
```

## <a name="decodeuricomponent"></a>decodeUriComponent

將參數從 URL 編碼字串解碼。 此函式只支援字串類型的參數。

下列範例會傳回 `"https://portal.azure.com/"`：

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="encodebase64"></a>encodeBase64

將參數編碼為 base-64 編碼字串。 此函式只支援字串類型的參數。

下列範例會傳回 `"Q29udG9zbw=="`：

```json
"[encodeBase64('Contoso')]"
```

## <a name="encodeuricomponent"></a>encodeUriComponent

將參數編碼為 URL 編碼字串。 此函式只支援字串類型的參數。

下列範例會傳回 `"https%3A%2F%2Fportal.azure.com%2F"`：

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

## <a name="float"></a>FLOAT

將參數轉換成浮點數。 此函式支援數值和字串類型的參數。

下列範例會傳回 `1.0`：

```json
"[float('1.0')]"
```

下列範例會傳回 `2.9`：

```json
"[float(2.9)]"
```

## <a name="int"></a>int

將參數轉換成整數。 此函式支援數值和字串類型的參數。

下列範例會傳回 `1`：

```json
"[int('1')]"
```

下列範例會傳回 `2`：

```json
"[int(2.9)]"
```

## <a name="parse"></a>parse

將參數轉換成原生類型。 換句話說，此函式與 `string()` 相反。 此函式只支援字串類型的參數。

下列範例會傳回 `1`：

```json
"[parse('1')]"
```

下列範例會傳回 `true`：

```json
"[parse('true')]"
```

下列範例會傳回 `[1,2,3]`：

```json
"[parse('[1,2,3]')]"
```

下列範例會傳回 `{"type":"webapp"}`：

```json
"[parse('{\"type\":\"webapp\"}')]"
```

## <a name="string"></a>字串

將參數轉換成字串。 此函式支援所有 JSON 資料類型的參數。

下列範例會傳回 `"1"`：

```json
"[string(1)]"
```

下列範例會傳回 `"2.9"`：

```json
"[string(2.9)]"
```

下列範例會傳回 `"[1,2,3]"`：

```json
"[string([1,2,3])]"
```

下列範例會傳回 `"{"type":"webapp"}"`：

```json
"[string({\"type\":\"webapp\"})]"
```

## <a name="next-steps"></a>接下來的步驟

* 如需 Azure Resource Manager 的簡介，請參閱 [Azure Resource Manager 概觀](../management/overview.md)。
