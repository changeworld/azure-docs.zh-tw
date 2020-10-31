---
title: Azure 串流分析預覽功能
description: 本文列出目前處於預覽狀態的 Azure 串流分析功能。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 8/07/2020
ms.openlocfilehash: 4179b06759802025f97bd32a355b788c96c9eddb
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123315"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure 串流分析預覽功能

本文摘要說明目前處於預覽狀態的所有 Azure 串流分析功能。 生產環境不建議使用預覽功能。

## <a name="public-previews"></a>公開預覽

下列功能目前處於公開預覽狀態。 您目前可以利用這些功能，但請勿將其用於生產環境。

### <a name="authenticate-to-sql-database-output-with-managed-identities"></a>使用受控識別向 SQL Database 輸出進行驗證

Azure 串流分析支援 Azure SQL Database 輸出接收的[受控識別驗證](../active-directory/managed-identities-azure-resources/overview.md)。 受控識別消除了以使用者為基礎的驗證方法限制，例如因密碼變更而需要重新驗證。 

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>使用由 Azure Machine Learning 管理的自訂 ML 模型進行即時高效能評分

Azure 串流分析使用不需要撰寫程式碼的工作流程，藉由利用 Azure Machine Learning 所管理的自訂預先定型 Machine Learning 模型，並裝載在 Azure Kubernetes Service (AKS) 或 Azure 容器執行個體 (ACI) 中，來支援高效能的即時評分。 [註冊](https://aka.ms/asapreview1)預覽

### <a name="c-custom-de-serializers"></a>C# 自訂還原序列化程式
開發人員可以利用 Azure 串流分析的能力，以 Protobuf、XML 或任何自訂格式處理資料。 您可以在 C# 中實作[自訂還原序列化程式](custom-deserializer-examples.md)，然後將其用來還原序列化 Azure 串流分析所收到的事件。

### <a name="extensibility-with-c-custom-code"></a>使用 C# 自訂程式碼的擴充性

在雲端或 IoT Edge 上建立串流分析模組的開發人員可以撰寫或重複使用自訂 C# 函式，並透過[使用者定義的函式](stream-analytics-edge-csharp-udf-methods.md)，直接在查詢中叫用這些函式。

### <a name="debug-query-steps-in-visual-studio"></a>Visual Studio 中的偵錯查詢步驟

在適用於 Visual Studio 的 Azure 串流分析工具中進行本機測試時，您可以輕鬆地預覽資料圖表上的中繼資料列集。 


### <a name="live-data-testing-in-visual-studio"></a>在 Visual Studio 中即時測試資料

Azure 串流分析的 Visual Studio 工具加強了本機測試功能，讓您可以針對雲端來源 (例如，事件中樞或 IoT 中樞) 的即時事件串流查詢進行測試。 了解如何[使用適用於 Visual Studio 的 Azure 串流分析工具在本機測試即時資料](stream-analytics-live-data-local-testing.md)。

### <a name="visual-studio-code-for-azure-stream-analytics"></a>適用於 Azure 串流分析的 Visual Studio Code

Azure 串流分析作業可以在 Visual Studio Code 中撰寫。 請參閱 [VS Code 入門教學課程](./quick-create-visual-studio-code.md)。

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>使用 Visual Studio Code 中即時資料的本機測試

您可以對本機電腦上的即時資料測試查詢，然後再將作業提交至 Azure。 每個測試反覆項目平均需要二到三秒以內，因而產生非常有效率的開發程序。

## <a name="other-previews"></a>其他預覽

下列功能也會在要求時提供預覽。

### <a name="support-for-azure-stack"></a>支援 Azure Stack
這項功能會在 Azure IoT Edge 執行階段上啟用，利用自訂 Azure Stack 功能，例如本機輸入的原生支援，以及在 Azure Stack 上執行的輸出 (例如事件中樞、IoT 中樞、Blob 儲存體)。 這個新的整合可讓您建置混合式架構，以便在接近資料產生的位置分析資料，進而降低延遲並將深入解析發揮到極致。
這項功能可讓您建立混合式架構，以便在接近資料產生的位置分析資料，進而降低延遲並將深入解析發揮到極致。 您必須[註冊](https://aka.ms/asapreview1)此預覽。