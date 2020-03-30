---
title: 自訂 Microsoft 安全代碼分析任務
titleSuffix: Azure
description: 本文介紹在 Microsoft 安全代碼分析擴展中自訂任務
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 6cdf892651407defc21f359a8e3b326b4af63b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499995"
---
# <a name="configure-and-customize-the-build-tasks"></a>配置和自訂生成任務

本文詳細介紹了每個生成任務中可用的配置選項。 本文從安全代碼分析工具的任務開始。 它以後處理任務結束。

## <a name="anti-malware-scanner-task"></a>反惡意軟體掃描器任務

>[!NOTE]
> 反惡意軟體掃描器生成任務需要啟用 Windows Defender 的生成代理。 託管視覺工作室 2017 和更高版本提供這樣的代理。 生成任務不會在 Visual Studio 2015 託管代理上運行。
>
> 儘管無法在這些代理上更新簽名，但簽名應始終小於三個小時。

任務配置的詳細資訊顯示在以下螢幕截圖和文本中。

![配置反惡意軟體掃描器生成任務](./media/security-tools/5-add-anti-malware-task600.png)

在螢幕截圖的 **"類型"** 清單方塊中，選擇了 **"基本**"。 選擇 **"自訂"** 以提供自訂掃描的命令列參數。

Windows Defender 使用 Windows 更新用戶端下載並安裝簽名。 如果生成代理上的簽名更新失敗，則**HRESULT**錯誤代碼可能來自 Windows 更新。

有關 Windows 更新錯誤及其緩解的詳細資訊，請參閱[按元件更新錯誤代碼的 Windows 更新代碼](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference)和 TechNet 文章[Windows 更新代理 - 錯誤代碼](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)。

有關此任務的 YAML 配置的資訊，請查看我們的[反惡意軟體 YAML 選項](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>賓斯基任務

> [!NOTE]
> 在運行 BinSkim 任務之前，生成必須滿足以下條件之一：
>  - 生成從託管代碼生成二進位專案。
>  - 您已承諾要使用 BinSkim 分析的二進位工件。

任務配置的詳細資訊顯示在以下螢幕截圖和清單中。

![配置 BinSkim 生成任務](./media/security-tools/7-binskim-task-details.png)

- 將組建組態設置為調試，以便生成 .pdb 調試檔。 BinSkim 使用這些檔將輸出二進位檔案中的問題映射回原始程式碼。
- 為了避免研究和創建自己的命令列：
     - 在 **"類型"** 清單中，選擇 **"基本**"。
     - 在 **"函數**"清單中，選擇 **"分析**"。
- 在 **"目標"** 中，為檔、目錄或篩選器模式輸入一個或多個指定值。 這些指定者解析為一個或多個要分析的二進位檔案：
    - 多個指定的目標必須用分號分隔（;)。
    - 指定器可以是單個檔或包含萬用字元。
    - 目錄規範必須始終以 *\\結尾。
    - 範例：

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- 如果在 **"類型"** 清單中選擇**命令列**，則需要運行 binskim.exe：
     - 確保 binskim.exe 的第一個參數是動詞**分析**，後跟一個或多個路徑規範。 每個路徑可以是完整路徑，也可以是相對於原始目錄的路徑。
     - 多個目標路徑必須由空格分隔。
     - 您可以省略 **/o**或 **/輸出**選項。 為您添加或替換輸出值。
     - 標準命令列配置如下所示。

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > 如果為\\目標指定目錄，則尾隨 * 非常重要。

有關 BinSkim 命令列參數、按 ID 規則或結束代碼的詳細資訊，請參閱[BinSkim 使用者指南](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)。

