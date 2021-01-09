---
title: 在本機開發和調試 Azure 串流分析作業
description: 瞭解如何在您的本機電腦上開發及測試 Azure 串流分析作業，然後在 Azure 入口網站中執行它們。
ms.author: sujie
author: su-jie
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 18df480dab90d9ab127bb96971fc19cdc5a361ce
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016468"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>在本機開發和調試 Azure 串流分析作業

雖然您可以在 Azure 入口網站中建立及測試 Azure 串流分析作業，但許多開發人員都偏好本機開發體驗。 串流分析可讓您輕鬆地使用您慣用的程式碼編輯器和開發工具，透過 Azure 事件中樞、IoT 中樞和 Blob 儲存體的即時事件資料流程，使用完整的單一節點本機執行時間來建立和測試工作。 您也可以直接從本機開發環境將作業提交至 Azure。

## <a name="local-development-environments"></a>本機開發環境

您在本機電腦上開發串流分析作業的方式，取決於您的工具喜好設定和功能可用性。 請參閱 [Azure 串流分析功能比較](feature-comparison.md) ，以瞭解每個開發環境支援哪些功能。

下表中的環境支援本機開發：

|環境                              |描述    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| 適用于 Visual Studio Code 的 [Azure 串流分析工具擴充](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) 功能可讓您使用豐富的 IntelliSense 和原生原始檔控制，在本機和雲端中撰寫、管理及測試串流分析作業。 支援在 Linux、MacOS 和 Windows 上進行開發。 若要深入瞭解，請參閱 [在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-visual-studio-code.md)。 此延伸模組也支援 [Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/) ，也就是雲端裝載的開發環境。|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |串流分析工具是 Azure 開發的一部分，也是 Visual Studio 中的資料儲存和處理工作負載的一部分。 您可以使用 Visual Studio 來撰寫自訂的 c # 使用者定義函式和還原序列化程式。 若要深入瞭解，請參閱 [使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)。|
|[命令提示字元或終端機](stream-analytics-tools-for-visual-studio-cicd.md)|Azure 串流分析 CI/CD NuGet 套件提供適用于 Visual studio 專案組建的工具，在任意電腦上進行本機測試。 Azure 串流分析 CI/CD npm 套件提供 Visual Studio Code 專案組建的工具， (在任意電腦上產生 Azure Resource Manager 範本) 。|

## <a name="next-steps"></a>後續步驟

* [使用 Visual Studio Code 和範例資料在本機測試串流分析查詢](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 在本機針對即時資料流輸入測試串流分析查詢](visual-studio-code-local-run-live-input.md)
* [使用 Visual Studio 在本機測試串流分析查詢](stream-analytics-vs-tools-local-run.md)
* [使用適用於 Visual Studio 的 Azure 串流分析工具在本機測試即時資料](stream-analytics-live-data-local-testing.md)
