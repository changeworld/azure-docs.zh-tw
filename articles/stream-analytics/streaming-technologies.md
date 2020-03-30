---
title: 在 Azure 上選擇即時和流處理解決方案
description: 瞭解如何選擇正確的即時分析和流處理技術在 Azure 上構建應用程式。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 2146b1bd782aba5d98729a2d37d956744e469ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860243"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>在 Azure 上選擇即時分析和流處理技術

Azure 上有多種服務可用於即時分析和流處理。 本文提供了確定哪種技術最適合您的應用程式所需的資訊。

## <a name="when-to-use-azure-stream-analytics"></a>何時使用 Azure 流分析

Azure 流分析是 Azure 上流分析的推薦服務。 它適用于各種方案，包括但不限於：

* 用於資料視覺化的儀表板
* 來自時空模式或異常的即時[警報](stream-analytics-set-up-alerts.md)
* 擷取、轉換、載入 (ETL)
* [事件採購模式](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

將 Azure 流分析作業添加到應用程式是使用您已經知道的 SQL 語言在 Azure 中啟動和運行流分析的最快方法。 Azure 流分析是一項作業服務，因此您不必花時間管理群集，也不必擔心在作業級別使用 99.9% 的 SLA 停機。 計費也在作業級別完成，使啟動成本降低（一個流式處理單元），但可擴展（最多 192 個流式處理單元）。 與運行和維護群集相比，運行一些流分析作業更具成本效益。

Azure 流分析具有豐富的開箱即用體驗。 您可以立即利用以下功能，而無需任何其他設置：

* 內置時態運算子，如[視窗聚合](stream-analytics-window-functions.md)、時態聯接和時態分析函數。
* 本機 Azure[輸入](stream-analytics-add-inputs.md)和[輸出](stream-analytics-define-outputs.md)配接器
* 支援緩慢變化[的參考資料](stream-analytics-use-reference-data.md)（也稱為查閱資料表），包括與地理空間參考資料聯接，用於地理圍欄。
* 集成解決方案，如[異常檢測](stream-analytics-machine-learning-anomaly-detection.md)
* 同一查詢中的多個時間視窗
* 能夠以任意順序組合多個時間運算子。
* 從到達事件中心的輸入到事件中心輸出著陸（包括從事件中心到事件中心的網路延遲）的 100 毫秒端到端延遲以下，輸送量持續高

## <a name="when-to-use-other-technologies"></a>何時使用其他技術

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>您希望以 JavaScript 或 C 以外的語言編寫 UdF、UDA 和自訂反序列化器#

Azure 流分析支援 JavaScript 中用於雲作業的使用者定義函數 （UDF） 或使用者定義的聚合 （UDA），支援 IoT Edge 作業的 C#。 還支援 C# 使用者定義的反序列化器。 如果要在其他語言（如 JAVA 或 Python）中實現反序列化器、UDF 或 UDA，則可以使用 Spark 結構化流式處理。 您還可以在您自己的虛擬機器上運行事件中心**事件處理器Host，** 以執行任意流式處理。

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>您的解決方案位於多雲或本地環境中

Azure 流分析是 Microsoft 的專有技術，僅在 Azure 上可用。 如果您需要解決方案在雲或本地之間可移植，請考慮開源技術，如 Spark 結構化流式處理或風暴。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 門戶創建流分析作業](stream-analytics-quick-create-portal.md)
* [使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [使用視覺化工作室創建流分析作業](stream-analytics-quick-create-vs.md)
* [使用視覺化工作室代碼創建流分析作業](quick-create-vs-code.md)
