---
title: Microsoft Azure 安全性程式碼分析工作自訂指南
description: 本文說明在 Microsoft 安全性程式碼分析延伸模組中自訂所有工作的清單 YAML 設定選項
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 11/29/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 6985107dd8f13e26875cf5ea7428b3280d00cea1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85367252"
---
# <a name="yaml-configuration-options-to-customize-the-build-tasks"></a>自訂群組建工作的 YAML 設定選項

本文列出每個組建工作中可用的所有 YAML 設定選項。 本文從安全性程式碼分析工具的工作開始。 它會以後置處理工作做為結尾。

## <a name="anti-malware-scanner-task"></a>反惡意程式碼掃描器工作

| **InputType**      | **型別**     | **適用**            | **必要** | **預設值**             | **挑選清單 (的選項) **                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| InputType | 挑選清單 | always | True | 基本 | 基本、自訂 | 
| ScanType | 挑選清單 | InputType = 基本 | True | CustomScan | CustomScan, FullSystemScan, QuickScan, YourConfiguredScan | 要用於反惡意程式碼掃描的掃描類型。
| FileDirPath | filePath | ScanType = CustomScan | True | $ (Build. Build.stagingdirectory)  |  | 表示要掃描的檔案或目錄。
| DisableRemediation | boolean | ScanType = CustomScan | False | true |  | 核取時：會忽略 1) 的檔案排除。 2) 掃描保存檔案。 3) 在偵測之後不會套用動作。 4) 在偵測之後不會寫入事件記錄檔專案。 5) 自訂掃描的偵測不會顯示在使用者介面中。 6) 主控台輸出將會顯示來自自訂掃描的偵測清單。
| BootSectorScan | boolean | ScanType = CustomScan | False | false |  | 如果選取此選項，則會啟用開機磁區掃描。
| 引數 | 字串 | InputType = 自訂 | True | -Scan-ScanType 3-DisableRemediation-File $ (Build. Build.stagingdirectory)  |  | 命令列引數，其中-File 的引數是絕對路徑，或在您的組建代理程式上預先定義的 $ (Build.stagingdirectory) 的相對路徑。 注意：如果您未提供-File 的引數做為最後一個引數，它會預設為 $ (Build.stagingdirectory) 。 您也可以提供 MpCmdRun.exe 工具所允許的引數。<br/><br/>如需此工具的命令列引數的詳細資訊，請輸入 <strong>-h</strong> 或 <strong>-？</strong> 在 [引數] 欄位中，執行組建工作。
| EnableServices | boolean | always | True | false |  | 若選取此選項，將會嘗試啟用 Windows Update 的必要服務，以防它們停用。<br/>**注意**：請確定群組原則未停用服務，而執行此組建的帳戶具有系統管理員許可權。
| SupportLogOnError | boolean | always | True | false |  | 如果選取此選項，則會在發生錯誤時收集支援檔案以進行診斷。 這可能需要幾分鐘的時間。<br/>**注意**：請確認執行此組建的帳戶具有系統管理員許可權。
| TreatSignatureUpdateFailureAs | 挑選清單 | always | True | 警告 | 錯誤、標準、警告 | 當簽章無法在執行時間更新時，所使用的記錄層級。 當設定為 [ **錯誤**] 時，更新簽章的失敗將會導致組建工作失敗。 請注意，在裝載的組建代理程式上，簽章更新通常會失敗，即使簽章可能相當於最新的 (小於3小時的舊) 也是一樣。
| SignatureFreshness | 挑選清單 | always | True | UpToDate | OneDay、ThreeDays、TwoDays、UpToDate | 反惡意程式碼簽章允許的最長使用期限。 如果無法更新簽章，且該簽章早于此值，則組建工作會根據 [驗證簽章 **存留期** ] 欄位中選取的值來進行。 注意：如果您選擇 [ **最**新]，則簽章最多可達3小時的舊。
| TreatStaleSignatureAs | 挑選清單 | always | True | 錯誤 | 錯誤、標準、警告 | 當簽章存留期比選取的反惡意程式碼簽章 **存留期**還舊時，所使用的記錄層級。 過期的簽章可視為 **警告** 或 **資訊** ，以繼續進行反惡意程式碼掃描，但不建議這麼做。

