---
title: 使用 Data Lake 工具存取資源
description: 瞭解如何使用 Azure Data Lake 工具來存取 Azure Data Lake Analytics 資源。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754605"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>使用 Azure Data Lake 工具存取資源

您可以輕鬆地使用 Azure Data Tools 命令或 VS Code 中的動作來存取 Azure Data Lake Analytics 資源。

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>透過命令與 Azure Data Lake Analytics 整合

您可以存取 Azure Data Lake Analytics 資源來列出帳戶、存取中繼資料，以及檢視分析作業。

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>列出 Azure 訂用帳戶下的 Azure Data Lake Analytics 帳戶

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。
2. 輸入 **ADL: List Accounts**。 帳戶會出現在 [輸出] 窗格中。

### <a name="to-access-azure-data-lake-analytics-metadata"></a>存取 Azure Data Lake Analytics 中繼資料

1. 選取 Ctrl+Shift+P，然後輸入 **ADL: List Tables**。
2. 選取其中一個 Data Lake Analytics 帳戶。
3. 選取其中一個 Data Lake Analytics 資料庫。
4. 選取其中一個結構描述。 您就會看到資料表清單。

### <a name="to-view-azure-data-lake-analytics-jobs"></a>檢視 Azure Data Lake Analytics 作業

1. 開啟命令選擇區 (Ctrl+Shift+P)，然後選取 [ADL: Show Job]。
2. 選取 Data Lake Analytics 帳戶或本機帳戶。
3. 等候帳戶的作業清單出現。
4. 從作業清單中選取作業。 Data Lake Tools 會在右窗格開啟作業檢視，並在 VS Code 輸出中顯示一些資訊。

    ![作業清單](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>透過命令與 Azure Data Lake Store 整合

您可以使用 Azure Data Lake Store 相關命令來進行下列作業：

- [瀏覽 Azure Data Lake Store 資源](#list-the-storage-path)
- [預覽 Azure Data Lake Store 檔案](#preview-the-storage-file)
- 在 VS Code 中直接將檔案上傳至 Azure Data Lake Store
- 在 VS Code 中直接從 Azure Data Lake Store 下載檔案

### <a name="list-the-storage-path"></a>列出儲存體路徑

### <a name="to-list-the-storage-path-through-the-command-palette"></a>透過命令選擇區來列出儲存體路徑

1. 以滑鼠右鍵按一下指令碼編輯器，然後選取 [ADL: List Path]。
2. 選擇清單中的資料夾，或是選取 [輸入路徑] 或 [從根路徑瀏覽]。 (我們使用 [輸入路徑] 作為範例)。
3. 選取您的 Data Lake Analytics 帳戶。
4. 瀏覽或輸入儲存體資料夾路徑 (例如 /output/)。  

命令選擇區會根據您的輸入列出路徑資訊。

![儲存體路徑結果](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

有一個可列出相對路徑的更便利方式，就是透過捷徑功能表。

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>透過捷徑功能表來列出儲存體路徑

在路徑字串上按一下滑鼠右鍵，然後選取 [列出路徑]。

![捷徑功能表上的 [列出路徑]](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>預覽儲存體檔案

1. 以滑鼠右鍵按一下指令碼編輯器，然後選取 [ADL: Preview File]。
2. 選取您的 Data Lake Analytics 帳戶。
3. 輸入 Azure 儲存體檔案路徑 (例如 /output/SearchLog.txt)。

檔案會在 VS Code 中開啟。

![儲存體檔案的預覽步驟和結果](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

有另一個可預覽檔案的方式，就是在指令碼編輯器中，透過檔案完整路徑或檔案相對路徑上的捷徑功能表來進行。

### <a name="upload-a-file-or-folder"></a>上傳檔案或資料夾

1. 以滑鼠右鍵按一下指令碼編輯器，然後選取 [上傳檔案] 或 [上傳資料夾]。
2. 如果您已選取 [上傳檔案]，請選擇一或多個檔案。或者，如果您已選取 [上傳資料夾]，則請選擇整個資料夾。 然後選取 [上傳]。
3. 選擇清單中的儲存體資料夾，或是選取 [輸入路徑] 或 [從根路徑瀏覽]。 (我們使用 [輸入路徑] 作為範例)。
4. 選取您的 Data Lake Analytics 帳戶。
5. 瀏覽或輸入儲存體資料夾路徑 (例如 /output/)。
6. 選取 [選擇目前資料夾] 來指定您的上傳目的地。

![檔案或資料夾的上傳步驟和結果](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

有另一個可將檔案上傳到儲存體的方式，就是在指令碼編輯器中，透過檔案完整路徑或檔案相對路徑上的捷徑功能表來進行。

您可以[監視上傳狀態](#check-storage-tasks-status)。

### <a name="download-a-file"></a>下載檔案

您可以使用 **ADL: Download File** 或 **ADL: Download File (Advanced)** 命令來下載檔案。

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>透過 ADL: Download File (Advanced) 命令來下載檔案

1. 以滑鼠右鍵按一下指令碼編輯器，然後選取 [Download File (Advanced)]。
2. VS Code 會顯示一個 JSON 檔案。 您可以輸入檔案路徑，然後同時下載多個檔案。 指示會顯示在 [ **輸出** ] 視窗中。 若要繼續下載一或多個檔案，請儲存 (Ctrl+S) JSON 檔案。

    ![含有檔案下載路徑的 JSON 檔案](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

[輸出] 視窗會顯示檔案下載狀態。

![含有下載狀態的 [輸出] 視窗](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

您可以[監視下載狀態](#check-storage-tasks-status)。

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>透過 ADL: Download File 命令來下載檔案

1. 在指令碼編輯器上按一下滑鼠右鍵，選取 [下載檔案]，然後從 [選取資料夾] 對話方塊中選取目的地資料夾。

1. 選擇清單中的資料夾，或是選取 [輸入路徑] 或 [從根路徑瀏覽]。 (我們使用 [輸入路徑] 作為範例)。

1. 選取您的 Data Lake Analytics 帳戶。

1. 瀏覽或輸入儲存體資料夾路徑 (例如：/output/)，然後選擇要下載的檔案。

![檔案的下載步驟和結果](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

有另一個可下載儲存體檔案的方式，就是在指令碼編輯器中，透過檔案完整路徑或檔案相對路徑上的捷徑功能表來進行。

您可以[監視下載狀態](#check-storage-tasks-status)。

### <a name="check-storage-tasks-status"></a>檢查儲存體工作的狀態

上傳和下載狀態會顯示在狀態列上。 請選取狀態列，然後狀態就會顯示在 [輸出] 索引標籤上。

![狀態列和輸出詳細資料](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>從總管與 Azure Data Lake Analytics 整合

登入之後，您 Azure 帳戶的所有訂用帳戶都會列在左窗格中 [AZURE DATALAKE] 底下。

![Data Lake 總管](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics 中繼資料瀏覽

展開您的 Azure 訂用帳戶。 在 [U-SQL 資料庫] 節點底下，您可以瀏覽 U-SQL 資料庫並檢視資料夾，例如 [結構描述]、[認證]、[組件]、[資料表] 及 [索引]。

### <a name="data-lake-analytics-metadata-entity-management"></a>Data Lake Analytics 中繼資料實體管理

展開 [U-SQL 資料庫]。 您可以建立資料庫、結構描述、資料表、資料表類型、索引或統計資料，方法是在對應的節點上按一下滑鼠右鍵，然後從捷徑功能表上選取 [要建立的指令碼]。 在已開啟的指令碼頁面上，根據您的需求編輯指令碼。 接著，在指令碼上按一下滑鼠右鍵並選取 [ADL: Submit Job] 來提交作業。

建立完項目之後，在節點上按一下滑鼠右鍵，然後選取 [重新整理] 來顯示項目。 您也可以刪除項目，方法是在項目上按一下滑鼠右鍵，然後選取 [刪除]。

![Data Lake 總管中捷徑功能表上的 [要建立的指令碼] 命令](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![新項目的指令碼頁面](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics 組件註冊

您可以在對應的資料庫中註冊組件，方法是在 [組件] 節點上按一下滑鼠右鍵，然後選取 [註冊組件]。

![[組件] 節點捷徑功能表上的 [註冊組件] 命令](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>從總管與 Azure Data Lake Store 整合

瀏覽至 [Data Lake Store]：

- 您可以在資料夾節點上按一下滑鼠右鍵，然後使用捷徑功能表上的 [重新整理]、[刪除]、[上傳]、[上傳資料夾]、[複製相對路徑] 及 [複製完整路徑] 命令。

   ![Data Lake 總管中資料夾節點上的捷徑功能表命令](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- 您可以在檔案節點上按一下滑鼠右鍵，然後使用捷徑功能表上的 [預覽]、[下載]、[刪除]、[建立 EXTRACT 指令碼] (只適用於 CSV、TSV 和 TXT 檔案)，以及 [複製相對路徑] 和 [複製完整路徑] 命令。

   ![Data Lake 總管中檔案節點上的捷徑功能表命令](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>從總管與 Azure Blob 儲存體整合

瀏覽至 Blob 儲存體：

- 您可以在 Blob 容器節點上按一下滑鼠右鍵，然後使用捷徑功能表上的 [重新整理]、[刪除 Blob 容器] 及 [上傳 Blob] 命令。

   ![Blob 儲存體底下 Blob 容器節點的捷徑功能表命令](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- 您可以在資料夾節點上按一下滑鼠右鍵，然後使用捷徑功能表上的 [重新整理]和 [上傳 Blob] 命令。

   ![Blob 儲存體底下資料夾節點的捷徑功能表命令](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- 您可以在檔案節點上按一下滑鼠右鍵，然後使用捷徑功能表上的 [預覽/編輯]、[下載]、[刪除]、[建立 EXTRACT 指令碼] (只適用於 CSV、TSV 和 TXT 檔案)，以及 [複製相對路徑] 和 [複製完整路徑] 命令。

    ![Blob 儲存體底下檔案節點的捷徑功能表命令](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>在入口網站中開啟 Data Lake 總管

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。
2. 輸入 **Open Web Azure Storage Explorer**，或在指令碼編輯器中的相對路徑或完整路徑上按一下滑鼠右鍵，然後選取 [Open Web Azure Storage Explorer]。
3. 選取 Data Lake Analytics 帳戶。

Data Lake Tools 會在 Azure 入口網站中開啟 Azure 儲存體路徑。 您可以找到該路徑，並從網路預覽檔案。

## <a name="additional-features"></a>其他功能

Data Lake Tools for VSCode 支援下列功能︰

- **IntelliSense 自動完成**：在關鍵字、方法和變數等項目周圍的快顯視窗中會顯示建議。 不同圖示代表不同類型的物件：

  - Scala 資料類型
  - 複雜資料類型
  - 內建 UDT
  - .NET 集合和類別
  - C# 運算式
  - 內建 C# UDF、UDO 和 UDAAG
  - U-SQL 函式
  - U-SQL 時間範圍函式

    ![IntelliSense 物件類型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **Data Lake Analytics 中繼資料的 IntelliSense 自動完成**：Data Lake Tools 會將 Data Lake Analytics 中繼資料資訊下載到本機。 IntelliSense 功能會從 Data Lake Analytics 中繼資料自動填入物件。 這些物件包括資料庫、結構描述、資料表、檢視、資料表值函式、程序及 C# 組件。

  ![IntelliSense 中繼資料](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **IntelliSense 錯誤標記**：Data Lake Tools 會為 U-SQL 和 C# 的編輯錯誤加上底線。
- **語法醒目提示**：Data Lake Tools 會使用顏色來區分項目，例如變數、關鍵字、資料類型及函式。

    ![帶有各種顏色的語法](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> 建議您升級至 Azure Data Lake Tools for Visual Studio 2.3.3000.4 版或更新版本。 舊版目前已淘汰，不再提供下載。  

## <a name="next-steps"></a>後續步驟

- [在 VS Code 中使用 Python、R 和 C Sharp 來開發適用於 Azure Data Lake Analytics 的 U-SQL](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [使用 Visual Studio Code 來進行 U-SQL 本機執行和本機偵錯](data-lake-tools-for-vscode-local-run-and-debug.md)
- [教學課程：開始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [教學課程：使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)