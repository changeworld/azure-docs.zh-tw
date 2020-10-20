---
title: 限制
description: SDK 功能的程式碼限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b500a3777ee24d6615022dae2571d021bd0d675a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201166"
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

* **動畫：** 動畫僅限用來製作遊戲物件的個別轉換動畫。 不支援具有外觀或頂點動畫的框架動畫。 不會保留來源資產檔案的動畫追蹤。 相反地，物件轉換動畫必須由用戶端程式代碼驅動。
* **自訂著色器：** 不支援撰寫自訂著色器。 只能使用內建的 [色材質](../overview/features/color-materials.md) 或 [.pbr 材質](../overview/features/pbr-materials.md) 。
* 資產中**的相異材質數目上限**：65535。 如需有關自動減少資料計數的詳細資訊，請參閱「 [資料重復資料](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) 刪除」一章。
* **單一材質的最大維度**： 16384 x 16384。 轉換程式會減少較大來源紋理的大小。

### <a name="overall-number-of-polygons"></a>多邊形的整體數目

所有已載入模型允許的多邊形數目取決於傳遞給[工作階段管理 REST API](../how-tos/session-rest-api.md#create-a-session) 的 VM 大小：

| 伺服器大小 | 多邊形的數目上限 |
|:--------|:------------------|
|標準| 2 千萬 |
|進階| 沒有限制 |

如需這項限制的詳細資訊，請參閱 [伺服器大小](../reference/vm-sizes.md) 一章。

## <a name="platform-limitations"></a>平台限制

**Windows 10 Desktop**

* Win32/x64 是唯一支援的 Win32 平臺。 不支援 Win32/x86。

**HoloLens 2**

* 不支援[從 PV 相機轉譯](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)功能。