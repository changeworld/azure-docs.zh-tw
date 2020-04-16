---
title: 限制
description: SDK 功能的代碼限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417686"
---
# <a name="limitations"></a>限制

許多功能具有大小、計數或其他限制。

## <a name="azure-frontend"></a>Azure 前端

* 每個進程的總 Azure Frontend 實例數:16。
* 每個 Azure 前端的 Azure 會話實例總數:16。

## <a name="objects"></a>物件

* 單個類型(實體、CutPlane元件等)的允許物件總數:16,777,215。
* 允許的活動切割平面總數:8。

## <a name="materials"></a>材質

* 資產中允許的材料總數:65,535。

## <a name="overall-number-of-polygons"></a>多邊形的總數

所有載入的模型的允許多邊形數來使用[工作階段管理 REST API](../how-tos/session-rest-api.md#create-a-session)的 VM 的大小:

| VM 大小 | 最大面數 |
|:--------|:------------------|
|標準| 2000萬 |
|溢價| 沒有限制 |


## <a name="platform-limitations"></a>平台限制

**視窗 10 桌面**

* 不支援從 Unity 部署「PC 獨立」部署。 改用 UWP。
* UWP/x86 是唯一受支援的 UWP 平臺。 不支援 UWP/x64。

**霍洛倫斯 2**

* 不支援[從 PV 攝影機功能的渲染](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)。
