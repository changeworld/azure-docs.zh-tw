---
title: 微軟安全代碼分析文檔常見問題解答
description: 本文包含有關 Microsoft 安全代碼分析擴展的常見問題解答
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
ms.openlocfilehash: de76467cc741a65a851e5f3a7ec424d0326aebb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851514"
---
# <a name="frequently-asked-questions"></a>常見問題集
有問題嗎？ 有關詳細資訊，請查看以下常見問題解答。

## <a name="general-faq"></a>一般常見問題解答

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>是否可以在視覺化工作室團隊基礎伺服器實例上安裝擴展，而不是在 Azure DevOps 實例上安裝該擴展？

否。 該擴展不適用於 Visual Studio 團隊基礎伺服器的下載和安裝。

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>我是否要使用我的生成運行 Microsoft 安全代碼分析？ 

可能。 這取決於分析工具的類型。 原始程式碼可能是唯一需要的東西，或者可能需要生成輸出。

例如，憑據掃描程式 （CredScan） 分析代碼存儲庫資料夾結構中的檔。 由於此分析，您可以在獨立生成中運行 CredScan 和發佈安全分析日誌生成任務以獲得結果。

對於分析生成後工件的其他工具（如 BinSkim），首先需要生成。

### <a name="can-i-break-my-build-when-results-are-found"></a>找到結果時，是否可以中斷我的生成？

是。 當任何工具報告其日誌檔中的問題時，都可以引入生成中斷。 只需添加"分析後生成"任務，然後為要中斷生成的任何工具選擇核取方塊。

在"分析後"任務的 UI 中，您可以選擇在任何工具僅報告錯誤或同時報告錯誤和警告時中斷生成。

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Azure DevOps 中的命令列參數與獨立桌面工具中的這些參數有何不同？ 

在大多數情況下，Azure DevOps 生成任務是圍繞安全工具的命令列參數的直接包裝。 您可以將通常傳遞給命令列工具的任何內容作為參數傳遞給生成任務。

明顯差異：

- 工具從代理 $（Build.SourceDirectory） 的源資料夾運行，或從 %BUILD_SOURCESDIRECTORY%。 例如 C：\代理\_工作\1\s。
- 參數中的路徑可以相對於以前列出的原始目錄的根目錄。 路徑也可以是絕對的。 通過使用 Azure DevOps 生成變數或運行具有本地資源的已知部署位置的本地代理，可以獲取絕對路徑。
- 工具自動提供輸出檔案路徑或資料夾。 如果為生成任務提供輸出位置，則該位置將替換為到生成代理上已知日誌位置的路徑
- 某些工具將更改一些其他命令列參數。 一個例子是添加或刪除確保不啟動 GUI 的選項。

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>是否可以在 Azure DevOps 生成中的多個存儲庫中運行像憑據掃描器這樣的生成任務？

否。 不支援在單個管道中跨多個存儲庫運行安全開發工具。

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>未創建我指定的輸出檔案，或者找不到我指定的輸出檔案

生成任務篩選一些使用者輸入。 具體對於此問題，它們會將生成的輸出檔案的位置更新為生成代理上的常見位置。 有關此位置的詳細資訊，請參閱以下問題。

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>工具生成的輸出檔案保存在何處？ 

生成任務會自動將輸出路徑添加到生成代理上的此已知位置：$（代理.BuildDirectory）\_sdt_logs。 由於我們在此位置進行標準化，因此生成或使用代碼分析日誌的所有團隊都有權訪問輸出。

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>是否可以對生成進行排隊以在託管生成代理上運行這些任務？ 

是。 擴展中的所有任務和工具都可以在託管生成代理上執行。

>[!NOTE]
> 反惡意軟體掃描器生成任務需要啟用 Windows Defender 的生成代理。 託管視覺工作室 2017 和更高版本提供這樣的代理。 生成任務不會在 Visual Studio 2015 託管代理上運行。
>
> 儘管無法在這些代理上更新簽名，但簽名應始終小於三個小時。

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>我可以作為發佈管道的一部分而不是生成管道運行這些生成任務嗎？

在大多數情況下，是的。

