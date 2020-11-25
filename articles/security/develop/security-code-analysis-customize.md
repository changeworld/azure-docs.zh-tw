---
title: 自訂 Microsoft 安全性程式碼分析工作
titleSuffix: Azure
description: 本文說明如何自訂 Microsoft 安全性程式碼分析延伸模組中的工作
author: sukhans
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
ms.openlocfilehash: 4016e1dd055b45f9cd59a172d0e71ef95fec1c40
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008679"
---
# <a name="configure-and-customize-the-build-tasks"></a>設定和自訂群組建工作

本文將詳細說明每個組建工作中可用的設定選項。 本文從安全性程式碼分析工具的工作開始。 它會以後置處理工作做為結尾。

## <a name="anti-malware-scanner-task"></a>反惡意程式碼掃描器工作

>[!NOTE]
> 反惡意程式碼掃描器組建工作需要已啟用 Windows Defender 的組建代理程式。 Hosted Visual Studio 2017 和更新版本提供這類代理程式。 組建工作不會在 Visual Studio 2015 裝載的代理程式上執行。
>
> 雖然無法在這些代理程式上更新簽章，但簽章應該一律小於三個小時。

工作設定的詳細資料會顯示在下列螢幕擷取畫面和文字中。

![設定反惡意程式碼掃描器組建工作](./media/security-tools/5-add-anti-malware-task600.png)

在螢幕擷取畫面的 [ **類型** ] 清單方塊中，選取 [ **基本** ]。 選取 [ **自訂** ] 可提供自訂掃描的命令列引數。

Windows Defender 使用 Windows Update 用戶端來下載和安裝簽章。 如果您的組建代理程式上的簽章更新失敗，則 **HRESULT** 錯誤碼可能來自 Windows Update。

如需有關 Windows Update 錯誤及其緩和措施的詳細資訊，請參閱 [Windows Update 錯誤碼（依元件](/windows/deployment/update/windows-update-error-reference) ）和 TechNet 文章 [Windows Update 代理程式錯誤碼](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)。

