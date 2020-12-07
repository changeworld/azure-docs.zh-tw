---
title: 使用 Azure Data Lake Tools for Visual Studio Code
description: 了解如何使用 Azure Data Lake Tools for Visual Studio Code 來建立、測試和執行 U-SQL 指令碼。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: 40e3ce17e036312e7c3fdee95fcb42d06f5845e9
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751354"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>使用 Azure Data Lake Tools for Visual Studio Code

在本文中，您將了解如何使用 Azure Data Lake Tools for Visual Studio Code (VS Code) 來建立、測試及執行 U-SQL 指令碼。 下列影片中也涵蓋此資訊︰

[![影片播放程式：適用于 VS Code 的 Azure Data Lake 工具](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>先決條件

Azure Data Lake Tools for VS Code 支援 Windows、Linux 與 macOS。 U-SQL 本機執行與本機偵錯僅適用於 Windows。

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

若為 MacOS 和 Linux：

- [.NET Core SDK 2。0](https://www.microsoft.com/net/download/core)
- [Mono 5.2. x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>安裝 Azure Data Lake 工具

安裝完必要條件之後，您就可以安裝 Azure Data Lake Tools for VS Code。

### <a name="to-install-azure-data-lake-tools"></a>安裝 Azure Data Lake Tools

1. 開啟 Visual Studio Code。
2. 在左窗格中，選取 [延伸模組]。 在搜尋方塊中輸入 **Azure Data Lake Tools**。
3. 選取 [Azure Data Lake Tools] 旁邊的 [安裝]。

   ![Data Lake Tools 的安裝選取項目](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   幾秒鐘後，[安裝] 按鈕會變為 [重新載入]。
4. 選取 [重新載入] 以啟用 [Azure Data Lake Tools] 延伸模組。
5. 選取 [重新載入視窗] 進行確認。 您可以在 [**擴充** 功能] 窗格中看到 **Azure Data Lake 工具**。

## <a name="activate-azure-data-lake-tools"></a>啟動 Azure Data Lake Tools

建立一個 .usql 檔案或開啟現有的 .usql 檔案，以啟用延伸模組。

## <a name="work-with-u-sql"></a>使用 U-SQL

若要使用 U-SQL，您需要開啟 U-SQL 檔案或資料夾。

### <a name="to-open-the-sample-script"></a>開啟範例指令碼

開啟命令選擇區 (Ctrl+Shift+P)，然後輸入 **ADL: Open Sample Script**。 它會開啟此範例的另一個執行個體。 您也可以在此執行個體上編輯、設定及提交指令碼。

### <a name="to-open-a-folder-for-your-u-sql-project"></a>開啟 U-SQL 專案的資料夾

1. 從 Visual Studio Code 選取 [檔案] 功能表，然後選取 [開啟資料夾]。
2. 指定資料夾，然後選取 [選取資料夾]。
3. 選取 [檔案] 功能表，然後選取 [新增]。 專案中就會加入一個 Untitled-1 檔案。
4. 在 Untitled-1 檔案中輸入以下程式碼：

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   @departments使用 Outputters.Csv ( # A2; 輸出至 "/Output/departments.csv"

    這個指令碼會在 /output 資料夾中建立 departments.csv 檔案並納入一些資料。

5. 在開啟的資料夾中，將檔案儲存為 **myUSQL.usql**。

### <a name="to-compile-a-u-sql-script"></a>編譯 U-SQL 指令碼

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。
2. 輸入 **ADL: Compile Script**。 編譯結果會出現在 [輸出] 視窗中。 您可以也在指令碼檔案上按一下滑鼠右鍵，然後選取 [ADL: Compile Script] 來編譯 U-SQL 作業。 編譯結果會出現在 [輸出] 窗格中。

### <a name="to-submit-a-u-sql-script"></a>提交 U-SQL 指令碼

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。
2. 輸入 **ADL: Submit Job**。 您也可以在指令碼檔案上按一下滑鼠右鍵，然後選取 [ADL: Submit Job]。

在提交 U-SQL 作業後，提交記錄會出現在 VS Code 的 [輸出] 視窗中。 作業檢視會出現在右窗格中。 如果提交成功，則作業 URL 也會出現。 您可以在網頁瀏覽器中開啟作業 URL 來追蹤即時的作業狀態。

在作業檢視的 [摘要] 索引標籤上，您可以看到作業詳細資料。 主要功能包括重新提交指令碼、複製指令碼，以及在入口網站中開啟。 在作業檢視的 [資料] 索引標籤上，您可以參考輸入檔案、輸出檔案及資源檔案。 您可以將檔案下載到本機電腦。

![作業檢視中的 [摘要] 索引標籤](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![作業檢視中的 [資料] 索引標籤](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>設定預設內容

如果您尚未個別設定檔案的參數，便可以設定預設內容以將此設定套用至所有指令碼檔案。

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。
2. 輸入 **ADL: Set Default Context**。 或在指令碼編輯器上按一下滑鼠右鍵，然後選取 [ADL: Set Default Context]。
3. 選擇您想要的帳戶、資料庫及結構描述。 此設定會儲存到 xxx_settings.json 設定檔。

   ![設定為預設內容的帳戶、資料庫及結構描述](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>設定指令碼參數

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。
2. 輸入 **ADL: Set Script Parameters**。
3. xxx_settings.json 檔案隨即開啟，其中含有下列屬性：

   - **account**：您 Azure 訂用帳戶底下編譯和執行 U-SQL 作業所需的 Azure Data Lake Analytics 帳戶。 您必須在編譯和執行 U-SQL 作業之前，先設定電腦帳戶。
   - **資料庫**：您的帳戶底下的資料庫。 預設值為 **master**。
   - **架構**：您資料庫底下的架構。 預設值為 **dbo**。
   - **optionalSettings**：
        - **priority**︰優先順序範圍是從 1 到 1000，其中 1 是最高優先順序。 預設值為 **1000**。
        - **degreeOfParallelism**︰平行處理原則的範圍是從 1 到 150。 預設值為您 Azure Data Lake Analytics 帳戶中允許的平行處理原則上限。

   ![JSON 檔案的內容](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> 在儲存設定後，如果您尚未設定預設內容，帳戶、資料庫和結構描述資訊就會出現在對應之 .usql 檔案左下角的狀態列上。

### <a name="to-set-git-ignore"></a>設定 Git 忽略

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。
2. 輸入 **ADL: Set Git Ignore**。

   - 如果您的 VS Code 工作資料夾中沒有 **.gitIgnore** 檔案，系統就會在您的資料夾中建立名為 **.gitIgnore** 的檔案。 預設會在檔案中新增四個項目 (**usqlCodeBehindReference**、**usqlCodeBehindGenerated**、**.cache**、**obj**) 預設會新增至檔案。 您可以視需要進行更多更新。
   - 如果您的 VS Code 工作資料夾中已經有 **.gitIgnore** 檔案，且檔案中未包含四個項目 (**usqlCodeBehindReference**、**usqlCodeBehindGenerated**、**.cache**、**obj**)，工具就會在您的 **.gitIgnore** 檔案中新增這四個項目。

   ![.gitIgnore 檔案中的項目](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>使用程式碼後置檔案：C Sharp、Python 和 R

Azure Data Lake Tools 支援多個自訂程式碼。 如需相關指示，請參閱[在 VS Code 中使用 Python、R 和 C Sharp 來開發適用於 Azure Data Lake Analytics 的 U-SQL](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)。

## <a name="work-with-assemblies"></a>使用組件

如需有關開發組件的資訊，請參閱[針對 Azure Data Lake Analytics 作業開發 U-SQL 組件]()。

您可以使用 Data Lake Tools 在 Data Lake Analytics 目錄中註冊自訂程式碼組件。

### <a name="to-register-an-assembly"></a>註冊組件

您可以透過 **ADL: Register Assembly** 或 **ADL: Register Assembly (Advanced)** 命令來註冊組件。

### <a name="to-register-through-the-adl-register-assembly-command"></a>透過 ADL: Register Assembly 命令來進行註冊

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。
2. 輸入 **ADL: Register Assembly**。
3. 指定本機組件路徑。
4. 選取 Data Lake Analytics 帳戶。
5. 選取資料庫。

入口網站會在瀏覽器中開啟，並顯示組件註冊程序。  

有另一個可觸發 **ADL: Register Assembly** 命令的更便利方式，就是對「檔案總管」中的 .dll 檔案按一下滑鼠右鍵。

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>透過 ADL: Register Assembly (Advanced) 命令來進行註冊

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。
2. 輸入 **ADL: Register Assembly (Advanced)**。
3. 指定本機組件路徑。
4. 隨即會顯示 JSON 檔案。 請視需要檢閱並編輯組件相依性及資源參數。 指示會顯示在 [ **輸出** ] 視窗中。 若要繼續進行組件註冊，請儲存 (Ctrl + S) JSON 檔案。

   ![含有組件相依性和資源參數的 JSON 檔案](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Azure Data Lake Tools 會自動偵測 DLL 是否有任何相依項目。 在偵測到相依項目後，就會在 JSON 檔案中顯示這些相依項目。
>- 您可以在註冊組件時上傳 DLL 資源 (例如 .txt、.png 和 .csv)。

另一個可供觸發 **ADL: Register Assembly (Advanced)** 命令的方式，是對檔案總管中的 .dll 檔案按一下滑鼠右鍵。

下列 U-SQL 程式碼示範如何呼叫組件。 在此範例中，組件名稱是 *test*。

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>針對 Windows 使用者使用 U-SQL 本機執行和本機偵錯

U-SQL 本機執行會先測試您的本機資料並在本機驗證您的指令碼，然後才將您的程式碼發行至 Data Lake Analytics。 您可以使用本機偵錯功能先完成下列工作，再將您的程式碼提交給 Data Lake Analytics：

- 偵錯您的 C# 程式碼後置。
- 逐步執行程式碼。
- 在本機驗證您的指令碼。

本機執行與本機偵錯功能僅適用於 Windows 環境，在 macOS 與 Linux 型作業系統上不支援。

如需本機執行和本機偵錯的指示，請參閱[使用 Visual Studio Code 來進行 U-SQL 本機執行和本機偵錯](data-lake-tools-for-vscode-local-run-and-debug.md)。

## <a name="connect-to-azure"></a>連線到 Azure

您必須先連線到 Azure 帳戶，才能在 Data Lake Analytics 中編譯和執行 U-SQL 指令碼。

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>使用命令來連線至 Azure

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。

2. 輸入 **ADL: Login**。 登入資訊會出現在右下方。

   ![輸入登入命令](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![登入和驗證的相關通知](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. 選取 [複製及開啟] 以開啟[登入網頁](https://aka.ms/devicelogin)。 將程式碼貼到方塊中，然後選取 [繼續]。

    ![登入網頁](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. 依照網頁上的指示登入。 當您已連線時，您的 Azure 帳戶名稱會出現在 VS Code 視窗左下角的狀態列上。

> [!NOTE]
>
> - 如果您未登出，下次 Data Lake Tools 就會自動將您登入。
> - 如果您的帳戶已啟用雙因素驗證，建議您使用電話驗證而非使用 PIN 碼。

若要登出，請輸入命令 **ADL: Logout**。

### <a name="to-connect-to-azure-from-the-explorer"></a>從總管連線至 Azure

展開 [AZURE DATALAKE]，選取 [登入 Azure]，然後依照[使用命令來連線至 Azure](#sign-in-by-command) 的步驟 3 和步驟 4 進行操作。

![總管中的 [登入 Azure] 選取項目](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

您無法從總管登出。 若要登出，請參閱[使用命令來連線至 Azure](#sign-in-by-command)。

## <a name="create-an-extraction-script"></a>建立擷取指令碼

您可以使用 **ADL: Create EXTRACT Script** 命令或從 Azure Data Lake 總管，建立 .csv、.tsv 及 .txt 檔案的擷取指令碼。

### <a name="to-create-an-extraction-script-by-using-a-command"></a>使用命令來建立擷取指令碼

1. 選取 Ctrl+Shift+P 來開啟命令選擇區，然後輸入 **ADL: Create EXTRACT Script**。
2. 指定 Azure 儲存體檔案的完整路徑，然後選取 Enter 鍵。
3. 選取帳戶。
4. 若為 .txt 檔案，請選取用來擷取檔案的分隔符號。

![建立擷取指令碼的程序](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

系統會根據您的輸入來產生擷取指令碼。 對於無法偵測資料行的指令碼，請選擇兩個選項的其中一個。 如果未選擇，則只會產生一個指令碼。

![建立擷取指令碼的結果](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>從總管建立擷取指令碼

有另一個可建立擷取指令碼的方式，就是在 Azure Data Lake Store 或 Azure Blob 儲存體中透過 .csv、.tsv 或 .txt 檔案的滑鼠右鍵 (捷徑) 功能表來進行。

![捷徑功能表中的 [建立 EXTRACT 指令碼] 命令](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>後續步驟

- [在 VS Code 中使用 Python、R 和 C Sharp 來開發適用於 Azure Data Lake Analytics 的 U-SQL](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [使用 Visual Studio Code 來進行 U-SQL 本機執行和本機偵錯](data-lake-tools-for-vscode-local-run-and-debug.md)
- [教學課程：開始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [教學課程：使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)