但是，Azure DevOps 不支援在發佈管道中運行的任務，因為這些任務發佈專案。 這種缺乏支援可防止發佈安全分析日誌任務在發佈管道中成功運行。 任務會失敗，並出現描述性錯誤訊息。

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>生成任務從何處下載工具？

生成任務可以從[Azure DevOps 包管理源](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json)下載工具的 NuGet 包。 生成任務也可以使用節點包管理器，必須在生成代理上預先安裝節點包管理器。 這種安裝的一個示例是命令**npm 安裝 tslint**。

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>安裝擴展對 Azure DevOps 組織有何影響？ 

安裝後，擴展提供的安全生成任務將可供組織中的所有使用者使用。 創建或編輯 Azure 管道時，這些任務可從生成任務集合清單中獲取。 否則，在 Azure DevOps 組織中安裝擴展不起作用。 安裝不會修改任何帳戶設置、專案設置或管道。

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>安裝擴展是否修改了我現有的 Azure 管道？ 

否。 安裝擴展使安全生成任務可用於添加到管道中。 您仍然需要添加或更新組建定義，以便工具可以使用生成過程。

## <a name="task-specific-faq"></a>特定于任務的常見問題解答

本節列出了特定于生成任務的問題。

### <a name="credential-scanner"></a>憑據掃描程式

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>什麼是常見的抑制方案和示例？

下面是兩個最常見的抑制方案的詳細資訊。

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>禁止指定路徑中給定機密的所有匹配項

CredScan 輸出檔案中的機密雜湊金鑰是必需的，如下例所示。

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> 雜湊鍵由匹配值或檔內容的一部分生成。 任何原始程式碼修訂都可以更改雜湊鍵並禁用抑制規則。

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>禁止顯示指定檔中的所有機密或禁止機密檔本身

檔運算式可以是檔案名。 它也可以是完整檔路徑或檔案名的基名部分。 不支援萬用字元。

以下示例演示如何禁止>_src_JS_lib_angular.js 的檔\<輸入路徑

有效抑制規則的示例：