## <a name="binskim-task"></a>Binskim 等工作

| **InputType**      | **型別**     | **適用**            | **必要** | **預設值**             | **挑選清單 (的選項) **                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| InputType | 挑選清單 | always | True | 基本 | 基本、命令列 | 
| 引數 | 字串 | InputType = 命令列 | True |  |  | 要執行的標準 Binskim 等命令列引數。 輸出路徑將會移除並被取代。<br>如需此工具的命令列引數的詳細資訊，請在 [引數] 欄位 **中輸入說明** ，然後執行組建工作。
| 函式 | 挑選清單 | InputType = 基本 | True | 分析 | 分析、傾印、exportConfig、exportRules | 
| AnalyzeTarget | filePath | InputType = 基本 && 函數 = 分析 | True | $ (Build. ArtifactStagingDirectory) \* .dll;<br>$ (Build. ArtifactStagingDirectory) \* .exe |  | 可解析為一或多個要分析之二進位檔的檔案、目錄或篩選模式的一或多個規範。  ( '; ' 分隔清單) 
| AnalyzeSymPath | 字串 | InputType = 基本 && 函數 = 分析 | False |  |  | 目標的符號檔路徑。
| AnalyzeConfigPath | 字串 | InputType = 基本 && 函數 = 分析 | False | default |  | 將用來設定分析之原則檔案的路徑。 傳遞 ' default ' 的值以使用內建設定。
| AnalyzePluginPath | 字串 | InputType = 基本 && 函數 = 分析 | False |  |  | 將針對分析集中的所有目標叫用之外掛程式的路徑。
| AnalyzeRecurse | boolean | InputType = 基本 && 函數 = 分析 | False | true |  | 在評估檔案規範引數時，遞迴成為子目錄。
| AnalyzeVerbose | boolean | InputType = 基本 && 函數 = 分析 | False | false |  | 發出詳細資訊輸出。 產生的完整報告旨在提供適當的合規性案例證明。
| AnalyzeHashes | boolean | InputType = 基本 && 函數 = 分析 | False | false |  | 發出 SARIF 報表時，輸出 SHA-256 分析目標的雜湊。
| AnalyzeStatistics | boolean | InputType = 基本 && 函數 = 分析 | False | false |  | 產生分析會話的計時和其他統計資料。
| AnalyzeEnvironment | boolean | InputType = 基本 && 函數 = 分析 | False | false |  | 執行至輸出檔案的記錄機器環境詳細資料。 警告：此選項會記錄可能的機密資訊 (例如) 所有已發出記錄的環境變數值。
| ExportRulesOutputType | 挑選清單 | InputType = Basic && Function = exportRules | False | SARIF | SARIF、SonarQube | 要輸出的規則描述元檔案類型。 這會包含在「發行安全性分析記錄」組建工作所發行的 Binskim 等 logs 資料夾中。
| DumpTarget | filePath | InputType = Basic && Function = 傾印 | True | $ (Build. ArtifactStagingDirectory)  |  | 可解析為一或多個要分析之二進位檔的檔案、目錄或篩選模式的一或多個規範。  ( '; ' 分隔清單) 
| DumpRecurse | boolean | InputType = Basic && Function = 傾印 | False | true |  | 在評估檔案規範引數時，遞迴成為子目錄。
| DumpVerbose | boolean | InputType = Basic && Function = 傾印 | False | true |  | 發出詳細資訊輸出。 產生的完整報告旨在提供適當的合規性案例證明。
| toolVersion | 挑選清單 | always | False | Latest | 1.5.0、最新、LatestPreRelease | 要執行的工具版本。

