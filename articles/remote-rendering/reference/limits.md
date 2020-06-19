---
title: 限制
description: SDK 功能的程式碼限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6a1a51ee09422607ae1392704add4d49d3367d57
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759042"
---
# <a name="limitations"></a>限制

一些功能有大小、計數或其他限制。

## <a name="azure-frontend"></a>Azure Frontend

* 每個程序的 AzureFrontend 執行個體總計：16.
* 每個 AzureFrontend 的 AzureSession 執行個體總計：16.

## <a name="objects"></a>物件

* 單一類型的允許物件總計 (Entity、CutPlaneComponent 等等)：16,777,215。
* 允許的使用中切割平面總計：8.

## <a name="materials"></a>材質

* 資產中允許的材質總計：65,535。

## <a name="overall-number-of-polygons"></a>多邊形的整體數目

所有已載入模型允許的多邊形數目取決於傳遞給[工作階段管理 REST API](../how-tos/session-rest-api.md#create-a-session) 的 VM 大小：

| VM 大小 | 多邊形的數目上限 |
|:--------|:------------------|
|標準| 2 千萬 |
|進階| 沒有限制 |


## <a name="platform-limitations"></a>平台限制

**Windows 10 Desktop**

* UWP/x86 是唯一支援的 UWP 平台。 不支援 UWP/x64。

**Hololens 2**

* 不支援[從 PV 相機轉譯](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)功能。
