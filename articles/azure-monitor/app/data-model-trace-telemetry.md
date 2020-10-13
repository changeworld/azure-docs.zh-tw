---
title: Azure 應用程式 Insights 資料模型-追蹤遙測
description: 追蹤遙測的 Application Insights 資料模型
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8fea4bbf590816b2ef168a2ed16f197389ee282e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320539"
---
# <a name="trace-telemetry-application-insights-data-model"></a>追蹤遙測：Application Insights 資料模型

追蹤遙測 (在 [Application Insights](./app-insights-overview.md) 中) 代表以文字搜尋的 `printf` 樣式追蹤陳述式。 `Log4Net`、`NLog`和其他以文字為基礎的記錄檔項目會轉譯成此類型的執行個體。 追蹤沒有作為擴充性的度量。

## <a name="message"></a>訊息

追蹤訊息。

最大長度︰32768 個字元

## <a name="severity-level"></a>嚴重性層級

追蹤嚴重性層級。 值可為 `Verbose`、`Information`、`Warning`、`Error`、`Critical`。

## <a name="custom-properties"></a>自訂屬性

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>接下來的步驟

- [探索 Application Insights 中的 .net 追蹤記錄](./asp-net-trace-logs.md)。
- [探索 Application Insights 中的 JAVA 追蹤記錄](./java-trace-logs.md)。
- 如需 Application Insights 類型和資料模型，請參閱[資料模型](data-model.md)。
- [撰寫自訂追蹤遙測](./api-custom-events-metrics.md#tracktrace)
- 查看 Application Insights 支援的[平台](./platforms.md)。

