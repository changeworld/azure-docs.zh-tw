---
title: 建立 UI 定義日期函式
description: 描述處理日期值時所要使用的函數。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096236"
---
# <a name="createuidefinition-date-functions"></a>CreateUiDefinition date 函數

處理日期值時所要使用的函數。

## <a name="addhours"></a>addHours

將指定的時間戳記加上整數小時數。

下列範例會傳回 `"1991-01-01T00:59:59.000Z"`：

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addMinutes

將指定的時間戳記加上整數分鐘數。

下列範例會傳回 `"1991-01-01T00:00:59.000Z"`：

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addSeconds
將指定的時間戳記加上整數秒數。

下列範例會傳回 `"1991-01-01T00:00:00.000Z"`：

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

傳回本機電腦上目前日期和時間之 ISO 8601 格式的字串。

下列範例可能會傳回 `"1990-12-31T23:59:59.000Z"`：

```json
"[utcNow()]"
```

## <a name="next-steps"></a>後續步驟

* 如需 Azure Resource Manager 的簡介，請參閱 [Azure Resource Manager 概觀](../management/overview.md)。
