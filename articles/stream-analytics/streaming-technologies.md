---
title: 選擇 Azure 上的即時和串流處理解決方案
description: 瞭解如何選擇正確的即時分析和串流處理技術，在 Azure 上建立您的應用程式。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 4c10a91971357001723adcb783253c9867cf6d87
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019052"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>選擇 Azure 上的即時分析和串流處理技術

有數項服務可用於 Azure 上的即時分析和串流處理。 本文提供您所需的資訊，以決定哪一種技術最適合您的應用程式。

## <a name="when-to-use-azure-stream-analytics"></a>使用 Azure 串流分析的時機

Azure 串流分析是 Azure 上的串流分析建議的服務。 它適用于各種案例，包括但不限於：

* 資料視覺效果的儀表板
* 時態和空間模式或異常的即時[警示](stream-analytics-set-up-alerts.md)
* 擷取、轉換、載入 (ETL)
* [事件來源模式](/azure/architecture/patterns/event-sourcing)
* [IoT Edge](stream-analytics-edge.md)

將 Azure 串流分析作業新增至您的應用程式，是使用您熟悉的 SQL 語言，在 Azure 中啟動串流分析並執行的最快方式。 Azure 串流分析是一項作業服務，因此您不必花時間管理叢集，也不必擔心在作業層級有 99.9% SLA 的停機時間。 您也可以在作業層級進行計費，讓啟動成本低 (一個串流單位) ，但可調整 (高達192串流單位) 。 執行一些串流分析作業比執行和維護叢集更符合成本效益。

Azure 串流分析有豐富的現成體驗。 您可以立即利用下列功能，而不需要進行任何額外的設定：

* 內建的時態性運算子，例如 [視窗型匯總](stream-analytics-window-functions.md)、時態性聯結和時態性分析函數。
* 原生 Azure [輸入](stream-analytics-add-inputs.md) 和 [輸出](stream-analytics-define-outputs.md) 介面卡
* 支援緩慢變更的 [參考資料](stream-analytics-use-reference-data.md) (也稱為查閱資料表) ，包括與地理柵欄的地理空間參考資料聯結。
* 整合式解決方案，例如 [異常偵測](stream-analytics-machine-learning-anomaly-detection.md)
* 相同查詢中的多個時間視窗
* 以任意順序撰寫多個時態運算子的能力。
* 在 100-ms 端對端延遲進入事件中樞抵達的輸入，到事件中樞的輸出登陸，包括事件中樞的網路延遲，以及持續高輸送量

## <a name="when-to-use-other-technologies"></a>使用其他技術的時機

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>您想要以非 JavaScript 或 C 以外的語言撰寫 Udf、Uda 和自訂還原序列化程式#

Azure 串流分析支援 (UDF) 的使用者定義函數或使用者定義的匯總 (UDA) 適用于雲端作業的 JavaScript 和 c # 的 IoT Edge 作業。 也支援 c # 使用者定義還原序列化程式。 如果您想要執行還原序列化、UDF 或其他語言的 UDA，例如 JAVA 或 Python，您可以使用 Spark 結構化串流。 您也可以在自己的虛擬機器上執行事件中樞 **EventProcessorHost** ，以執行任意串流處理。

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>您的解決方案是在多雲端或內部部署環境中

Azure 串流分析是 Microsoft 的專屬技術，僅適用于 Azure。 如果您需要在雲端或內部部署之間可移植的解決方案，請考慮開放原始碼技術，例如 Spark 結構化串流或風暴。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)
* [使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)
* [使用 Visual Studio 建立串流分析作業](stream-analytics-quick-create-vs.md)
* [使用 Visual Studio Code 建立串流分析作業](quick-create-visual-studio-code.md)
