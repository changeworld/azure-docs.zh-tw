---
title: Azure 串流分析功能比較
description: 本文比較 Azure 門戶、可視化工作室和可視化工作室代碼中 Azure 流分析雲和 IoT Edge 作業支援的功能。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d3b27a50fa86916b71c84b30ecdbf45deb0ec45c
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770761"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure 串流分析功能比較

使用 Azure 串流分析,您可以使用[Azure 門戶](stream-analytics-quick-create-portal.md),[可視化工作室](stream-analytics-quick-create-vs.md)和[可視化工作室代碼](quick-create-vs-code.md)在雲端和 IoT 邊緣建立流式處理解決方案。 本文中的表顯示了兩種作業類型的每個平臺都支援哪些功能。

> [!NOTE]
> Visual Studio 和 Visual Studio 代碼工具不支援華東、華北、德國中部和德國東北部地區的工作。

## <a name="cloud-job-features"></a>雲作業功能


|功能  |入口網站  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|跨平台     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|文稿創作     |是         |是         |是         |
|文稿感知     |語法醒目提示         |語法醒目提示</br>代碼完成</br>錯誤標記         |語法醒目提示</br>代碼完成</br>錯誤標記         |
|定義所有類型的輸入、輸出和工作設定     |是         |是         |是         |
|原始檔控制     |否         |是         |是         |
|CI/CD 支援     |Partial         |是         |是         |
|跨多個查詢共享輸入和輸出     |否         |是         |是         |
|使用範例檔案查詢測試     |是         |是        |是         |
|即時資料本地測試     |否         |是       |是      |
|列出工作與檢視工作實體     |是         |是        |是         |
|匯出工作到本地端項目     |否         |是         |是         |
|提交、啟動及停止工作     |是         |是         |是         |
|檢視工作指標與圖表     |是         |是         |在入口網站中開啟         |
|檢視工作執行時發生錯誤     |是         |是         |否         |
|診斷記錄     |是         |否         |否         |
|自訂訊息屬性     |是         |是         |否       |
|C# 自訂代碼函數與反序列化器|唯讀模式|是|否|
|JavaScript UDF 和 UDA     |是         |是         |僅限 Windows         |
|機器學習服務     |是        |是         |否         |
|Machine Learning Studio     |是,但無法測試查詢        |是 |否         |
|相容性層級     |1.0</br>1.1</br>1.2 (預設)         |1.0</br>1.1</br>1.2 (預設)           |1.0</br>1.1</br>1.2 (預設)           |
|內建基於 ML 的例外偵測功能     |是         |是         |是         |
|內建地理空間功能     |是         |是         |是         |



## <a name="iot-edge-job-features"></a>IoT 邊緣工作功能

|功能  |入口網站  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|工作創作     |是         |是         |否         |
|原始檔控制     |否         |是         |否         |
|匯出工作到本地端項目     |否         |是         |否         |
|使用範例檔案查詢測試     |是         |是         |否         |
|跨多個查詢共享輸入和輸出     |否         |是         |否         |
|C# UDF     |否         |是         |否         |
|提交工作     |是         |是         |否         |
|列出工作與檢視工作實體     |是         |是         |否         |
|檢視工作指標與圖表     |是         |部分         |否         |
|檢視工作執行時發生錯誤     |是         |部分         |否         |
|CI/CD 支援     |否         |否         |否         |


## <a name="next-steps"></a>後續步驟

* [Azure IoT Edge 串流分析](stream-analytics-edge.md)
* [教學:為 Azure 串流分析 IoT 邊緣作業編寫 C# 使用者定義的函數(預覽)](stream-analytics-edge-csharp-udf.md)
* [使用視覺化工作室工具開發串流分析 IoT 邊緣作業](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [使用 Visual Studio 檢視 Azure 串流分析工作](stream-analytics-vs-tools.md)
* [使用視覺化工作室代碼瀏覽 Azure 串流分析(預覽版)](visual-studio-code-explore-jobs.md)


