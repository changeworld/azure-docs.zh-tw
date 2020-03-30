---
title: Azure 流分析功能比較
description: 本文比較 Azure 門戶、視覺化工作室和視覺化工作室代碼中 Azure 流分析雲和 IoT Edge 作業支援的功能。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: cdae6a3b7319aefa9d4f19b5d613d1afb8b6804a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235315"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure 流分析功能比較

使用 Azure 流分析，您可以使用[Azure 門戶](stream-analytics-quick-create-portal.md)、[視覺化工作室](stream-analytics-quick-create-vs.md)和[視覺化工作室代碼](quick-create-vs-code.md)在雲和 IoT 邊緣創建流式處理解決方案。 本文中的表顯示了兩種作業類型的每個平臺都支援哪些功能。

## <a name="cloud-job-features"></a>雲作業功能


|功能  |入口網站  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|跨平台     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|腳本創作     |是         |是         |是         |
|腳本感知     |語法醒目提示         |語法醒目提示</br>代碼完成</br>錯誤標記         |語法醒目提示</br>代碼完成</br>錯誤標記         |
|定義所有類型的輸入、輸出和作業配置     |是         |是         |是         |
|原始檔控制     |否         |是         |是         |
|CI/CD 支援     |部分         |是         |是         |
|跨多個查詢共用輸入和輸出     |否         |是         |是         |
|使用示例檔的查詢測試     |是         |是        |是         |
|即時資料本地測試     |否         |是       |是      |
|列出作業和查看作業實體     |是         |是        |是         |
|將作業匯出到本地專案     |否         |是         |是         |
|提交、啟動和停止作業     |是         |是         |是         |
|查看作業指標和圖表     |是         |是         |在入口網站中開啟         |
|查看作業執行階段錯誤     |是         |是         |否         |
|診斷記錄     |是         |否         |否         |
|自訂消息屬性     |是         |是         |否       |
|C# 自訂代碼函數和反序列化器|唯讀模式|是|否|
|JavaScript UDF 和 UDA     |是         |是         |僅限 Windows         |
|機器學習服務     |是        |是         |否         |
|Machine Learning Studio     |是，但無法測試查詢        |是 |否         |
|相容性層級     |1.0</br>1.1</br>1.2 （預設）         |1.0</br>1.1</br>1.2 （預設）           |1.0</br>1.1</br>1.2 （預設）           |
|內置基於 ML 的異常檢測功能     |是         |是         |是         |
|內置地理空間功能     |是         |是         |是         |



## <a name="iot-edge-job-features"></a>IoT 邊緣作業功能

|功能  |入口網站  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|工作創作     |是         |是         |否         |
|原始檔控制     |否         |是         |否         |
|將作業匯出到本地專案     |否         |是         |否         |
|使用示例檔的查詢測試     |是         |是         |否         |
|跨多個查詢共用輸入和輸出     |否         |是         |否         |
|C# UDF     |否         |是         |否         |
|提交工作     |是         |是         |否         |
|列出作業和查看作業實體     |是         |是         |否         |
|查看作業指標和圖表     |是         |部分         |否         |
|查看作業執行階段錯誤     |是         |部分         |否         |
|CI/CD 支援     |否         |否         |否         |


## <a name="next-steps"></a>後續步驟

* [Azure IoT Edge 串流分析](stream-analytics-edge.md)
* [教程：為 Azure 流分析 IoT 邊緣作業編寫 C# 使用者定義的函數（預覽）](stream-analytics-edge-csharp-udf.md)
* [使用視覺化工作室工具開發流分析 IoT 邊緣作業](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [使用 Visual Studio 檢視 Azure 串流分析工作](stream-analytics-vs-tools.md)
* [使用視覺化工作室代碼流覽 Azure 流分析（預覽版）](visual-studio-code-explore-jobs.md)


