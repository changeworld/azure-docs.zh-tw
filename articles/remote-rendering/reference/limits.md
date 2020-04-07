---
title: 限制
description: SDK 功能的代碼限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6e23c716a965cdf178c9f847900e66c141d5d7eb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680332"
---
# <a name="limits"></a>限制

由於正在運行的系統的內部詳細資訊,許多功能具有大小或計數限制。

## <a name="azure-frontend"></a>Azure 前端

* 每個進程的總 Azure Frontend 實例數:16。
* 每個 Azure 前端的 Azure 會話實例總數:16。

## <a name="objects"></a>物件

* 單個類型(實體、CutPlane元件等)的允許物件總數:16,777,215。
* 允許的活動切割平面總數:8。

## <a name="materials"></a>物料

* 資產中允許的材料總數:65,535。

## <a name="overall-number-of-polygons"></a>多邊形的總數

所有載入的模型的允許多邊形數來使用[工作階段管理 REST API](../how-tos/session-rest-api.md#create-a-session)的 VM 的大小:

| VM 大小 | 最大面數 |
|:--------|:------------------|
|標準| 2000萬 |
|溢價| 沒有限制 |



