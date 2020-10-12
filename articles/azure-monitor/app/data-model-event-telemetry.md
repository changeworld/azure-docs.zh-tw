---
title: Azure Application Insights 遙測資料模型 - 事件遙測 | Microsoft Docs
description: 事件遙測的 Application Insights 資料模型
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 69685afa14352a22b58bccbea342038e4273696e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320607"
---
# <a name="event-telemetry-application-insights-data-model"></a>事件遙測：Application Insights 資料模型

您可以建立事件遙測項目 (在 [Application Insights](./app-insights-overview.md) 中) 來代表發生在您應用程式中的事件。 通常它是與使用者互動的事件，例如按一下按鈕或簽出訂單。 它也可以是初始化或組態更新等應用程式生命週期事件。 

事件在語意上不一定會與要求相互關聯。 不過，如果使用得當，事件遙測比要求或追蹤更重要。 事件代表商務遙測，且應受限於個別、較不積極的 [取樣](./api-filtering-sampling.md)。

## <a name="name"></a>名稱

事件名稱。 若要有適當的分組與實用的計量，請限制應用程式，使其產生少量的個別事件名稱。 例如，針對每個產生的事件執行個體，不要使用不同的名稱。

最大長度︰512 個字元

## <a name="custom-properties"></a>自訂屬性

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>自訂度量

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>接下來的步驟

- 如需 Application Insights 類型和資料模型，請參閱[資料模型](data-model.md)。
- [撰寫自訂事件遙測](./api-custom-events-metrics.md#trackevent)
- 查看 Application Insights 支援的[平台](./platforms.md)。

