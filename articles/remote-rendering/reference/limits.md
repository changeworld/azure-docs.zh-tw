---
title: 限制
description: SDK 功能的程式碼限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417686"
---
# <a name="limitations"></a>限制

有一些功能具有大小、計數或其他限制。

## <a name="azure-frontend"></a>Azure 前端

* 每個進程的 AzureFrontend 實例總數：16。
* 每個 AzureFrontend 的 AzureSession 實例總數：16。

## <a name="objects"></a>物件

* 單一類型（實體、CutPlaneComponent 等）的允許物件總計：16777215。
* 允許的現用切割平面總計：8。

## <a name="materials"></a>材質

* 資產中允許的材質總計：65535。

## <a name="overall-number-of-polygons"></a>多邊形的整體數目

所有已載入的模型可允許的多邊形數目取決於傳遞至[會話管理 REST API](../how-tos/session-rest-api.md#create-a-session)的 VM 大小：

| VM 大小 | 多邊形數目上限 |
|:--------|:------------------|
|標準| 20000000 |
|進階| 沒有限制 |


## <a name="platform-limitations"></a>平臺限制

**Windows 10 desktop**

* 不支援來自 Unity 的「獨立電腦」部署。 請改用 UWP。
* UWP/x86 是唯一支援的 UWP 平臺。 不支援 UWP/x64。

**Hololens 2**

* 不支援[從 PV 攝影機功能呈現](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)。