## <a name="credential-scanner-task"></a>認證掃描器工作

| **InputType**      | **型別**     | **適用**            | **必要** | **預設值**             | **挑選清單 (的選項) **                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OutputFormat | 挑選清單 | always | False | pre | csv、pre、tsv | 認證掃描器結果檔案的輸出格式。
| toolVersion | 挑選清單 | always | False | Latest | 1.27.7、最新、LatestPreRelease | 要執行的工具版本。
| scanFolder | filePath | always | False | $ (Build. SourcesDirectory)  |  | 您存放庫中的資料夾，用來掃描認證。
| searchersFileType | 挑選清單 | always | False | 預設 | Custom、Default、DefaultAndCustom | 用來找出用於掃描之 searchers 檔的選項。
| searchersFile | filePath | searchersFileType = = Custom 或 searchersFileType = = DefaultAndCustom | False |  |  | 要執行之檢查的認證掃描器 searchers 設定檔。 您可以藉由提供認證掃描器 searchers 檔的路徑清單（以逗號分隔），包含和使用多個值。
| suppressionsFile | filePath | always | False |  |  | 用於隱藏輸出記錄檔中問題的認證掃描器隱藏檔案。
| suppressAsError | boolean | always | False | false |  | 隱藏的相符專案會輸出到輸出檔 [-O]-相符。[-f]，而不是預設隱藏的輸出檔 [-O]-抑制。[-f]。  (預設為 ' False ' ) 
| verboseOutput | boolean | always | False | false |  | 輸出詳細資訊。
| batchSize | 字串 | always | False |  |  | 用來平行執行認證掃描器的平行線程數目。  (預設為 20) <br/>值必須在1-2147483647 的範圍內。
| RegExMatchTimeoutInSeconds | 字串 | always | False |  |  | 在放棄檢查之前，嘗試搜尋結果所花費的時間量（以秒為單位）。<br/>新增 ``-Co RegexMatchTimeoutInSeconds=<Value>`` 至命令列。
| fileScanReadBufferSize | 字串 | always | False |  |  | 讀取內容時的緩衝區大小（以位元組為單位）。  (預設為 524288) <br/>新增 ``-Co FileScanReadBufferSize=<Value>`` 至命令列。
| maxFileScanReadBytes | 字串 | always | False |  |  | 在內容分析期間，從指定檔案讀取的最大位元組數目。  (預設為 104857600) <br/>新增 ``-Co MaxFileScanReadBytes=<Value>`` 至命令列。

## <a name="roslyn-analyzers-task"></a>Roslyn 分析器工作

