---
title: 使用視覺化工作室代碼使用示例資料在本地測試 Azure 流分析作業
description: 本文介紹如何使用視覺化工作室代碼的 Azure 流分析工具在本地使用示例資料測試查詢。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: conceptual
ms.openlocfilehash: c29d0d9ecd856ee9611df21d23b1b2b763e24652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75486465"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>使用視覺化工作室代碼使用示例資料在本地測試流分析查詢

您可以使用視覺化工作室代碼的 Azure 流分析工具在本地使用示例資料測試流分析作業。 您可以在專案的 **"本地運行輸出"** 資料夾中的 JSON 檔中找到查詢結果。

## <a name="prerequisites"></a>Prerequisites

* 安裝[.NET 核心 SDK](https://dotnet.microsoft.com/download)並重新啟動視覺化工作室代碼。

* 使用此[快速入門](quick-create-vs-code.md)瞭解如何使用視覺化工作室代碼創建流分析作業。

## <a name="prepare-sample-data"></a>準備範例資料

您需要首先準備示例輸入資料檔案。 如果電腦上已有一些示例資料檔案，則可以跳過此步驟，然後轉到下一步。

1. 按一下從頂行預覽輸入設定檔中**的資料**。 某些輸入資料將從 IoT 中心提取，並顯示在預覽視窗中。 請注意，這可能需要一段時間。

2. 顯示資料後，按一下"**保存"以**將資料保存到本地檔。

 ![預覽即時輸入](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>定義本地輸入

1. 按一下流分析專案中的"輸入"資料夾下的**input.json。** 然後選擇**從頂行添加本地輸入**。

    ![從專案添加本地輸入](./media/quick-create-vs-code/add-input-from-project.png)

    您還可以使用**Ctrl_Shift_P**打開命令調色板並輸入**ASA：添加輸入**。

   ![在 VS Code 中新增串流分析輸入](./media/quick-create-vs-code/add-input.png)

2. 選擇**本地輸入**。

    ![在視覺化工作室代碼中添加 ASA 本地輸入](./media/vscode-local-run/add-local-input.png)

3. 選擇 **= 新的本地輸入**。

    ![在視覺化工作室代碼中添加新的 ASA 本地輸入](./media/vscode-local-run/add-new-local-input.png)

4. 輸入查詢中使用的相同輸入別名。

    ![添加新的 ASA 本地輸入別名](./media/vscode-local-run/new-local-input-alias.png)

5. 在新生成的**LocalInput_Input.json**檔中，輸入本地資料檔案所在的檔路徑。

    ![在視覺化工作室中輸入本地檔路徑](./media/vscode-local-run/local-file-path.png)

6. 選擇 **"預覽資料**"以預覽輸入資料。 如果資料的 JSON 或 CSV，將自動檢測到資料的序列化類型。 使用選擇器以**表格**或**原始**格式查看資料。 下表是**表格式**的資料示例：

     ![以表格格式預覽本地資料](./media/vscode-local-run/local-file-preview-table.png)

    下表是**原始格式**的資料示例：

    ![以原始格式預覽本地資料](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>在本機執行查詢

返回到查詢編輯器，然後選擇 **"在本地運行**"。 然後選擇 **"使用**下拉清單中的本地輸入"。

![在查詢編輯器中選擇本地運行](./media/vscode-local-run/run-locally.png)

![使用本地輸入](./media/vscode-local-run/run-locally-use-local-input.png)

結果顯示在正確的視窗中。 您可以按一下 **"運行"** 以再次測試。 您還可以選擇 **"在資料夾中打開"** 以查看檔資源管理器中的結果檔，然後使用其他工具進一步打開這些檔。 請注意，結果檔僅以 JSON 格式提供。

![查看本地運行結果](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>後續步驟

* [使用視覺化工作室代碼使用即時輸入在本地測試 Azure 流分析作業](visual-studio-code-local-run-live-input.md)

* [使用視覺化工作室代碼流覽 Azure 流分析作業（預覽版）](visual-studio-code-explore-jobs.md)
