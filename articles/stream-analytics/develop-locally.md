---
title: 在本地開發與除錯 Azure 串流分析作業
description: 瞭解如何在 Azure 門戶中運行 Azure 流分析作業之前,在本地電腦上開發和測試它們。
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879839"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>在本地開發與除錯 Azure 串流分析作業

雖然您可以在 Azure 門戶中創建和測試 Azure 流分析作業,但許多開發人員更喜歡本地開發體驗。 串流分析使用您喜愛的程式碼編輯器和開發工具使用 Azure 事件中心、IoT 中心和 Blob 儲存的即時事件串流使用完全功能的單節點本地執行時輕鬆建立和測試作業。 還可以直接從本地開發環境向 Azure 提交作業。

## <a name="local-development-environments"></a>本機開發環境

在本地電腦上開發流分析作業的方式取決於您的工具首選項和功能可用性。 請參閱[Azure 流分析功能比較](feature-comparison.md),瞭解每個開發環境支援哪些功能。

下表中的環境支援本機開發：

|環境                              |描述    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| Visual Studio Code 的[Azure 串流分析工具擴展](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa)允許您使用豐富的 IntelliSense 和本機原始程式碼控制,在本地和雲端中創作、管理和測試流分析作業。 支援 Linux、MacOS 和 Windows 上的開發。 要瞭解更多資訊,請參閱[在可視化工作室代碼中建立 Azure 串流分析作業](quick-create-vs-code.md)。|
|[視覺工作室 2019](stream-analytics-tools-for-visual-studio-install.md) |流分析工具是可視化工作室中的 Azure 開發和數據存儲和處理工作負載的一部分。 您可以使用 Visual Studio 撰寫自訂 C# 使用者定義的函數和反序列化器。 要瞭解更多資訊,請參閱[使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)。|
|[命令提示字元或終端機](stream-analytics-tools-for-visual-studio-cicd.md)|Azure 串流分析 CI/CD NuGet 套件為 Visual Studio 專案生成、任意電腦上的本地測試提供了工具。 Azure 串流分析 CI/CD npm 包為任意電腦上的視覺工作室代碼專案生成(生成 Azure 資源管理員範本)提供工具。|

## <a name="next-steps"></a>後續步驟

* [使用視覺化工作室代碼使用範例資料在本地測試串流分析查詢](visual-studio-code-local-run.md)
* [使用視覺化工作室代碼在本地測試流分析查詢,反對即時流輸入](visual-studio-code-local-run-live-input.md)
* [使用 Visual Studio 在本機測試串流分析查詢](stream-analytics-vs-tools-local-run.md)
* [使用適用於 Visual Studio 的 Azure 串流分析工具在本機測試即時資料](stream-analytics-live-data-local-testing.md)