如需這項工作 YAML 設定的相關資訊，請查看我們的 [反惡意程式碼 YAML 選項](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>Binskim 等工作

> [!NOTE]
> 您的組建必須符合下列其中一項條件，您才能執行 Binskim 等工作：
>  - 您的組建會從 managed 程式碼產生二進位成品。
>  - 您已認可二進位成品，而您想要使用 Binskim 等進行分析。

工作設定的詳細資料會顯示在下列螢幕擷取畫面和清單中。

![設定 Binskim 等組建工作](./media/security-tools/7-binskim-task-details.png)

- 將組建設定設定為 Debug，以產生 .pdb 偵錯工具檔。 Binskim 等會使用這些檔案，將輸出二進位檔中的問題對應回原始程式碼。
- 若要避免研究和建立您自己的命令列：
     - 在 [ **類型** ] 清單中選取 [ **基本**]。
     - 在 [ **函數** ] 清單中，選取 [ **分析**]。
- 在 [ **目標**] 中，輸入一或多個適用于檔案、目錄或篩選器模式的規範。 這些規範會解析為一或多個要分析的二進位檔：
    - 多個指定的目標必須以分號分隔 (; ) 。
    - 規範可以是單一檔案或包含萬用字元。
    - 目錄規格的結尾一律必須是 \\ *。
    - 範例：

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

- 如果您在 [**類型**] 清單中選取 [**命令列**]，則必須執行 binskim.exe：
     - 請確定要 binskim.exe 的第一個引數是動詞 **分析** ，後面接著一或多個路徑規格。 每個路徑可以是完整路徑或相對於來原始目錄的路徑。
     - 多個目標路徑必須以空格分隔。
     - 您可以省略 **/o** 或 **/output** 選項。 系統會為您新增或取代輸出值。
     - 標準命令列設定如下所示。

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> \\如果您為目標指定目錄，則尾端 * 相當重要。

如需有關 Binskim 等命令列引數、規則（依識別碼或結束代碼）的詳細資訊，請參閱 [Binskim 等使用者指南](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)。

如需這項工作 YAML 設定的相關資訊，請參閱我們的 [BINSKIM 等 YAML 選項](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>認證掃描器工作

工作設定的詳細資料會顯示在下列螢幕擷取畫面和清單中。

![設定認證掃描器組建工作](./media/security-tools/3-taskdetails.png)

可用的選項包括：
  - **顯示名稱**： Azure DevOps 工作的名稱。 預設值為 [執行認證掃描器]
  - **工具主要版本**：可用的值包括 **CredScan V2**、 **CredScan V1**。 我們建議客戶使用 **CredScan V2** 版本。
  - **輸出格式**：可用的值包括 **TSV**、 **CSV**、 **SARIF** 和 **PREfast**。
  - **工具版本**：建議您選取 [ **最新**]。
  - **掃描資料夾**：要掃描的存放庫資料夾。
  - **Searchers 檔案類型**：用於尋找用來掃描之 Searchers 檔的選項。
  - **Suppressions File** 隱藏隱藏檔案： [JSON](https://json.org/)檔案可以隱藏輸出記錄檔中的問題。 如需隱藏專案案例的詳細資訊，請參閱本文的常見問題一節。
  - **詳細資訊輸出**：一目了然。
  - **批次大小**：用來執行認證掃描器的平行線程數目。 預設值為 20。 可能的值範圍從1到2147483647。
  - 比對 **Timeout**：放棄檢查之前，嘗試搜尋結果相符的時間量（以秒為單位）。
  - **檔掃描讀取緩衝區大小**：讀取內容時使用的緩衝區大小（以位元組為單位）。 預設值為524288。  
  - 檔案 **掃描讀取位元組上限**：在內容分析期間從檔案讀取的最大位元組數目。 預設值為104857600。
  - **控制項選項**  > **執行這** 項工作：指定工作將執行的時間。 選取 [ **自訂條件** ] 以指定更複雜的條件。
  - **版本**： Azure DevOps 內的組建工作版本。 此選項不常使用。

如需這項工作 YAML 設定的相關資訊，請查看我們的 [認證掃描器 YAML 選項](yaml-configuration.md#credential-scanner-task)

## <a name="roslyn-analyzers-task"></a>Roslyn 分析器工作

> [!NOTE]
> 您的組建必須符合下列條件，才能執行 Roslyn 分析器工作：
>
> - 您的組建定義包含內建的 MSBuild 或 VSBuild build 工作，可編譯 c # 或 Visual Basic 程式碼。 分析器工作依賴內建工作的輸入和輸出，以執行已啟用 Roslyn 分析器的 MSBuild 編譯。
> - 執行此組建工作的組建代理程式已安裝 Visual Studio 2017 15.5 版或更新版本，使其使用編譯器2.6 版或更新版本。

工作設定的詳細資料會顯示在下列清單中，並加以說明。

可用的選項包括：

- **規則集**：值為 **sdl 必要**、 **sdl 建議** 或您自己的自訂規則集。
- **分析器版本**：建議您選取 [ **最新**]。
- **編譯器警告** 隱藏檔案：包含隱藏之警告識別碼清單的文字檔。
- **控制項選項**  > **執行這** 項工作：指定工作將執行的時間。 選擇 [ **自訂條件** ] 以指定更複雜的條件。

> [!NOTE]
>
> - Roslyn 分析器與編譯器整合，而且只能在 csc.exe 編譯時執行。 因此，此工作需要重新執行先前在組建中執行的編譯器命令，或重新執行。 這項重新執行作業的完成方式是查詢先前) Visual Studio Team Services MSBuild 組建工作記錄的 Azure DevOps (。
>
>   沒有其他方法可讓工作從組建定義中可靠地取得 MSBuild 編譯命令列。 我們已考慮新增自由格式的文字方塊，讓使用者可以輸入其命令列。 但接著很難讓這些命令列保持在最新狀態，並與主要組建同步。
>
>   自訂群組建需要重新執行一組完整的命令，而不只是編譯器命令。 在這些情況下，啟用 Roslyn 分析器並非簡單或可靠。
>
> - Roslyn 分析器會與編譯器整合。 若要叫用，Roslyn 分析器需要編譯。
>
>   這項新的組建工作是藉由重新編譯已經建立的 c # 專案來執行。 新的工作只會在與原始工作相同的組建或組建定義中使用 MSBuild 和 VSBuild 組建工作。 不過，在此情況下，新的工作會在啟用 Roslyn 分析器時使用它們。
>
>   如果新的工作在與原始工作相同的代理程式上執行，則新工作的輸出會覆寫 [來源] 資料夾中 *原始工作的* 輸出。 雖然組建輸出相同，但我們建議您執行 MSBuild、將輸出複製到構件臨時目錄，然後執行 Roslyn 分析器。

如需 Roslyn 分析器工作的其他資源，請參閱 Microsoft Docs 上以 [Roslyn 為基礎的分析器](/dotnet/standard/analyzers/api-analyzer) 。

您可以在 NuGet 頁面 [CodeAnalysis. 將 microsoft.codeanalysis.fxcopanalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)上，找到此組建工作所安裝和使用的分析器套件。

如需這項工作 YAML 設定的相關資訊，請參閱我們的 [Roslyn 分析器 YAML 選項](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>TSLint 工作

如需 TSLint 的詳細資訊，請移至 [TSLint GitHub](https://github.com/palantir/tslint)存放庫。

>[!NOTE] 
>您可能會注意到， [TSLint GitHub](https://github.com/palantir/tslint) 存放庫首頁指出 TSLint 將在2019時淘汰。 Microsoft 正在調查 [ESLint](https://github.com/eslint/eslint) 為替代工作。

如需這項工作 YAML 設定的相關資訊，請參閱我們的 [TSLINT YAML 選項](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>發佈安全性分析記錄工作

工作設定的詳細資料會顯示在下列螢幕擷取畫面和清單中。

![設定發佈安全性分析記錄組建工作](./media/security-tools/9-publish-security-analsis-logs600.png)  

- 成品 **名稱**：任何字串識別碼。
- 成品 **類型**：根據您的選擇，您可以將記錄發行至您的 Azure DevOps Server 或可供組建代理程式存取的共用檔案。
- **工具**：您可以選擇保留特定工具的記錄，也可以選取 **所有工具** 來保留所有記錄。

如需這項工作 YAML 設定的相關資訊，請查看我們的 [發行安全性記錄 YAML 選項](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>安全性報告工作

安全性報告設定的詳細資料會顯示在下列螢幕擷取畫面和清單中。

![設定安全性報表組建工作](./media/security-tools/4-createsecurityanalysisreport600.png)

- **報表**：選取任何 **管線主控台**、 **TSV** 檔案，以及 **Html 檔** 格式。 針對每個選取的格式建立一個報表檔案。
- **工具**：在您的組建定義中選取您想要摘要偵測到的問題的工具。 針對每個選取的工具，可能會有一個選項可選擇您是否只看到錯誤，或在摘要報告中看到錯誤和警告。
- **Advanced Options**：如果未選取其中一個工具的記錄，您可以選擇記錄警告或錯誤。 如果您記錄錯誤，工作將會失敗。
- **基底記錄資料夾**：您可以自訂要在其中尋找記錄檔的基底記錄檔資料夾。 但通常不會使用此選項。

如需這項工作 YAML 設定的相關資訊，請參閱我們的 [安全性報告 YAML 選項](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>分析後工作

工作設定的詳細資料會顯示在下列螢幕擷取畫面和清單中。

![設定分析後組建工作](./media/security-tools/a-post-analysis600.png)

- **工具**：選取您的組建定義中，您想要有條件地插入組建中斷的工具。 針對每個選取的工具，可能會有一個選項可選取您只想要在錯誤發生時，還是在錯誤和警告上中斷。
- **報表**：您可以選擇性地撰寫造成組建中斷的結果。 結果會寫入 Azure DevOps 主控台視窗和記錄檔。
- **Advanced Options**：如果未選取其中一個工具的記錄，您可以選擇記錄警告或錯誤。 如果您記錄錯誤，工作將會失敗。

如需這項工作 YAML 設定的相關資訊，請查看我們的 [文章分析 YAML 選項](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>後續步驟

如需 YAML 為基礎的設定相關資訊，請參閱我們的 [YAML 設定指南](yaml-configuration.md)。

如果您對安全性程式碼分析延伸模組和所提供的工具有進一步的疑問，請參閱 [我們的常見問題頁面](security-code-analysis-faq.md)。