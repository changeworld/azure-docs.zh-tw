---
title: 使用視覺化工作室代碼在本地測試 Azure 流分析查詢，反對即時流輸入
description: 本文介紹如何使用視覺化工作室代碼的 Azure 流分析工具在本地測試即時流輸入的查詢。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: 34ce91a1385f951847abeedd3a6b526d3a07af35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75660846"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>使用視覺化工作室代碼在本地測試流分析查詢，反對即時流輸入

您可以使用 Azure 流分析工具進行視覺化工作室代碼，根據即時流輸入在本地測試流分析作業。 輸入可能來自 Azure 事件中心或 Azure IoT 中心等源。 輸出結果以 JSON 檔身份發送到專案中名為**LocalRun輸出的**資料夾中。

## <a name="prerequisites"></a>Prerequisites

* 安裝[.NET 核心 SDK](https://dotnet.microsoft.com/download)並重新啟動視覺化工作室代碼。

* [使用此快速入門](quick-create-vs-code.md)瞭解如何使用視覺化工作室代碼創建流分析作業。

## <a name="define-a-live-stream-input"></a>定義即時流輸入

1. 以滑鼠右鍵按一下您串流分析專案中的**輸入**資料夾。 然後選擇**ASA：從內容功能表中添加輸入**。

   ![從 [輸入] 資料夾新增輸入](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   您還可以選擇**Ctrl_Shift_P**來打開命令調色板並輸入**ASA：添加輸入**。

   ![在 Visual Studio Code 中新增串流分析輸入](./media/quick-create-vs-code/add-input.png)

2. 從下拉清單中選擇輸入源類型。

   ![選取 IoT 中樞作為輸入選項](./media/quick-create-vs-code/iot-hub.png)

3. 如果您已從命令選擇區新增輸入，請選擇將使用輸入的串流分析查詢指令碼。 其中應該會自動填入 **myASAproj.asaql** 的檔案路徑。

   ![選取 Visual Studio Code 中的串流分析指令碼](./media/quick-create-vs-code/asa-script.png)

4. 從下拉式功能表中選擇 [從您的 Azure 訂用帳戶選取]****。

    ![從訂用帳戶選取](./media/quick-create-vs-code/add-input-select-subscription.png)

5. 配置新生成的 JSON 檔。 您可以利用 CodeLens 功能來輸入字串、從下拉式清單中選取，或直接在檔案中變更文字。 下列螢幕擷取畫面顯示**從您的訂用帳戶選取**作為範例。

   ![在 Visual Studio Code 中設定輸入](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>預覽輸入

要確保輸入資料即將到來，請從頂行選擇即時輸入設定檔中的**預覽資料**。 某些輸入資料來自 IoT 中心，顯示在預覽視窗中。 預覽可能需要幾秒鐘才能顯示。

 ![預覽即時輸入](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>在本機執行查詢

返回到查詢編輯器，然後選擇 **"在本地運行**"。 然後從下拉清單中選擇 **"使用即時輸入**"。

![在查詢編輯器中選擇"本地運行"](./media/vscode-local-run/run-locally.png)

![選擇"使用即時輸入"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

結果顯示在正確的視窗中，每 3 秒刷新一次。 您可以選擇 **"運行"** 以再次測試。 您還可以選擇 **"在資料夾中打開"** 以查看檔資源管理器中的結果檔，並使用 Visual Studio 代碼或 Excel 等工具打開這些檔。 請注意，結果檔僅以 JSON 格式提供。

作業開始創建輸出的預設時間設置為 **"現在**"。 您可以通過在結果視窗中選擇 **"輸出開始時間"** 按鈕來自訂時間。

![查看本地運行結果](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>後續步驟

* [使用視覺化工作室代碼流覽 Azure 流分析作業（預覽版）](visual-studio-code-explore-jobs.md)

* [使用 npm 套件來設定 CI/CD 管線](setup-cicd-vs-code.md)
