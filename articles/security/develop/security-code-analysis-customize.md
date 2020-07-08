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
ms.openlocfilehash: 744b186b32927f81be21ff067c9195bddb33c416
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85362086"
---
# <a name="configure-and-customize-the-build-tasks"></a>設定和自訂群組建工作

本文詳細說明每個組建工作中可用的設定選項。 本文會從安全性程式碼分析工具的工作開始。 結尾為後置處理工作。

## <a name="anti-malware-scanner-task"></a>反惡意程式碼掃描程式工作

>[!NOTE]
> 反惡意程式碼掃描器組建工作需要有已啟用 Windows Defender 的組建代理程式。 託管 Visual Studio 2017 和更新版本會提供這類代理程式。 組建工作不會在 Visual Studio 2015 主控的代理程式上執行。
>
> 雖然無法更新這些代理程式上的簽章，但簽章應一律小於三小時的舊版本。

下列螢幕擷取畫面和文字會顯示工作設定的詳細資料。

![設定反惡意程式碼掃描器組建工作](./media/security-tools/5-add-anti-malware-task600.png)

在螢幕擷取畫面的 [**類型**] 清單方塊中，已選取 [**基本**]。 選取 [**自訂**] 以提供自訂掃描的命令列引數。

Windows Defender 會使用 Windows Update 用戶端來下載和安裝簽章。 如果您的組建代理程式上的簽章更新失敗，則**HRESULT**錯誤碼可能來自 Windows Update。

如需有關 Windows Update 錯誤及其緩和措施的詳細資訊，請參閱[依元件 Windows Update 錯誤碼](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference)和 TechNet 文章[Windows Update 代理程式錯誤代碼](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)。

如需這項工作之 YAML 設定的相關資訊，請查看我們的[反惡意程式碼 YAML 選項](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>BinSkim 工作

> [!NOTE]
> 您的組建必須符合下列其中一項條件，才可以執行 BinSkim 工作：
>  - 您的組建會從 managed 程式碼產生二進位成品。
>  - 您已認可您想要使用 BinSkim 分析的二進位成品。

下列螢幕擷取畫面和清單中會顯示工作設定的詳細資料。

![設定 BinSkim 組建工作](./media/security-tools/7-binskim-task-details.png)

- 將組建設定設為 Debug，以便產生 .pdb Debug 檔案。 BinSkim 會使用這些檔案，將輸出二進位檔中的問題對應回原始碼。
- 若要避免研究和建立您自己的命令列：
     - 在 [**類型**] 清單中，選取 [**基本**]。
     - 在 [**函數**] 清單中，選取 [**分析**]。
- 在 [**目標**] 中，輸入檔案、目錄或篩選模式的一或多個規範。 這些規範會解析為一或多個要分析的二進位檔：
    - 多個指定的目標必須以分號分隔（;)。
    - 規範可以是單一檔案或包含萬用字元。
    - 目錄規格的結尾必須是 \\ *。
    - 範例：

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- 如果您在 [**類型**] 清單中選取 [**命令列**]，則必須執行 binskim.exe：
     - 請確定 binskim.exe 的第一個引數是動詞**分析**，後面接著一或多個路徑規格。 每個路徑可以是完整路徑或相對於來原始目錄的路徑。
     - 多個目標路徑必須以空格分隔。
     - 您可以省略 **/o**或 **/output**選項。 系統會為您加入或取代輸出值。
     - 標準命令列設定如下所示。

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > \\如果您為目標指定目錄，結尾的 * 就很重要。

如需有關 BinSkim 命令列引數、依識別碼或結束代碼的規則的詳細資訊，請參閱[BinSkim 使用者指南](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)。

