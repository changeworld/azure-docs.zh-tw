---
title: 使用 Visual Studio Code 的範例資料，在本機測試 Azure 串流分析作業
description: 本文說明如何使用適用于 Visual Studio Code 的 Azure 串流分析工具，在本機測試查詢的範例資料。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: how-to
ms.openlocfilehash: 0122e67714efb201584a52f1cb763528dbb0b925
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903798"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>使用 Visual Studio Code 和範例資料在本機測試串流分析查詢

您可以使用適用于 Visual Studio Code 的 Azure 串流分析工具，在本機使用範例資料測試您的串流分析作業。 您可以在專案的 **LocalRunOutputs** 資料夾中，找到 JSON 檔案中的查詢結果。

## <a name="prerequisites"></a>必要條件

* 安裝 [.net CORE SDK](https://dotnet.microsoft.com/download) 並重新啟動 Visual Studio Code。

* 您可以使用本 [快速入門](quick-create-visual-studio-code.md) 來瞭解如何使用 Visual Studio Code 建立串流分析作業。

## <a name="prepare-sample-data"></a>準備範例資料

您需要先準備範例輸入資料檔案。 如果您的電腦上已有一些範例資料檔案，您可以略過此步驟，並移至下一個步驟。

1. 按一下頂端行中的 [預覽輸入設定檔中的 **資料** ]。 某些輸入資料將會從 IoT 中樞提取，並顯示在 [預覽] 視窗中。 請注意，這可能需要一些時間。

2. 顯示資料之後，請按一下 [ **另存** 新檔]，將資料儲存至本機檔案。

 ![預覽即時輸入](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-a-local-input"></a>定義本機輸入

1. 在串流分析專案的 [輸入] 資料夾下，按一下 [ **input.js** ]。 然後，從頂端行選取 [ **新增本機輸入** ]。

    ![從專案新增本機輸入](./media/quick-create-visual-studio-code/add-input-from-project.png)

    您也可以使用 **Ctrl + Shift + P** 來開啟命令選擇區，並輸入 **ASA： Add Input**。

   ![在 VS Code 中新增串流分析輸入](./media/quick-create-visual-studio-code/add-input.png)

2. 選取 [ **本機輸入**]。

    ![在 Visual Studio 程式碼中新增 ASA 本機輸入](./media/vscode-local-run/add-local-input.png)

3. 選取 [ **+ 新增本機輸入**]。

    ![在 Visual Studio 程式碼中新增 ASA 本機輸入](./media/vscode-local-run/add-new-local-input.png)

4. 輸入您在查詢中使用的相同輸入別名。

    ![新增 ASA 本機輸入別名](./media/vscode-local-run/new-local-input-alias.png)

5. 在新產生的 [檔案 **LocalInput_Input.js** ] 中，輸入本機資料檔所在的檔案路徑。

    ![在 Visual Studio 中輸入本機檔案路徑](./media/vscode-local-run/local-file-path.png)

6. 選取 [ **預覽資料** ] 來預覽輸入資料。 如果資料的 JSON 或 CSV，則會自動偵測資料的序列化類型。 使用選取器來查看 **資料表** 或 **原始** 格式的資料。 下表是 **資料表格式**的資料範例：

     ![預覽資料表格式的本機資料](./media/vscode-local-run/local-file-preview-table.png)

    下表是 **原始格式**的資料範例：

    ![以原始格式預覽本機資料](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>在本機執行查詢

返回您的查詢編輯器，然後選取 [在 **本機執行**]。 然後，從下拉式清單中選取 [ **使用本機輸入** ]。

![在查詢編輯器中選取 [在本機執行]](./media/vscode-local-run/run-locally.png)

![使用本機輸入](./media/vscode-local-run/run-locally-use-local-input.png)

結果會顯示在右視窗中。 您可以按一下 [ **執行** ] 再次測試。 您也可以選取 [ **在資料夾中開啟** ]，以在 [檔案瀏覽器] 中查看結果檔，並使用其他工具進一步開啟這些檔案。 請注意，結果檔案僅適用于 JSON 格式。

![查看本機執行結果](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>後續步驟

* [使用 Visual Studio Code 在本機測試 Azure 串流分析作業與即時輸入](visual-studio-code-local-run-live-input.md)

* [使用 Visual Studio Code (預覽版探索 Azure 串流分析作業) ](visual-studio-code-explore-jobs.md)