| **InputType**      | **型別**     | **適用**            | **必要** | **預設值**             | **挑選清單 (的選項) **                                   | **描述**                                                                                                                                                                                                                                                                                                                   |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| userProvideBuildInfo | 挑選清單 | always | True | 自動 | auto、msBuildInfo | 適用于 Roslyn 分析的使用者提供 MSBuild 版本、MSBuild 架構和組建命令列的選項。 如果選取 [ **自動** ]，此工作將會從先前的 **MSBuild**、 **VSBuild**和（或） **.net Core** (中取得相同管線中組建) 工作的組建資訊。
| msBuildVersion | 挑選清單 | userProvideBuildInfo = = msBuildInfo | True | 16.0 | 15.0、16。0 | MSBuild 版本。
| msBuildArchitecture | 挑選清單 | userProvideBuildInfo = = msBuildInfo | True | x86 | DotNetCore、x64、x86 | MSBuild 架構。 注意：如果組建命令列呼叫 **dotnet.exe 組建**，請選擇 [ **Via .net Core** ] 選項。
| msBuildCommandline | 字串 | userProvideBuildInfo = = msBuildInfo | True |  |  | 用於編譯方案或專案的完整組建命令列。<br/><br/>附注：命令列的開頭應該是 **MSBuild.exe** 或 **dotnet.exe**的完整路徑。<br/>此命令會以 $ (SourcesDirectory) 作為工作目錄來執行。
| rulesetName | 挑選清單 | always | False | 建議 | 自訂，無，建議，必要 | 要使用的命名規則集。<br/><br/>如果 `Ruleset Configured In Your Visual Studio Project File(s)` 選擇了，將會使用您在 VS 專案檔 (s 中預先設定的規則集) 。 如果 `Custom` 選擇了，則可以設定自訂規則集路徑選項。
| rulesetVersion | 挑選清單 | rulesetName = = Required 或 rulesetName = = 建議 | False | Latest | 8.0、8.1、8.2、最新、LatestPreRelease | 選擇的 SDL 規則集版本。
| customRuleset | 字串 | rulesetName = 自訂 | False |  |  | 要使用之規則集的可存取路徑。 相對路徑會正規化為來源存放庫的根目錄， (`$(Build.SourcesDirectory)`) 。<br/><br/>如果規則集將 `Rules` `Actions` 設定為 `Error` ，則組建工作將會失敗。 若要使用執行此作業的規則集，請簽 `Continue on error` 入組建工作 `Control Options` 。
| microsoftAnalyzersVersion | 挑選清單 | always | False | Latest | 2.9.3、2.9.4、2.9.6、最新、LatestPreRelease | 要執行的 [CodeAnalysis 將 microsoft.codeanalysis.fxcopanalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) 套件版本。
| suppressionFileForCompilerWarnings | filePath | always | False |  |  | 隱藏 c # 與 VB 編譯器警告的隱藏隱藏檔案。<br/><br/>純文字檔案，其中每個警告識別碼都會以個別行列出。<br/>針對編譯器警告，請只指定警告識別碼的數值部分。 例如，1018會隱藏 CS1018，而 CA1501 會隱藏 CA1501。<br/><br/>相對檔案路徑會附加至來源儲存機制的根目錄， (`$(Build.SourcesDirectory)`) 。

## <a name="tslint-task"></a>TSLint 工作

