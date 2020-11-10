---
title: 限制
description: SDK 功能的程式碼限制
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 33f5314c80dc33dbec50dc21a71f4cb507979e12
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427423"
---
# <a name="limitations"></a>限制

一些功能有大小、計數或其他限制。

## <a name="azure-frontend"></a>Azure Frontend

下列限制適用于 (c + + 和 c # ) 的前端 API：
* 每個進程的 [AzureFrontend](/dotnet/api/microsoft.azure.remoterendering.azurefrontend) 實例總數：16。
* 每個[AzureFrontend](/dotnet/api/microsoft.azure.remoterendering.azurefrontend)的[AzureSession](/dotnet/api/microsoft.azure.remoterendering.azuresession)實例總數：16。

## <a name="objects"></a>物件

* 單一類型 ([實體](../concepts/entities.md)、 [CutPlaneComponent](../overview/features/cut-planes.md)等的允許物件總數 ) ：16777215。
* 允許的使用中切割平面總計：8.

## <a name="geometry"></a>幾何形狀

* **動畫：** 動畫僅限用來製作 [遊戲物件](../concepts/entities.md)的個別轉換動畫。 不支援具有外觀或頂點動畫的框架動畫。 不會保留來源資產檔案的動畫追蹤。 相反地，物件轉換動畫必須由用戶端程式代碼驅動。
* **自訂著色器：** 不支援撰寫自訂著色器。 只能使用內建的 [色材質](../overview/features/color-materials.md) 或 [.pbr 材質](../overview/features/pbr-materials.md) 。
* 資產中 **的相異材質數目上限** ：65535。 如需有關自動減少資料計數的詳細資訊，請參閱「 [資料重復資料](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) 刪除」一章。
* **單一材質的最大維度** ： 16384 x 16384。 轉換程式會減少較大來源紋理的大小。

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