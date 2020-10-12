---
title: 建立 UI 定義數學函數
description: 描述執行數學運算時要使用的函數。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 022e59d847a4d89b4243223637fc6fd1e73255a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096232"
---
# <a name="createuidefinition-math-functions"></a>CreateUiDefinition 數學函數

這些函數可讓您執行數學運算。

## <a name="add"></a>add

將兩個數字相加，並傳回結果。

下列範例會傳回 `3`：

```json
"[add(1, 2)]"
```

## <a name="ceil"></a>ceil

傳回大於或等於指定數字的最小整數。

下列範例會傳回 `4`：

```json
"[ceil(3.14)]"
```

## <a name="div"></a>div

將第一個數字除以第二個數字，並傳回結果。 結果永遠是整數。

下列範例會傳回 `2`：

```json
"[div(6, 3)]"
```

## <a name="floor"></a>floor

傳回小於或等於指定數字的最大整數。

下列範例會傳回 `3`：

```json
"[floor(3.14)]"
```

## <a name="max"></a>max

傳回兩個數字中較大的數字。

下列範例會傳回 `2`：

```json
"[max(1, 2)]"
```

## <a name="min"></a>分鐘

傳回兩個數字的較小者。

下列範例會傳回 `1`：

```json
"[min(1, 2)]"
```

## <a name="mod"></a>mod

將第一個數字除以第二個數字，並傳回餘數。

下列範例會傳回 `0`：

```json
"[mod(6, 3)]"
```

下列範例會傳回 `2`：

```json
"[mod(6, 4)]"
```

## <a name="mul"></a>mul

將兩個數字相乘，並傳回結果。

下列範例會傳回 `6`：

```json
"[mul(2, 3)]"
```

## <a name="rand"></a>rand

產生指定範圍內的隨機整數。 此函式不會產生密碼編譯安全的隨機數字。

下列範例可能會傳回 `42`：

```json
"[rand(-100, 100)]"
```

## <a name="range"></a>range

產生指定範圍內的整數序列。

下列範例會傳回 `[1,2,3]`：

```json
"[range(1, 3)]"
```

## <a name="sub"></a>sub

將第一個數字減去第二個數字，並傳回結果。

下列範例會傳回 `1`：

```json
"[sub(3, 2)]"
```

## <a name="next-steps"></a>接下來的步驟

* 如需 Azure Resource Manager 的簡介，請參閱 [Azure Resource Manager 概觀](../management/overview.md)。
