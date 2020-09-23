---
title: 限制
description: SDK 功能的程式碼限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: e6b12c2bac4a9732f868f6a6ac3491ef993f54c3
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90976565"
---
# <a name="limitations"></a>限制

一些功能有大小、計數或其他限制。

## <a name="azure-frontend"></a>Azure Frontend

* 每個程序的 AzureFrontend 執行個體總計：16.
* 每個 AzureFrontend 的 AzureSession 執行個體總計：16.

## <a name="objects"></a>物件

* 單一類型的允許物件總計 (Entity、CutPlaneComponent 等等)：16,777,215。
* 允許的使用中切割平面總計：8.

## <a name="geometry"></a>幾何形狀

* 資產中允許的材質總計：65,535。 如需詳細資訊，請參閱 [資料消除重複](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) 。
* 單一材質的最大維度： 16384 x 16384。 較大的來源紋理將會由轉換程式縮小。

## <a name="overall-number-of-polygons"></a>多邊形的整體數目

所有已載入模型允許的多邊形數目取決於傳遞給[工作階段管理 REST API](../how-tos/session-rest-api.md#create-a-session) 的 VM 大小：

| 伺服器大小 | 多邊形的數目上限 |
|:--------|:------------------|
|標準| 2 千萬 |
|進階| 沒有限制 |

如需這項限制的詳細資訊，請參閱 [伺服器大小](../reference/vm-sizes.md) 一章。

## <a name="platform-limitations"></a>平台限制

**Windows 10 Desktop**

* Win32/x64 是唯一支援的 Win32 平臺。 不支援 Win32/x86。

**Hololens 2**

* 不支援[從 PV 相機轉譯](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)功能。
