---
title: 使用 Visual Studio Code 在本機針對即時串流輸入測試 Azure 串流分析查詢
description: 本文說明如何使用適用于 Visual Studio Code 的 Azure 串流分析工具，在本機針對即時串流輸入測試查詢。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: how-to
ms.openlocfilehash: 1b2268426b66ddb9ffdd48d6901ab991e76cb95c
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037438"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>使用 Visual Studio Code 在本機針對即時串流輸入測試串流分析查詢

您可以使用適用于 Visual Studio Code 的 Azure 串流分析工具，在本機針對即時串流輸入測試您的串流分析作業。 輸入可以來自 Azure 事件中樞或 Azure IoT 中樞等來源。 輸出結果會以 JSON 檔案形式傳送至您專案中名為**LocalRunOutputs**的資料夾。

## <a name="prerequisites"></a>必要條件

* 安裝[.NET Core SDK](https://dotnet.microsoft.com/download)並重新啟動 Visual Studio Code。

* 使用[本快速入門](quick-create-vs-code.md)來瞭解如何使用 Visual Studio Code 建立串流分析作業。

## <a name="define-a-live-stream-input"></a>定義即時資料流輸入

1. 以滑鼠右鍵按一下您串流分析專案中的**輸入**資料夾。 然後，從操作功能表中選取 **[ASA：新增輸入]** 。

   ![從 [輸入] 資料夾新增輸入](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

   您也可以選取**Ctrl + Shift + P**來開啟命令選擇區，然後輸入**ASA： Add Input**。

   ![在 Visual Studio Code 中新增串流分析輸入](./media/quick-create-vs-code/add-input.png)

2. 從下拉式清單中選擇輸入來源類型。

   ![選取 IoT 中樞作為輸入選項](./media/quick-create-vs-code/iot-hub.png)

3. 如果您已從命令選擇區新增輸入，請選擇將使用輸入的串流分析查詢指令碼。 其中應該會自動填入 **myASAproj.asaql** 的檔案路徑。

   ![選取 Visual Studio Code 中的串流分析指令碼](./media/quick-create-vs-code/asa-script.png)

4. 從下拉式功能表中選擇 [從您的 Azure 訂用帳戶選取]  。

    ![從訂用帳戶選取](./media/quick-create-vs-code/add-input-select-subscription.png)

5. 設定新產生的 JSON 檔案。 您可以利用 CodeLens 功能來輸入字串、從下拉式清單中選取，或直接在檔案中變更文字。 下列螢幕擷取畫面顯示**從您的訂用帳戶選取**作為範例。

   ![在 Visual Studio Code 中設定輸入](./media/quick-create-vs-code/configure-input.png)

## <a name="preview-input"></a>預覽輸入

若要確定輸入資料即將存在，請從頂端行中選取即時輸入設定檔案中的 [**預覽資料**]。 某些輸入資料來自 IoT 中樞，並顯示在預覽視窗中。 預覽可能需要幾秒鐘的時間才會出現。

 ![預覽即時輸入](./media/quick-create-vs-code/preview-live-input.png)

## <a name="run-queries-locally"></a>在本機執行查詢

返回您的查詢編輯器，然後選取 [在**本機執行**]。 然後從下拉式清單中選取 [**使用即時輸入**]。

![在 [查詢編輯器] 中選取 [本機執行]](./media/vscode-local-run/run-locally.png)

![選取 [使用即時輸入]](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

結果會顯示在右側視窗中，並每3秒重新整理一次。 您可以選取 [**執行**] 來重新測試。 您也可以選取 [**在資料夾中開啟**]，在 [檔案管理器] 中查看結果檔案，然後使用 Visual Studio Code 或 Excel 之類的工具來開啟這些檔案。 請注意，結果檔案僅適用于 JSON 格式。

開始建立輸出之作業的預設時間是設定為 [**現在**]。 您可以在 [結果] 視窗中選取 [**輸出開始時間**] 按鈕來自訂時間。

![查看本機執行結果](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>後續步驟

* [使用 Visual Studio Code 探索 Azure 串流分析作業（預覽）](visual-studio-code-explore-jobs.md)

* [使用 npm 套件來設定 CI/CD 管線](setup-cicd-vs-code.md)
