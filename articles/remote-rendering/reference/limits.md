---
title: 限制
description: SDK 功能的程式碼限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 17f98f452764abdc8458cdc38661d464ecb0a60d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808552"
---
# <a name="limitations"></a>限制

一些功能有大小、計數或其他限制。

## <a name="azure-frontend"></a>Azure Frontend

* 每個程序的 AzureFrontend 執行個體總計：16.
* 每個 AzureFrontend 的 AzureSession 執行個體總計：16.

## <a name="objects"></a>物件

* 單一類型的允許物件總計 (Entity、CutPlaneComponent 等等)：16,777,215。
* 允許的使用中切割平面總計：8.

## <a name="geometry"></a>幾何

* 資產中允許的材質總計：65,535。
* 單一材質的最大維度： 16384 x 16384。 較大的來源材質會依照轉換程式相應縮小。

## <a name="overall-number-of-polygons"></a>多邊形的整體數目

所有已載入模型允許的多邊形數目取決於傳遞給[工作階段管理 REST API](../how-tos/session-rest-api.md#create-a-session) 的 VM 大小：

| VM 大小 | 多邊形的數目上限 |
|:--------|:------------------|
|標準| 2 千萬 |
|進階| 沒有限制 |

如需這項限制的詳細資訊，請參閱[VM 大小](../reference/vm-sizes.md)一章。

## <a name="platform-limitations"></a>平台限制

**Windows 10 Desktop**

* UWP/x86 是唯一支援的 UWP 平台。 不支援 UWP/x64。
* Win32/x64 是唯一支援的 Win32 平臺。 不支援 Win32/x86。

**Hololens 2**

* 不支援[從 PV 相機轉譯](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)功能。
