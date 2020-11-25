---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001140"
---
`ApplicationInsights` 設定可讓您將 [Azure Application Insights](/azure/application-insights) 遙測支援新增至容器。 Application Insights 可提供深入容器監視。 您可輕鬆監視容器的可用性、效能和使用情形。 您也可以快速識別並診斷容器中的錯誤。

下表說明 `ApplicationInsights` 區段下所支援的組態設定。

|必要| 名稱 | 資料類型 | 描述 |
|--|------|-----------|-------------|
|否| `InstrumentationKey` | String | Application Insights 執行個體的檢測金鑰，容器的遙測資料會傳送到這裡。 如需詳細資訊，請參閱 [ASP.NET Core 的 Application Insights](../articles/azure-monitor/app/asp-net-core.md)。 <br><br>範例：<br>`InstrumentationKey=123456789`|