如需這項工作之 YAML 設定的相關資訊，請參閱我們的[BINSKIM YAML 選項](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>認證掃描器工作

下列螢幕擷取畫面和清單中會顯示工作設定的詳細資料。

![設定認證掃描器組建工作](./media/security-tools/3-taskdetails.png)

可用的選項包括：
  - **顯示名稱**： Azure DevOps 工作的名稱。 預設值為執行認證掃描器
  - **工具主要版本**：可用的值包括**CredScan V2**、 **CredScan V1**。 我們建議客戶使用**CredScan V2**版本。
  - **輸出格式**：可用的值包括**TSV**、 **CSV**、 **SARIF**和**PREfast**。
  - **工具版本**：建議您選取 [**最新**]。
  - **掃描資料夾**：要掃描的存放庫資料夾。
  - **Searchers 檔案類型**：用於尋找用於掃描之 Searchers 檔案的選項。
  - **禁止**隱藏檔案： [JSON](https://json.org/)檔案可以抑制輸出記錄檔中的問題。 如需隱藏案例的詳細資訊，請參閱本文的常見問題一節。
  - **詳細資訊輸出**：一目了然。
  - **批次大小**：用來執行認證掃描器的平行線程數目。 預設值為 20。 可能的值範圍從1到2147483647。
  - 比對**超時**：在放棄檢查之前，嘗試搜尋搜尋符合的時間長度（以秒為單位）。
  - 檔案**掃描讀取緩衝區大小**：讀取內容時所使用的緩衝區大小（以位元組為單位）。 預設值為524288。  
  - 檔案**掃描讀取位元組上限**：在內容分析期間，從檔案讀取的最大位元組數目。 預設值為104857600。
  - **控制項選項**  > **執行此**工作：指定工作將執行的時間。 選取 [**自訂條件**] 以指定更複雜的條件。
  - **版本**： Azure DevOps 內的組建工作版本。 此選項不常使用。

如需這項工作之 YAML 設定的相關資訊，請查看[認證掃描器 YAML 選項](yaml-configuration.md#credential-scanner-task)

## <a name="roslyn-analyzers-task"></a>Roslyn 分析器工作

> [!NOTE]
> 您的組建必須符合下列條件，您才能執行 Roslyn 分析器工作：
>
> - 您的組建定義包含用來編譯 c # 或 Visual Basic 程式碼的內建 MSBuild 或 VSBuild 組建工作。 分析器工作會依賴內建工作的輸入和輸出，以執行已啟用 Roslyn 分析器的 MSBuild 編譯。
> - 執行此組建工作的組建代理程式已安裝 Visual Studio 2017 15.5 版或更新版本，因此它會使用編譯器2.6 版或更新版本。

下列清單和附注會顯示工作設定的詳細資料。

可用的選項包括：

- **規則集**：值是**sdl 必要**、 **sdl 建議**或您自己的自訂規則集。
- **分析器版本**：建議您選取 [**最新**]。
- **編譯器警告禁止**檔案：包含隱藏的警告識別碼清單的文字檔。
- **控制項選項**  > **執行此**工作：指定工作將執行的時間。 選擇 [**自訂條件**] 以指定更複雜的條件。

> [!NOTE]
>
> - Roslyn 分析器會與編譯器整合，而且只能在 csc.exe 編譯過程中執行。 因此，此工作需要重新執行稍早在組建中執行的編譯器命令，或再次執行。 這個重新執行或執行是藉由查詢 MSBuild 組建工作記錄 Visual Studio Team Services （VSTS）來完成。
>
>   沒有其他方法可讓工作從組建定義中可靠地取得 MSBuild 編譯命令列。 我們考慮加入一個自由格式的文字方塊，讓使用者可以輸入其命令列。 但是，將這些命令列保持在最新狀態，並與主要組建同步處理會很困難。
>
>   自訂群組建需要重新執行整個命令集，而不只是編譯器命令。 在這些情況下，啟用 Roslyn 分析器並不簡單，也不可靠。
>
> - Roslyn 分析器已與編譯器整合。 若要叫用，Roslyn 分析器需要編譯。
>
>   這個新的組建工作是藉由重新編譯已經建立的 c # 專案來執行。 新工作只會在與原始工作相同的組建或組建定義中使用 MSBuild 和 VSBuild 組建工作。 不過，在此情況下，新的工作會使用它們，並啟用 Roslyn 分析器。
>
>   如果新工作在與原始工作相同的代理程式上執行，新工作的輸出會覆寫 [ *s*源] 資料夾中原始工作的輸出。 雖然組建輸出是相同的，但我們建議您執行 MSBuild、將輸出複製到成品臨時目錄，然後執行 Roslyn 分析器。

如需 Roslyn 分析器工作的其他資源，請參閱 Microsoft Docs 上以[Roslyn 為基礎的分析器](https://docs.microsoft.com/dotnet/standard/analyzers/)。

您可以在[CodeAnalysis FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)的 NuGet 頁面上找到此組建工作所安裝和使用的分析器套件。

如需這項工作之 YAML 設定的相關資訊，請參閱[Roslyn 分析器 YAML 選項](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>TSLint 工作

如需 TSLint 的詳細資訊，請移至[TSLint GitHub](https://github.com/palantir/tslint)存放庫。

>[!NOTE] 
>您可能會注意到， [TSLint GitHub](https://github.com/palantir/tslint)存放庫首頁會指出 TSLint 將于2019時淘汰。 Microsoft 正在調查[ESLint](https://github.com/eslint/eslint)做為替代工作。

如需這項工作之 YAML 設定的相關資訊，請參閱我們的[TSLINT YAML 選項](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>發行安全性分析記錄工作

下列螢幕擷取畫面和清單中會顯示工作設定的詳細資料。

![設定發行安全性分析記錄組建工作](./media/security-tools/9-publish-security-analsis-logs600.png)  

- 成品**名稱**：任何字串識別碼。
- 成品類型：根據您的選取**專案**，您可以將記錄發行到 Azure DevOps Server 或可供組建代理程式存取的共用檔案。
- **工具**：您可以選擇保留特定工具的記錄檔，也可以選取 [**所有工具**] 來保留所有記錄。

如需這項工作之 YAML 設定的相關資訊，請參閱我們的[發佈安全性記錄 YAML 選項](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>安全性報告工作

下列螢幕擷取畫面和清單中會顯示安全性報告設定的詳細資料。

![設定安全性報告組建工作](./media/security-tools/4-createsecurityanalysisreport600.png)

- **報告**：選取任何一個**管線主控台**、 **TSV**檔案和**Html 檔**格式。 會針對每個選取的格式建立一個報表檔案。
- **工具**：在您的組建定義中，選取您想要偵測到的問題摘要的工具。 針對每個選取的工具，可能會有選項可選取您是否只看到錯誤，或在摘要報告中同時看到錯誤和警告。
- **Advanced Options**：如果選取的其中一個工具沒有任何記錄，您可以選擇記錄警告或錯誤。 如果您記錄錯誤，工作將會失敗。
- **基本記錄檔資料夾**：您可以自訂要在其中尋找記錄檔的 [基底記錄檔] 資料夾。 但通常不會使用此選項。

如需這項工作之 YAML 設定的相關資訊，請參閱我們的[安全性報告 YAML 選項](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>後續分析工作

下列螢幕擷取畫面和清單中會顯示工作設定的詳細資料。

![設定後置分析組建工作](./media/security-tools/a-post-analysis600.png)

- **工具**：在您的組建定義中，選取您想要有條件地插入組建中斷的工具。 針對每個選取的工具，可能會有選項可以選擇要只針對錯誤或在錯誤和警告上中斷。
- **報表**：您可以選擇性地撰寫導致建立中斷的結果。 結果會寫入 Azure DevOps 主控台視窗和記錄檔。
- **Advanced Options**：如果選取的其中一個工具沒有任何記錄，您可以選擇記錄警告或錯誤。 如果您記錄錯誤，工作將會失敗。

如需這項工作之 YAML 設定的相關資訊，請參閱我們的[Post 分析 YAML 選項](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>後續步驟

如需 YAML 為基礎之設定的相關資訊，請參閱我們的[YAML 設定指南](yaml-configuration.md)。

如果您對安全性程式碼分析延伸模組和提供的工具有進一步的疑問，請查看[我們的常見問題頁面](security-code-analysis-faq.md)。