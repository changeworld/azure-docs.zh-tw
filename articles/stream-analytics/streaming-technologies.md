---
title: 選擇 Azure 上的即時和串流處理解決方案
description: 瞭解如何選擇正確的即時分析和串流處理技術，在 Azure 上建立您的應用程式。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 2146b1bd782aba5d98729a2d37d956744e469ba1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75860243"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>選擇 Azure 上的即時分析和串流處理技術

有數項服務可在 Azure 上進行即時分析和串流處理。 本文提供您決定哪一種技術最適合您的應用程式時所需的資訊。

## <a name="when-to-use-azure-stream-analytics"></a>使用 Azure 串流分析的時機

Azure 串流分析是 Azure 上串流分析的建議服務。 其適用于各種案例，包括但不限於：

* 資料視覺效果的儀表板
* 來自時態性和空間模式或異常的即時[警示](stream-analytics-set-up-alerts.md)
* 擷取、轉換、載入 (ETL)
* [事件來源模式](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

將 Azure 串流分析作業新增至您的應用程式是使用您熟悉的 SQL 語言，在 Azure 中啟動並執行串流分析的最快方式。 Azure 串流分析是一種作業服務，因此您不需要花時間管理叢集，也不必擔心在作業層級 99.9% SLA 的停機。 計費也會在作業層級完成，讓啟動成本變低（一個串流單位），但可調整（最多192個串流單位）。 執行幾個串流分析作業比執行和維護叢集更符合成本效益。

Azure 串流分析有豐富的現成體驗。 您可以立即利用下列功能，而不需要進行任何額外的設定：

* 內建的時態運算子，例如[視窗型匯總](stream-analytics-window-functions.md)、時態性聯結和時態性分析函數。
* 原生 Azure[輸入](stream-analytics-add-inputs.md)和[輸出](stream-analytics-define-outputs.md)介面卡
* 支援緩慢變更的[參考資料](stream-analytics-use-reference-data.md)（也稱為查閱資料表），包括與地理柵欄的地理空間參考資料聯結。
* 整合式解決方案，例如[異常偵測](stream-analytics-machine-learning-anomaly-detection.md)
* 相同查詢中的多個時間視窗
* 能夠以任意順序撰寫多個時態運算子。
* 從抵達事件中樞的輸入到 100-ms 端對端延遲，到事件中樞的輸出登陸，包括從和到事件中樞的網路延遲，持續高輸送量

## <a name="when-to-use-other-technologies"></a>使用其他技術的時機

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>您想要以 JavaScript 或 C 以外的語言撰寫 Udf、Uda 和自訂還原序列化程式#

Azure 串流分析支援適用于雲端作業的 JavaScript 中的使用者定義函數（UDF）或使用者定義匯總（UDA），以及適用于 IoT Edge 作業的 c #。 也支援 c # 使用者定義的還原序列化程式。 如果您想要以其他語言（例如 JAVA 或 Python）來執行還原序列化、UDF 或 UDA，您可以使用 Spark 結構化串流。 您也可以在自己的虛擬機器上執行事件中樞**EventProcessorHost** ，以執行任意的串流處理。

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>您的解決方案是在多雲端或內部部署環境中

Azure 串流分析是 Microsoft 的專屬技術，僅適用于 Azure。 如果您需要您的解決方案可在雲端或內部部署中移植，請考慮開放原始碼技術，例如 Spark 結構化串流或風暴。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [使用 Visual Studio 建立串流分析作業](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio Code 建立串流分析作業](quick-create-vs-code.md)
