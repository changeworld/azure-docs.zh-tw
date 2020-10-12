---
title: 建立 UI 定義日期函數
description: 描述使用日期值時要使用的函數。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096236"
---
# <a name="createuidefinition-date-functions"></a>CreateUiDefinition date 函數

使用日期值時要使用的函數。

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

## <a name="next-steps"></a>接下來的步驟

* 如需 Azure Resource Manager 的簡介，請參閱 [Azure Resource Manager 概觀](../management/overview.md)。