有關此任務的 YAML 配置的資訊，請查看我們的[BinSkim YAML 選項](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>憑據掃描程式任務

任務配置的詳細資訊顯示在以下螢幕截圖和清單中。

![配置憑據掃描程式生成任務](./media/security-tools/3-taskdetails.png)

可用的選項包括：

  - **輸出格式**：可用值包括 TSV、CSV、SARIF 和**PREfast**。 **TSV** **CSV** **SARIF**
  - **工具版本**：我們建議您選擇 **"最新**"。
  - **掃描資料夾**：要掃描的存儲庫資料夾。
  - **搜索器檔案類型**：用於查找用於掃描的搜索者檔的選項。
  - **抑制檔** [：JSON](https://json.org/)檔可以抑制輸出日誌中的問題。 有關抑制方案的詳細資訊，請參閱本文的常見問題部分。
  - **詳細輸出**：不言自明。
  - **批次處理大小**：用於運行憑據掃描程式的併發執行緒數。 預設值為 20。 可能的值範圍為 1 到 2，147，483，647。
  - **匹配超時**：在放棄檢查之前嘗試搜索器匹配所花費的時間（以秒為單位）。
  - **檔掃描讀取緩衝區大小**：讀取內容時使用的緩衝區的大小（ 以位元組為單位）。 預設值為 524，288。  
  - **最大檔掃描讀取位元組**：內容分析期間從檔讀取的最大位元組數。 預設值為 104，857，600。
  - **控制項選項** > **運行此任務**：指定任務何時運行。 選擇 **"自訂條件"** 以指定更複雜的條件。
  - **版本**：Azure DevOps 中的生成任務版本。 此選項不經常使用。

有關此任務的 YAML 配置的資訊，請查看我們的[憑據掃描器 YAML 選項](yaml-configuration.md#credential-scanner-task)

## <a name="microsoft-security-risk-detection-task"></a>微軟安全風險檢測任務

> [!NOTE]
> 在使用 MSRD 任務之前，您必須使用 Microsoft 安全風險檢測 （MSRD） 服務創建和配置帳戶。 此服務需要單獨的載入流程。 與此擴展中的大多數其他任務不同，此任務需要使用 MSRD 進行單獨的訂閱。
>
> 請參閱[Microsoft 安全風險檢測](https://aka.ms/msrddocs)和[Microsoft 安全風險檢測：如何](https://docs.microsoft.com/security-risk-detection/how-to/)獲得說明。

配置此任務的詳細資訊將顯示在以下清單中。 對於任何 UI 元素，都可以將滑鼠懸停在該元素上以獲取有關説明。

   - **MSRD 的 Azure DevOps 服務終結點名稱**：Azure DevOps 服務終結點的泛型型別記憶板載的 MSRD 實例 URL 和 REST API 訪問權杖。 如果已創建此類終結點，可以在此處指定它。 否則，選擇 **"管理**"連結可為此 MSRD 任務創建和配置新的服務終結點。
   - **帳戶 ID**：可以從 MSRD 帳戶 URL 檢索的 GUID。
   - **到二進位的 URL**：公開可用的 URL 的分號分隔清單。 模糊電腦使用這些 URL 下載二進位檔案。
   - **種子檔的 URL：** 公開可用的 URL 的分號分隔清單。 模糊機使用這些 URL 下載種子。 如果將種子檔與二進位檔案一起下載，則指定此值是可選的。
   - **OS 平臺類型**：運行模糊作業的電腦的作業系統 （OS） 平臺。 可用值是**Windows**和**Linux**。
   - **Windows 版本 / Linux 版本**： 運行模糊作業的電腦的作業系統版本。 如果電腦具有不同的作業系統版本，則可以覆蓋預設值。
   - **包安裝腳本**：要在測試電腦上運行的腳本。 此腳本在提交模糊作業之前安裝測試目的程式及其依賴項。
   - **作業提交參數**：
       - **種子目錄**：包含種子的模糊電腦上的目錄的路徑。
       - **種子副檔名**：種子的檔案名副檔名。
       - **測試驅動程式可執行性**：模糊電腦上目標可執行檔的路徑。
       - **測試驅動程式可執行體系結構**：目標可執行檔的體系結構。 可用值為**x86**和**amd64**。
       - **測試驅動程式參數**：傳遞給測試可執行檔的命令列參數。 參數"%testfile%"（包括引號）將自動替換為目的檔案的完整路徑。 此檔由測試驅動程式解析，並且是必需的。
       - **測試驅動程式在測試完成後退出**：如果測試驅動程式要在完成後終止，請選擇此核取方塊。 如果測試驅動程式需要強制關閉，則清除它。
       - **最大持續時間（以秒為單位）：** 估計目的程式解析輸入檔所需的最長合理預期時間。 估計越準確，模糊應用運行的效率就越高。
       - **測試驅動程式可以重複運行**：如果測試驅動程式可以重複運行而不依賴于持久性或共用的全域狀態，請選擇此核取方塊。
       - **測試驅動程式可以重命名**：如果測試驅動程式可執行檔可以重命名並且仍然正常工作，請選擇此核取方塊。
       - **模糊應用程式作為單個作業系統進程運行**：如果測試驅動程式在單個作業系統進程下運行，請選擇此核取方塊。 如果測試驅動程式生成其他進程，則清除它。

有關此任務的 YAML 配置的資訊，請查看我們的[Microsoft 安全風險檢測 YAML 選項](yaml-configuration.md#microsoft-security-risk-detection-task)

## <a name="roslyn-analyzers-task"></a>羅斯林分析器任務

> [!NOTE]
> 在運行 Roslyn 分析器任務之前，您的生成需要滿足以下條件：
>
> - 組建定義包括內置的 MSBuild 或 VSBuild 生成任務，用於編譯 C# 或視覺化基本代碼。 分析器任務依賴于內置任務的輸入和輸出來運行 MSBuild 編譯，並啟用了 Roslyn 分析器。
> - 運行此生成任務的生成代理安裝了 Visual Studio 2017 版本 15.5 或更高版本，因此它使用編譯器版本 2.6 或更高版本。

任務配置的詳細資訊顯示在以下清單和注釋中。

可用的選項包括：

- **規則集**：值為**SDL 必需**值 **、SDL 建議**值或您自己的自訂規則集。
- **分析器版本**：我們建議您選擇 **"最新**"。
- **編譯器警告抑制檔**：包含被抑制的警告 ID 清單的文字檔。
- **控制項選項** > **運行此任務**：指定任務何時運行。 選擇 **"自訂條件"** 以指定更複雜的條件。

> [!NOTE]
>
> - Roslyn 分析器與編譯器集成，只能作為 csc.exe 編譯的一部分運行。 因此，此任務要求在生成中較早運行的編譯器命令重新播放或重新運行。 此重播或運行是通過查詢 MSBuild 生成任務日誌的視覺化工作室團隊服務 （VSTS） 來完成的。
>
>   任務沒有其他方法可以可靠地從組建定義中獲取 MSBuild 編譯命令列。 我們考慮添加一個自由格式文字方塊，以允許使用者輸入其命令列。 但是，很難使這些命令列保持最新且與主生成同步。
>
>   自訂生成需要重播整個命令集，而不僅僅是編譯器命令。 在這些情況下，啟用 Roslyn 分析儀並非微不足道或可靠。
>
> - 羅斯林分析器與編譯器集成。 要調用 Roslyn 分析器需要編譯。
>
>   此新的生成任務是通過重新編譯已生成的 C# 專案實現的。 新任務僅使用與原始任務相同的生成或組建定義中的 MSBuild 和 VSBuild 生成任務。 但是，在這種情況下，新任務在啟用了 Roslyn 分析器的情況下使用它們。
>
>   如果新任務在與原始任務相同的代理上運行，則新任務的輸出將覆蓋原始*任務的源資料夾中*的輸出。 儘管生成輸出相同，但我們建議您運行 MSBuild，將輸出複製到工件臨時目錄，然後運行 Roslyn 分析器。

有關 Roslyn 分析器任務的其他資源，請查看 Microsoft 文檔上的[基於 Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/)的分析器。

您可以在 NuGet 頁面 Microsoft 上找到此生成任務安裝和使用的分析器包[。](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)

有關此任務的 YAML 配置的資訊，請查看我們的[羅斯林分析儀 YAML 選項](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>TSLint 任務

有關 TSLint 的更多資訊，請訪問[TSLint GitHub 存儲庫](https://github.com/palantir/tslint)。

>[!NOTE] 
>您可能知道[，TSLint GitHub 存儲庫](https://github.com/palantir/tslint)主頁表示，TSLint 將在 2019 年某個時候被棄用。 微軟正在調查[ESLint](https://github.com/eslint/eslint)作為替代任務。

有關此任務的 YAML 配置的資訊，請查看我們的[TSLint YAML 選項](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>發佈安全分析日誌任務

任務配置的詳細資訊顯示在以下螢幕截圖和清單中。

![配置發佈安全分析日誌生成任務](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **專案名稱**：任何字串識別碼。
- **專案類型**：根據您的選擇，您可以將日誌發佈到 Azure DevOps 伺服器或生成代理可訪問的共用檔。
- **工具**：您可以選擇保留特定工具的日誌，也可以選擇 **"所有工具**"來保留所有日誌。

有關此任務的 YAML 配置的資訊，請查看我們的[發佈安全性記錄檔 YAML 選項](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>安全報告工作

安全報告配置的詳細資訊顯示在以下螢幕截圖和清單中。

![配置安全報告生成任務](./media/security-tools/4-createsecurityanalysisreport600.png)

- **報表**： 選擇任何**管道主控台****、TSV 檔和** **Html 檔**格式。 為每個選定的格式創建一個報表檔。
- **工具**：選擇組建定義中需要對檢測到的問題進行摘要的工具。 對於所選的每個工具，可能有一個選項可以選擇是只看到錯誤還是在摘要報告中同時看到錯誤和警告。
- **高級選項**：如果所選工具之一沒有日誌，您可以選擇記錄警告或錯誤。 如果記錄錯誤，任務將失敗。
- **基本日誌資料夾**：您可以自訂要找到日誌的基本日誌資料夾。 但通常不使用此選項。

有關此任務的 YAML 配置的資訊，請查看我們的[安全報告 YAML 選項](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>分析後任務

任務配置的詳細資訊顯示在以下螢幕截圖和清單中。

![配置分析後生成任務](./media/security-tools/a-post-analysis600.png)

- **工具**：選擇組建定義中要有條件地注入生成中斷的工具。 對於所選的每個工具，可能有一個選項可以選擇是僅在錯誤上中斷，還是同時選擇錯誤和警告。
- **報表**：您可以選擇編寫導致生成中斷的結果。 結果將寫入 Azure DevOps 主控台視窗和日誌檔。
- **高級選項**：如果所選工具之一沒有日誌，您可以選擇記錄警告或錯誤。 如果記錄錯誤，任務將失敗。

有關此任務的 YAML 配置的資訊，請查看我們的[後分析 YAML 選項](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>後續步驟

有關基於 YAML 的配置的資訊，請參閱我們的[YAML 配置指南](yaml-configuration.md)。

如果您對安全代碼分析擴展和所提供的工具有任何疑問，請查看[我們的常見問題頁面](security-code-analysis-faq.md)。
