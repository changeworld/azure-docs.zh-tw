---
title: 在本機開發和調試 Azure 串流分析作業
description: 瞭解如何在 Azure 入口網站中執行 Azure 串流分析作業之前，先在本機電腦上進行開發和測試。
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80879839"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>在本機開發和調試 Azure 串流分析作業

雖然您可以在 Azure 入口網站中建立及測試 Azure 串流分析作業，但許多開發人員偏好使用本機開發體驗。 串流分析可讓您輕鬆地使用您最愛的程式碼編輯器和開發工具，利用來自 Azure 事件中樞的即時事件串流、IoT 中樞和 Blob 儲存體，使用完全正常運作的單一節點本機執行時間來建立及測試作業。 您也可以直接從本機開發環境將作業提交至 Azure。

## <a name="local-development-environments"></a>本機開發環境

您在本機電腦上開發串流分析作業的方式，取決於您的工具喜好設定和功能可用性。 請參閱[Azure 串流分析功能比較](feature-comparison.md)，以查看每個開發環境所支援的功能。

下表中的環境支援本機開發：

|環境                              |描述    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| 適用于 Visual Studio Code 的[Azure 串流分析工具擴充](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa)功能，可讓您使用豐富的 IntelliSense 和原生原始檔控制，在本機和雲端中撰寫、管理和測試您的串流分析作業。 支援在 Linux、MacOS 和 Windows 上進行開發。 若要深入瞭解，請參閱[在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-vs-code.md)。|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |串流分析工具是 Visual Studio 中 Azure 開發和資料儲存和處理工作負載的一部分。 您可以使用 Visual Studio 來撰寫自訂的 c # 使用者定義函數和還原序列化程式。 若要深入瞭解，請參閱[使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)。|
|[命令提示字元或終端機](stream-analytics-tools-for-visual-studio-cicd.md)|Azure 串流分析 CI/CD NuGet 套件提供 Visual studio 專案組建的工具，在任意電腦上進行本機測試。 Azure 串流分析 CI/CD npm 套件提供可在任意電腦上 Visual Studio Code 專案組建（其會產生 Azure Resource Manager 範本）的工具。|

## <a name="next-steps"></a>後續步驟

* [使用 Visual Studio Code 和範例資料在本機測試串流分析查詢](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 在本機針對即時串流輸入測試串流分析查詢](visual-studio-code-local-run-live-input.md)
* [使用 Visual Studio 在本機測試串流分析查詢](stream-analytics-vs-tools-local-run.md)
* [使用適用於 Visual Studio 的 Azure 串流分析工具在本機測試即時資料](stream-analytics-live-data-local-testing.md)
