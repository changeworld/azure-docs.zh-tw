---
title: Azure 監視器 Application Insights 相依性資料模型
description: 相依性遙測的 Application Insights 資料模型
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0f9fc96479569c3411024068ed614d422035ab17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87315966"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>相依性遙測：Application Insights 資料模型

相依性遙測 (在 [Application Insights](./app-insights-overview.md) 中) 代表受監視元件與遠端元件 (例如 SQL 或 HTTP 端點) 的互動。

## <a name="name"></a>名稱

使用此相依性呼叫所起始之命令的名稱。 基數值低。 範例為預存程序名稱和 URL 路徑範本。

## <a name="id"></a>識別碼

相依性呼叫執行個體的識別碼。 用來與此相依性呼叫的對應要求遙測項目相互關聯。 如需詳細資訊，請參閱[相互關聯](./correlation.md)頁面。

## <a name="data"></a>資料

此相依性呼叫所起始的命令。 範例為搭配所有查詢參數的 SQL 陳述式和 HTTP URL。

## <a name="type"></a>類型

相依性類型名稱。 相依性邏輯群組和其他欄位 (如 commandName 和 resultCode) 解譯的基數值較低。 範例為 SQL、Azure 資料表和 HTTP。

## <a name="target"></a>目標

相依性呼叫的目標網站。 範例為伺服器名稱、主機位址。 如需詳細資訊，請參閱[相互關聯](./correlation.md)頁面。

## <a name="duration"></a>持續時間

要求持續時間格式為︰`DD.HH:MM:SS.MMMMMM`。 必須小於 `1000` 天。

## <a name="result-code"></a>結果碼

相依性呼叫的結果碼。 範例為 SQL 錯誤碼與 HTTP 狀態碼。

## <a name="success"></a>成功

表示成功或失敗的呼叫。

## <a name="custom-properties"></a>自訂屬性

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>自訂度量

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>接下來的步驟

- 設定 [.NET](./asp-net-dependencies.md) 的相依性追蹤。
- 設定 [Java](./java-agent.md) 的相依性追蹤。
- [撰寫自訂相依性遙測](./api-custom-events-metrics.md#trackdependency)
- 如需 Application Insights 類型和資料模型，請參閱[資料模型](data-model.md)。
- 查看 Application Insights 支援的[平台](./platforms.md)。

