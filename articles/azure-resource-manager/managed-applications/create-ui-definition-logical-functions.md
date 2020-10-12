---
title: 建立 UI 定義邏輯函數
description: 描述執行邏輯運算的函式。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 00d2f0eeb5d353c8ebd7ad30f6866f890d6cb42e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096234"
---
# <a name="createuidefinition-logical-functions"></a>CreateUiDefinition 邏輯函數

這些函數可用於條件運算式中。 有些函式可能不支援所有 JSON 資料類型。

## <a name="and"></a>及

如果所有參數都評估為 `true`，則傳回 `true`。 此函數僅支援兩個或多個參數類型為布林值。

下列範例會傳回 `true`：

```json
"[and(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

下列範例會傳回 `false`：

```json
"[and(equals(0, 0), greater(1, 2))]"
```

## <a name="coalesce"></a>coalesce

傳回第一個非 null 參數的值。 此函式支援所有 JSON 資料類型。

假設 `element1` 和 `element2` 為未定義。 下列範例會傳回 `"Contoso"`：

```json
"[coalesce(steps('demoStep').element1, steps('demoStep').element2, 'Contoso')]"
```

這個函式在頁面載入後因使用者動作而發生的選擇性調用內容中特別有用。 其中一個範例是，如果條件約束放在 UI 的某個欄位上，則會根據目前選取的另一個 **非可見** 欄位值而定。 在此情況下， `coalesce()` 可以在頁面載入時間用來允許函式在語法上有效，同時在使用者與欄位互動時有所要的效果。

請考慮這 `DropDown` 種情況，讓使用者可以從數種不同的資料庫類型進行選擇：

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

若要在此欄位的目前選擇值上條件出另一個欄位的動作，請使用 `coalesce()` ，如下所示：

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

這是必要的，因為一 `databaseType` 開始並不會顯示，因此沒有值。 這會導致整個運算式無法正確進行評估。

## <a name="equals"></a>equals

如果這兩個參數有相同的類型和值，則傳回 `true`。 此函式支援所有 JSON 資料類型。

下列範例會傳回 `true`：

```json
"[equals(0, 0)]"
```

下列範例會傳回 `true`：

```json
"[equals('web', 'web')]"
```

下列範例會傳回 `false`：

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

## <a name="greater"></a>greater

如果第一個參數大於第二個參數，則傳回 `true`。 此函式只支援數值和字串類型的參數。

下列範例會傳回 `false`：

```json
"[greater(1, 2)]"
```

下列範例會傳回 `true`：

```json
"[greater('9', '10')]"
```

## <a name="greaterorequals"></a>greaterOrEquals

如果第一個參數大於或等於第二個參數，則傳回 `true`。 此函式只支援數值和字串類型的參數。

下列範例會傳回 `true`：

```json
"[greaterOrEquals(2, 2)]"
```

## <a name="if"></a>if

根據條件是 true 或 false 傳回值。 第一個參數是要測試的條件。 如果條件為 true，則第二個參數是要傳回的值。 第三個參數是條件為 false 時要傳回的值。

下列範例會傳回 `yes` 。

```json
"[if(equals(42, mul(6, 7)), 'yes', 'no')]"
```

## <a name="less"></a>less

如果第一個參數小於第二個參數，則傳回 `true`。 此函式只支援數值和字串類型的參數。

下列範例會傳回 `true`：

```json
"[less(1, 2)]"
```

下列範例會傳回 `false`：

```json
"[less('9', '10')]"
```

## <a name="lessorequals"></a>lessOrEquals

如果第一個參數小於或等於第二個參數，則傳回 `true`。 此函式只支援數值和字串類型的參數。

下列範例會傳回 `true`：

```json
"[lessOrEquals(2, 2)]"
```

## <a name="not"></a>not

如果參數評估為 `false`，則傳回 `true`。 此函式只支援布林值類型的參數。

下列範例會傳回 `true`：

```json
"[not(false)]"
```

下列範例會傳回 `false`：

```json
"[not(equals(0, 0))]"
```

## <a name="or"></a>或

如果至少一個參數評估為 `true`，則傳回 `true`。 此函式只支援布林值類型的兩個或多個參數。

下列範例會傳回 `true`：

```json
"[or(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

下列範例會傳回 `true`：

```json
"[or(equals(0, 0), greater(1, 2))]"
```

## <a name="next-steps"></a>接下來的步驟

* 如需 Azure Resource Manager 的簡介，請參閱 [Azure Resource Manager 概觀](../management/overview.md)。