| **InputType**      | **型別**     | **適用**            | **必要** | **預設值**             | **挑選清單 (的選項) **                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RuleLibrary | 挑選清單 | always | True | tslint | 自訂、microsoft、tslint | 所有結果都包含 TSLint 所選版本所隨附的規則 (**僅限基底**) 。<br/><br/>**僅限基底-** 只有 TSLint 隨附的規則。<br/><br/>**包含 Microsoft 規則-** 下載 [tslint-microsoft contrib](https://github.com/Microsoft/tslint-microsoft-contrib) ，並包含可在 tslint 執行中使用的規則。 選擇此選項會隱藏此 `Type Checking` 核取方塊，因為 Microsoft 的規則需要此核取方塊，而且將會自動使用。 它也會 unhides `Microsoft Contribution Version` 欄位，允許選取 npm 的版本 `tslint-microsoft-contrib` 。 [npm](https://www.npmjs.com/package/tslint-microsoft-contrib)<br/><br/>**包含自訂規則-** Unhides `Rules Directory` 欄位，這會接受可在 TSLint 執行中使用之 TSLint 規則目錄的可存取路徑。<br/><br/>**注意：** 預設值已變更為 tslint，因為許多使用者在設定 Microsoft 規則集時遇到問題。 如需特定版本設定，請參閱 [GitHub 上的 tslint-microsoft-contrib](https://github.com/microsoft/tslint-microsoft-contrib)。
| RulesDirectory | 字串 | RuleLibrary = = custom | True |  |  | 可存取的目錄，其中包含可在 TSLint 執行中使用的其他 TSLint 規則。
| 集 | 挑選清單 | RuleLibrary！ = microsoft | True | tsrecommended | custom、tslatest、tsrecommended | 定義要針對 TypeScript 檔案執行的規則。<br/><br/>** [tslint：最新](https://github.com/palantir/tslint/blob/master/src/configs/latest.ts)  - **擴充 `tslint:recommended` 並持續更新，以包含每個 TSLint 版本中最新規則的設定。 使用此設定可能會在次要版本之間引進重大變更，因為啟用了新的規則，這會導致程式碼中的失敗。 當 TSLint 到達主要版本時， `tslint:recommended` 將會更新為與相同 `tslint:latest` 。<br/><br/>** [tslint：建議使用](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts)  - **一組穩定、稍微固定的規則，TSLint 鼓勵一般 TypeScript 程式設計。 這種設定 `semver` 會遵循，因此 *不* 會在次要或修補程式版本之間有重大變更。
| RulesetMicrosoft | 挑選清單 | RuleLibrary = = microsoft | True | mssdlrequired | custom、msrecommended、mssdlrecommended、mssdlrequired、tslatest、tsrecommended | 定義要針對 TypeScript 檔案執行的規則。<br/><br/>** [microsoft： sdl-必要](https://github.com/Microsoft/tslint-microsoft-contrib/wiki/TSLint-and-the-Microsoft-Security-Development-Lifecycle)  - **執行 tslint 所提供的所有可用檢查，以及滿足 (SDL) 原則的*必要*[安全性開發生命週期](https://www.microsoft.com/sdl/)的 tslint-contrib 規則。<br/><br/>** [microsoft： sdl-建議](https://github.com/Microsoft/tslint-microsoft-contrib/wiki/TSLint-and-the-Microsoft-Security-Development-Lifecycle)  - **執行 tslint 所提供的所有可用檢查，以及滿足 (SDL) 原則的*必要和建議*[安全性開發生命週期](https://www.microsoft.com/sdl/)的 tslint contrib 規則。<br/><br/>**microsoft：建議使用** Tslint-microsoft contrib 規則的建立者所建議的所有檢查。 這包括安全性和非安全性檢查。<br/><br/>** [tslint：最新](https://github.com/palantir/tslint/blob/master/src/configs/latest.ts)  - **擴充 `tslint:recommended` 並持續更新，以包含每個 TSLint 版本中最新規則的設定。 使用此設定可能會在次要版本之間引進重大變更，因為啟用了新的規則，這會導致程式碼中的失敗。 當 TSLint 到達主要版本時， `tslint:recommended` 將會更新為與相同 `tslint:latest` 。<br/><br/>** [tslint：建議使用](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts)  - **一組穩定、稍微固定的規則，TSLint 鼓勵一般 TypeScript 程式設計。 這種設定 `semver` 會遵循，因此 *不* 會在次要或修補程式版本之間有重大變更。
| RulesetFile | 字串 | 規則集 = = custom 或 RulesetMicrosoft = = custom | True |  |  | 指定要執行之規則的 [設定檔](https://palantir.github.io/tslint/usage/cli/) 。<br/><br/>設定的路徑將會新增為 [自訂規則](https://palantir.github.io/tslint/develop/custom-rules/)的路徑。
| FileSelectionType | 挑選清單 | always | True | fileGlob | fileGlob、專案檔 | 
| 檔案儲存體 | 字串 | FileSelectionType = = fileGlob | True | **\*. ts |  | 檔案 [glob](https://www.npmjs.com/package/glob) ，可判斷) 要處理 (的檔案。 路徑是相對於 `Build.SourcesDirectory` 值。<br/><br/>Microsoft 的投稿文件庫需要使用專案檔。 如果您使用 Microsoft 的投稿文件庫搭配 `File Glob Pattern` 選項，系統就會為您產生專案檔。
| ECMAScriptVersion | 挑選清單 | FileSelectionType = = fileGlob && RuleLibrary = = microsoft | True | ES3 | ES2015、ES2016 功能、ES2017、ES3、ES5、ES6、ESNext | 使用 TypeScript 編譯器設定的 ECMAScript 目標版本。 當使用專案檔時，這是您的 TypeScript tsconfig.json file 的 [compilerOptions] 目標欄位。
| 專案 | 字串 | FileSelectionType = = 專案檔 | True |  |  | [tsconfig.js](http://www.typescriptlang.org/docs/handbook/tsconfig-json.html)檔案的路徑，該檔案會指定要在其上執行 TSLint 的 TypeScript 檔案。 路徑是相對於 `Build.SourcesDirectory` 值。
| TypeCheck | boolean | RuleLibrary！ = microsoft && FileSelectionType = = 專案檔 | False | true |  | 執行 linting 規則時啟用類型檢查程式。
| ExcludeFiles | 字串 | always | False |  |  | [Glob](https://www.npmjs.com/package/glob) ，表示要從 linting 排除的檔案。 路徑是相對於 `Build.SourcesDirectory` 值。 您可以指定多個值，並以分號分隔。
| OutputFormat | 挑選清單 | always | True | json | checkstyle、codeFrame、filesList、json、msbuild、pmd、prose、時尚、verbose、vso | 用來產生輸出的 [格式](https://palantir.github.io/tslint/formatters/) 器。 請注意，JSON 格式與 Post 分析相容。
| NodeMemory | 字串 | always | False |  |  | 要配置給節點以執行 TSLint 的明確記憶體數量（以 Mb 為單位）。 範例：8000<br/><br/>對應至 `--max_old_space=<value>` node 的 CLI 選項，也就是 `v8 option` 。
| ToolVersion | 挑選清單 | RuleLibrary！ = microsoft | True | 最新 | 4.0.0、4.0.1、4.0.2、4.1.0、4.1.1、4.2.0、4.3.0、4.3.1、4.4.0、4.4.1、4.4.2、4.5.0、4.5.1、5.0.0、5.1.0、5.2.0、5.3.0、5.3.2、5.4.0、5.4.1 版、5.4.2、5.4.3、5.5.0、最新 | 要下載並執行的 TSLint [版本](https://github.com/palantir/tslint/releases) 。
| TypeScriptVersion | 挑選清單 | always | True | 最新 | 0.8.0 版、0.8.1 版、0.8.2、0.8.3、0.9.0、0.9.1、0.9.5、0.9.7 版、1.0.0、1.0.1、1.3.0、1.4.1、1.5.3、1.6.2、1.7.3、1.7.5、1.8.0、1.8.10、1.8.2、1.8.5 版、1.8.6、1.8.7、1.8.9、1.9.0、2.0.0、2.0.10 版、2.0.2、2.0.3、2.0.6、2.0.7、2.0.8、2.0.9、2.1.1、2.1.4、2.1.5、2.1.6、2.2.0、2.2.1、custom、最新 | 要下載並使用的 [typescript](https://www.npmjs.com/package/typescript) 版本。<br/>**注意：** 這必須與用來編譯器代碼的 TypeScript 版本相同。
| TypeScriptVersionCustom | 字串 | TypeScriptVersion = = custom | True | 最新 |  | 要下載並使用的 [typescript](https://www.npmjs.com/package/typescript) 版本。<br/>**注意：** 這必須與用來編譯器代碼的 TypeScript 版本相同。
| MicrosoftContribVersion | 挑選清單 | RuleLibrary = = microsoft |  | 最新 | 4.0.0、4.0.1、5.0.0、5.0.1、最新 | Tslint 的版本 [-microsoft contrib](https://www.npmjs.com/package/tslint-microsoft-contrib) (SDL 規則) 下載並使用。</br>**注意：** 將會選擇與針對 tslint 所選擇的版本相容的 [tslint](https://www.npmjs.com/package/tslint) 版本。 Tslint 的更新-microsoft contrib 將會受到此組建工作的控點，直到可能發生測試期間為止。

## <a name="publish-security-analysis-logs-task"></a>發佈安全性分析記錄工作

| **InputType**      | **型別**     | **適用**            | **必要** | **預設值**             | **挑選清單 (的選項) **                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ArtifactName | 字串 | always | True | CodeAnalysisLogs |  | 要建立的成品名稱。
| ArtifactType | 挑選清單 | always | True | 容器 | 容器、FilePath | 要建立之成品的型別。
| TargetPath | 字串 | ArtifactType = FilePath | False | \\my\share \$ (Build. DefinitionName) <br>\$ (Build. BuildNumber)  |  | 要將檔案複製到其中的檔案共用
| AllTools | boolean | always | True | true |  | 發佈所有安全開發工具組建工作所產生的結果。
| 反惡意程式碼 | boolean | AllTools = false | True | true |  | 發佈反惡意程式碼組建工作產生的結果。
| Binskim 等 | boolean | AllTools = false | True | true |  | 發行 Binskim 等組建工作產生的結果。
| CredScan | boolean | AllTools = false | True | true |  | 發佈認證掃描器組建工作產生的結果。
| RoslynAnalyzers | boolean | AllTools = false | True | false |  | 發佈 Roslyn 分析器組建工作所產生的結果。
| TSLint | boolean | AllTools = false | True | true |  | 發行 TSLint 組建工作產生的結果。 請注意，報表僅支援 JSON 格式的 TSLint 記錄。 如果您選擇不同的格式，請據以更新您的 TSLint build 工作。
| ToolLogsNotFoundAction | 挑選清單 | always | True | 標準 | 錯誤、無、標準、警告 | 如果找不到選取的工具 (或任何工具) 找不到所有工具時所要採取的動作，即表示工具並未執行。<br/><br/>**選項：**<br/>**無：** 只有將 VSTS 變數 **system** 設定為 **true**，才會將訊息寫入至詳細資訊輸出資料流程。<br/>**標準：** (預設) 寫入標準輸出訊息，找不到工具的任何記錄。<br/>**警告：** 寫入黃色警告訊息，指出工具中找不到任何記錄，這會顯示在 [組建摘要] 頁面上顯示為警告。<br/>**錯誤：** 寫入紅色的錯誤訊息，並擲回例外狀況，並中斷組建。 您可以使用此選項來確保個別的工具選項，以確保執行的工具。

## <a name="security-report-task"></a>安全性報告工作

| **InputType**      | **型別**     | **適用**            | **必要** | **預設值**             | **挑選清單 (的選項) **                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VstsConsole | boolean | always | False | true |  | 將結果寫入至管線主控台。
| TsvFile | boolean | always | False | true |  | 產生 tsv 檔 (tab 分隔值) 每個找到的結果各有一行，而索引標籤分隔結果的資訊。
| HtmlFile | boolean | always | False | true |  | 產生 html 報表檔。
| AllTools | boolean | always | True | false |  | 所有安全開發工具組建工作產生的報表結果。
| Binskim 等 | boolean | AllTools = false | True | false |  | Binskim 等組建工作產生的報表結果。
| BinSkimBreakOn | 挑選清單 | AllTools = true 或 Binskim 等 = true | True | 錯誤 | 錯誤，WarningAbove | 要報告的結果層級。
| CredScan | boolean | AllTools = false | True | false |  | 認證掃描器組建工作產生的報告結果。
| RoslynAnalyzers | boolean | AllTools = false | True | false |  | Roslyn 分析器組建工作產生的報表結果。
| RoslynAnalyzersBreakOn | 挑選清單 | AllTools = true 或 RoslynAnalyzers = true | True | 錯誤 | 錯誤，WarningAbove | 要報告的結果層級。
| TSLint | boolean | AllTools = false | True | false |  | TSLint 組建工作產生的報表結果。 請注意，報表僅支援 JSON 格式的 TSLint 記錄。 如果您選擇不同的格式，請據以更新您的 TSLint build 工作。
| TSLintBreakOn | 挑選清單 | AllTools = true 或 TSLint = true | True | 錯誤 | 錯誤，WarningAbove | 要報告的結果層級。
| ToolLogsNotFoundAction | 挑選清單 | always | True | 標準 | 錯誤、無、標準、警告 | 如果找不到選取的工具 (或任何工具) 找不到所有工具時所要採取的動作，即表示工具並未執行。<br/><br/>**選項：**<br/>**無：** 只有將 VSTS 變數 **system** 設定為 **true**，才會將訊息寫入至詳細資訊輸出資料流程。<br/>**標準：** (預設) 寫入標準輸出訊息，找不到工具的任何記錄。<br/>**警告：** 寫入黃色警告訊息，指出工具中找不到任何記錄，這會顯示在 [組建摘要] 頁面上顯示為警告。<br/>**錯誤：** 寫入紅色的錯誤訊息，並擲回例外狀況，並中斷組建。 您可以使用此選項來確保個別的工具選項，以確保執行的工具。
| CustomLogsFolder | 字串 | always | False |  |  | 分析工具記錄所在的基底資料夾;個別記錄檔將會位於此路徑下每個工具所命名的子資料夾中。

## <a name="post-analysis-task"></a>分析後工作

| **InputType**      | **型別**     | **適用**            | **必要** | **預設值**             | **挑選清單 (的選項) **                                   | **描述**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AllTools | boolean | always | True | false |  | 如果任何 Microsoft 安全性程式碼分析組建工作發現任何問題，請中斷組建。
| Binskim 等 | boolean | AllTools = false | True | false |  | 如果找到任何 Binskim 等問題，請根據您選取的 [中斷] 選項來中斷組建。
| BinSkimBreakOn | 挑選清單 | AllTools = true 或 Binskim 等 = true | True | 錯誤 | 錯誤，WarningAbove | 中斷組建的問題層級。
| CredScan | boolean | AllTools = false | True | false |  | 如果找到任何認證掃描器問題，請中斷組建。
| RoslynAnalyzers | boolean | AllTools = false | True | false |  | 如果發現任何 Roslyn 分析器問題，請中斷組建。
| RoslynAnalyzersBreakOn | 挑選清單 | AllTools = true 或 RoslynAnalyzers = true | True | 錯誤 | 錯誤，WarningAbove | 中斷組建的問題層級。
| TSLint | boolean | AllTools = false | True | false |  | 如果發現任何 TSLint 問題，請中斷組建。 請注意，post 分析只支援 JSON 格式的 TSLint 記錄。 如果您選擇不同的格式，請據以更新您的 TSLint build 工作。
| TSLintBreakOn | 挑選清單 | AllTools = true 或 TSLint = true | True | 錯誤 | 錯誤，WarningAbove | 中斷組建的問題層級。
| VstsConsole | boolean | always | False | true |  | 將結果寫入至管線主控台。
| ToolLogsNotFoundAction | 挑選清單 | always | True | 標準 | 錯誤、無、標準、警告 | 如果找不到選取的工具 (或任何工具) 找不到所有工具時所要採取的動作，即表示工具並未執行。<br/><br/>**選項：**<br/>**無：** 只有將 VSTS 變數 **system** 設定為 **true**，才會將訊息寫入至詳細資訊輸出資料流程。<br/>**標準：** (預設) 寫入標準輸出訊息，找不到工具的任何記錄。<br/>**警告：** 寫入黃色警告訊息，指出工具中找不到任何記錄，這會顯示在 [組建摘要] 頁面上顯示為警告。<br/>**錯誤：** 寫入紅色的錯誤訊息，並擲回例外狀況，並中斷組建。 您可以使用此選項來確保個別的工具選項，以確保執行的工具。

## <a name="next-steps"></a>後續步驟

如果您對安全性程式碼分析延伸模組和所提供的工具有進一步的疑問，請參閱 [我們的常見問題頁面](security-code-analysis-faq.md)。