- \<輸入路徑>_src_JS_lib_angular.js - 在指定路徑中禁止顯示檔
- \src_JS_lib_angular.js
- [JS_lib_angular.js
- [lib_angular.js
- angular.js - 禁止任何同名檔

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> 添加到檔的所有將來機密也將自動被抑制。

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>建議哪些準則來管理機密？

以下資源可説明您安全地管理機密並從應用程式內訪問敏感資訊：

 - [Azure 金鑰保存庫](../../key-vault/index.yml)
 - [Azure 活動目錄（Azure AD）](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD 託管服務標識 （MSI）](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [適用於 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure 應用服務和 Azure 函數中的託管標識](../../app-service/overview-managed-identity.md)
 - [應用身份驗證庫](../../key-vault/service-to-service-authentication.md)


有關詳細資訊，請參閱在[雲中安全地管理機密的](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)博客文章。

#### <a name="can-i-write-my-own-custom-searchers"></a>我可以編寫自己的自訂搜索器嗎？

憑據掃描程式依賴于一組內容搜索程式，這些搜索程式通常在生成搜索器.xml 檔中定義。 該檔包含表示**內容搜索器**物件的 XML 序列化物件陣列。 該程式由一組經過良好測試的搜索人員分發。 但是，您也可以實現自己的自訂搜索。

內容搜索器的定義如下：

- **名稱**： 要在憑據掃描器輸出檔案中使用的描述性搜索者名稱。 我們建議您對搜索者名稱使用駱駝大小寫命名約定。
- **規則 Id**： 搜索器的穩定不透明 ID：
    - 憑據掃描程式預設搜索程式被分配為**規則 Id**值，如 CSCAN0010、CSCAN0020 或 CSCAN0030。 最後一個數位保留用於通過正則運算式 （RegEx） 可能合併或劃分搜索者組。
    - 自訂搜索器的**RuleId**值應有自己的命名空間。 示例包括\<CSCAN-\>命名空間 0010、CSCAN-\<命名空間\>0020\<和\>CSCAN- 命名空間 0030。
    - 完全限定的搜索者名稱是**RuleId**值和搜索者名稱的組合。 示例包括 CSCAN0010。金鑰存儲檔和 CSCAN0020。基64編碼證書。
- **資源匹配模式**：檔副檔名的 Regex，以對照搜索程式進行檢查。
- **內容搜索模式**：包含要匹配的正則運算式語句的字串陣列。 如果未定義搜索模式，則返回與**ResourceMatchPattern**值匹配的所有檔。
- **內容搜索篩選器**：包含正則運算式語句的字串陣列，用於篩選特定于搜索器的誤報。
- **匹配詳細資訊**：要為搜索器的每個匹配添加描述性消息、緩解說明或兩者。
- **建議**：使用 PREfast 報告格式的匹配的建議欄位內容。
- **嚴重性**：反映問題嚴重性級別的整數。 最高嚴重性級別具有值 1。

  ![顯示憑據掃描器設置的 XML](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn 分析器

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>使用羅斯林分析器任務時的常見錯誤是什麼？

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>該專案是使用錯誤的 Microsoft.NETCore.App 版本還原的

完整的錯誤訊息：

錯誤：該專案是使用 Microsoft.NETCore.App 版本*x.x.x*還原的，但使用當前設置時，將使用版本*y.y.y。* 若要解決此問題，請確定會針對還原和後續作業 (例如建置或發佈) 使用相同的設定。 一般來說，如果在建置或發佈期間 (而不是在還原期間) 設定了 RuntimeIdentifier 屬性，就會發生此問題。」

由於 Roslyn 分析器任務作為編譯的一部分運行，因此生成電腦上的源樹需要處於可構建狀態。

主生成和 Roslyn 分析器步驟之間的步驟可能使源樹處於阻止生成的狀態。 此額外步驟可能是**dotnet.exe 發佈**。 嘗試複製在 Roslyn 分析器步驟之前執行 NuGet 還原的步驟。 此重複步驟可能會使源樹重新處於可構建狀態。

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe 無法創建分析器實例

完整的錯誤訊息：

"'csc.exe'退出與錯誤代碼 1 - 分析器*AAAA*的實例不能從\\C：*BBBB*.dll ： 無法載入檔或程式集 'Microsoft.代碼分析， 版本=*X.X.X.X，* 文化=中性， PublicKeyToken_31bf3856ad364e35' 或其依賴項之一。 系統找不到指定的檔案。」

確保您的編譯器支援羅斯林分析器。 運行命令**csc.exe /version**應報告版本值 2.6 或更高版本。

有時 .csproj 檔可以通過引用 Microsoft.Net.編譯器的包來覆蓋生成電腦的 Visual Studio 安裝。 如果您不打算使用編譯器的特定版本，請刪除對 Microsoft.Net.編譯器的引用。 否則，請確保引用的包的版本也是 2.6 或更高版本。

嘗試獲取錯誤日誌路徑，該路徑在**csc.exe /錯誤日誌**選項中指定。 選項和路徑顯示在 Roslyn 分析器生成任務的日誌中。 它們可能類似于 **/錯誤日誌：F：\ts-服務-123\_工作\456\s\一些\專案\代碼.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>C# 編譯器版本不夠新

要獲取最新版本的 C# 編譯器，請訪問[Microsoft.Net.編譯器](https://www.nuget.org/packages/Microsoft.Net.Compilers)。 要獲取已安裝的版本，請以命令提示符運行**csc.exe /version。** 請確保引用 Microsoft.Net.編譯器 NuGet 版本 2.6 或更高版本的資料包。

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>找不到 MSBuild 和 VSBuild 日誌

Roslyn 分析器生成任務需要查詢 Azure DevOps 從 MSBuild 生成任務中查詢 MSBuild 日誌。 如果分析器任務在 MSBuild 任務之後立即運行，則日誌將不可用。 在 MSBuild 任務和羅斯林分析器任務之間放置其他任務。 其他任務的示例包括 BinSkim 和反惡意軟體掃描程式。

## <a name="next-steps"></a>後續步驟

如果您需要其他説明，Microsoft 安全代碼分析支援可于週一至週五上午 9：00 至下午 5：00 太平洋標準時間提供。

- 入職：請參閱我們的[入職文檔](security-code-analysis-onboard.md)
  
- 支援：在 Microsoft[安全代碼分析支援](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)中向我們的團隊發送電子郵件