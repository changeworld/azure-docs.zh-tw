---
title: Microsoft 安全性程式碼分析檔常見問題
description: 如需瞭解 Microsoft 安全性程式碼分析延伸模組，請查看常見問題（Faq）。
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
ms.openlocfilehash: f872159e538dc85121a7a6d4d6503fd18a263628
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543039"
---
# <a name="frequently-asked-questions"></a>常見問題集
有任何問題嗎？ 如需詳細資訊，請參閱下列常見問題。

## <a name="general-faq"></a>一般常見問題

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>我可以在 Visual Studio Team Foundation Server 實例上（而不是在 Azure DevOps 實例上安裝此擴充功能）嗎？

否。 延伸模組無法供下載及安裝 Visual Studio Team Foundation Server。

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>我是否必須使用我的組建來執行 Microsoft 安全性程式碼分析？ 

可能。 這取決於分析工具的類型。 原始程式碼可能是唯一需要的東西，或可能需要組建輸出。

例如，認證掃描器（CredScan）會分析程式碼存放庫的資料夾結構內的檔案。 由於這項分析，您可以在獨立組建中執行 CredScan 併發布安全性分析記錄組建工作，以取得結果。

對於分析後置組建成品的其他工具（例如 BinSkim），必須先建立組建。

### <a name="can-i-break-my-build-when-results-are-found"></a>可以在找到結果時中斷我的組建嗎？

是。 當任何工具在記錄檔中報告問題或問題時，您可以引進組建中斷。 只要新增 [後期分析] 組建工作，然後選取您想要中斷組建的任何工具的核取方塊即可。

在分析後工作的 UI 中，您可以選擇在任何工具僅報告錯誤或同時回報錯誤和警告時，中斷組建。

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Azure DevOps 中的命令列引數與獨立桌面工具中的引數有何不同？ 

在大部分的情況下，Azure DevOps 組建工作都是安全性工具的命令列引數的直接包裝函式。 您可以將做為引數傳遞至組建工作，以做為您通常傳遞給命令列工具的任何專案。

明顯的差異：

- 工具會從代理程式 $ （SourcesDirectory）的源資料夾，或從% BUILD_SOURCESDIRECTORY% 執行。 例如，C:\agent \_ work\1\s。
- 引數中的路徑可以相對於先前列出之來原始目錄的根目錄。 路徑也可以是絕對的。 您可以使用 Azure DevOps 組建變數或執行具有本機資源已知部署位置的內部部署代理程式，來取得絕對路徑。
- 工具會自動提供輸出檔案路徑或資料夾。 如果您提供組建工作的輸出位置，該位置會被取代為組建代理程式上已知記錄檔位置的路徑
- 某些工具的一些其他命令列引數已變更。 其中一個範例是新增或移除選項，以確保不會啟動 GUI。

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>我可以在 Azure DevOps 組建中跨多個存放庫執行認證掃描器之類的組建工作嗎？

否。 不支援在單一管線中跨多個存放庫執行安全的開發工具。

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>未建立我指定的輸出檔，或找不到我指定的輸出檔

[組建工作] 會篩選某些使用者輸入。 特別針對這個問題，他們會將產生之輸出檔案的位置更新為組建代理程式上的通用位置。 如需此位置的詳細資訊，請參閱下列問題。

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>這些工具所產生的輸出檔會儲存在哪裡？ 

組建工作會自動將輸出路徑加入至組建代理程式上的這個已知位置： $ （BuildDirectory） \_ sdt\logs。 因為我們會在這個位置上標準化，所以產生或取用程式碼分析記錄的所有小組都有輸出的存取權。

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>我可以將組建排入佇列，以便在裝載的組建代理程式上執行這些工作嗎？ 

是。 擴充功能中的所有工作和工具都可以在託管的組建代理程式上執行。

>[!NOTE]
> 反惡意程式碼掃描器組建工作需要有已啟用 Windows Defender 的組建代理程式。 託管 Visual Studio 2017 和更新版本會提供這類代理程式。 組建工作不會在 Visual Studio 2015 主控的代理程式上執行。
>
> 雖然無法更新這些代理程式上的簽章，但簽章應一律小於三小時的舊版本。

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>我可以將這些組建工作當做發行管線的一部分來執行，而不是組建管線嗎？

在大部分情況下為 [是]。

不過，當這些工作發行成品時，Azure DevOps 不支援在發行管線內執行工作。 這項支援可防止發行的安全性分析記錄工作順利地在發行管線中執行。 此工作會失敗，並顯示描述性錯誤訊息。

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>組建工作從何處下載工具？

組建工作可以從[Azure DevOps 套件管理](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json)摘要下載工具的 NuGet 套件。 組建工作也可以使用節點套件管理員，其必須預先安裝在組建代理程式上。 這類安裝的範例是**npm install tslint**命令。

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>在我的 Azure DevOps 組織上安裝延伸模組有何影響？ 

在安裝時，延伸模組所提供的安全性組建工作會提供給您組織中的所有使用者使用。 當您建立或編輯 Azure 管線時，可以從 [組建-工作集合] 清單中取得這些作業。 否則，在您的 Azure DevOps 組織中安裝擴充功能不會有任何作用。 安裝不會修改任何帳戶設定、專案設定或管線。

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>安裝延伸模組會修改我現有的 Azure Pipelines 嗎？ 

否。 安裝此延伸模組可讓您的管線加入安全性組建工作。 您仍然需要新增或更新組建定義，如此一來，工具就可以與您的組建流程搭配使用。

## <a name="task-specific-faq"></a>工作特定的常見問題

本節會列出組建工作特有的問題。

### <a name="credential-scanner"></a>認證掃描器

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>什麼是常見的隱藏專案案例和範例？

以下是兩個最常見隱藏專案案例的詳細資料。

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>抑制指定路徑內所有出現的特定密碼

如下列範例所示，CredScan 輸出檔中的密碼雜湊索引鍵是必要的。

```js
{
    "tool": "Credential Scanner",
    "suppressions": [
    {
        "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
        "_justification": "Secret used by MSDN sample, it is fake."
    }
  ]
}
```

>[!WARNING]
> 雜湊索引鍵是由相符值或檔案內容的一部分所產生。 任何來來源程式代碼修訂都可以變更雜湊索引鍵，並停用隱藏式規則。

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>抑制指定檔案中的所有秘密，或隱藏秘密檔案本身

檔案運算式可以是檔案名。 它也可以是完整檔案路徑或檔案名的 basename 部分。 不支援萬用字元。

下列範例示範如何隱藏檔案 \<InputPath>\src\JS\lib\angular.js

有效隱藏項規則的範例：

- \<InputPath>\src\JS\lib\angular.js-抑制指定路徑中的檔案
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js-隱藏所有具有相同名稱的檔案

```js
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
```

>[!WARNING] 
> 新增至檔案的所有未來秘密也會自動隱藏起來。

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>管理密碼的建議方針為何？

下列資源可協助您從應用程式內安全地管理秘密和存取機密資訊：

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../azure-sql/database/authentication-aad-overview.md)
 - [Azure AD 受控服務識別（MSI）](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [適用於 Azure 資源的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure App Service 和 Azure Functions 中的受控識別](../../app-service/overview-managed-identity.md)
 - [AppAuthentication 程式庫](../../key-vault/general/service-to-service-authentication.md)


如需詳細資訊，請參閱在[雲端中安全地管理秘密](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)的 blog 文章。

#### <a name="can-i-write-my-own-custom-searchers"></a>我可以撰寫自己的自訂 searchers 嗎？

認證掃描器依賴一組通常在 buildsearchers.xml 檔案中定義的內容 searchers。 檔案包含代表**ContentSearcher**物件的 XML 序列化物件陣列。 此程式會與一組經過妥善測試的 searchers 一起散發。 但是您也可以執行自己的自訂 searchers。

內容搜尋會定義如下：

- **名稱**：要在認證掃描器輸出檔案中使用的描述性搜尋程式名稱。 我們建議您針對搜尋者名稱使用 camel 大小寫的命名慣例。
- **RuleId**：搜尋者的穩定不透明識別碼：
    - 認證掃描器的預設搜尋者會被指派一個**RuleId**值，例如 CSCAN0010、CSCAN0020 或 CSCAN0030。 最後一個數位保留給可能會透過正則運算式（RegEx）合併或分割搜尋者群組。
    - 自訂的搜尋者的**RuleId**值應該有自己的命名空間。 範例包括 CSCAN- \<Namespace\> mylnxstorage02、CSCAN \<Namespace\> 0020 和 CSCAN- \<Namespace\> 0030。
    - 完整的搜尋者名稱是**RuleId**值和搜索者名稱的組合。 範例包括 CSCAN0010。KeyStoreFiles 和 CSCAN0020。Base64EncodedCertificate.
- **ResourceMatchPattern**：用來檢查搜尋者的檔案副檔名 Regex。
- **ContentSearchPatterns**：包含要比對之 RegEx 語句的字串陣列。 如果未定義任何搜尋模式，則會傳回符合**ResourceMatchPattern**值的所有檔案。
- **ContentSearchFilters**：包含 RegEx 語句的字串陣列，用來篩選搜尋特定的錯誤陽性。
- **MatchDetails**：要針對每個搜尋的相符新增的描述性訊息、緩和指示或兩者。
- **建議**事項：使用 PREfast 報表格式之相符項的建議欄位內容。
- **嚴重性**：反映問題嚴重性層級的整數。 最高嚴重性層級的值為1。

  ![顯示認證掃描器設定的 XML](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn 分析器

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>使用 Roslyn 分析器工作時有哪些常見的錯誤？

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>專案已使用錯誤的 NETCore 版本進行還原

完整的錯誤訊息：

「錯誤：此專案已使用 NETCore 版本*x.x*進行還原，但使用目前的設定，將改用版本 y. y *. y。* 若要解決此問題，請確定會針對還原和後續作業 (例如建置或發佈) 使用相同的設定。 一般來說，如果在建置或發佈期間 (而不是在還原期間) 設定了 RuntimeIdentifier 屬性，就會發生此問題。」

由於 Roslyn 分析器工作會在編譯過程中執行，因此組建電腦上的來源樹狀結構必須處於可建置狀態。

主要組建和 Roslyn 分析器步驟之間的步驟可能會使來源樹狀結構進入防止建立的狀態。 這個額外的步驟可能**dotnet.exe 發佈**。 請嘗試複製在 Roslyn 分析器步驟之前進行 NuGet 還原的步驟。 這個重複的步驟可能會使來源樹狀結構回到可建置狀態。

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe 無法建立分析器實例

完整的錯誤訊息：

"' csc.exe ' 已結束，錯誤碼為 1--無法從 C： Mm.nn.bbbb.rr 建立分析器*AAAA*的實例 \\ * *：無法載入檔案或元件 ' CodeAnalysis，Version =*x*. x，Culture = 中性，PublicKeyToken = 31bf3856ad364e35 ' 或其相依性的其中之一。 系統找不到指定的檔案。」

請確定您的編譯器支援 Roslyn 分析器。 執行命令**csc.exe/version**應該會報告2.6 或更新版本的版本值。

有時候 .csproj 檔案可以藉由參考 Microsoft.Net 中的套件，來覆寫組建電腦的 Visual Studio 安裝。 如果您不想要使用特定版本的編譯器，請移除 Microsoft.Net 的參考。 否則，請確定所參考封裝的版本也是2.6 或更新版本。

嘗試取得錯誤記錄檔路徑，這是在 [ **csc.exe/errorlog** ] 選項中指定的。 選項和路徑會出現在 Roslyn 分析器組建工作的記錄檔中。 它們看起來可能像是 **/errorlog： f:\ts-services-123 \_ work\456\s\Some\Project\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>C # 編譯器版本不夠新

若要取得最新版本的 c # 編譯器，請移至[Microsoft.Net](https://www.nuget.org/packages/Microsoft.Net.Compilers)。 若要取得已安裝的版本，請在命令提示字元中執行**csc.exe/version** 。 請確定您參考的是2.6 或更新版本的 Microsoft.Net NuGet 套件。

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>找不到 MSBuild 和 VSBuild 記錄

「Roslyn 分析器組建」工作需要從 MSBuild 組建工作查詢 MSBuild 記錄 Azure DevOps。 如果在 MSBuild 工作之後立即執行分析器工作，則記錄檔還無法使用。 將其他工作放在 MSBuild 工作與 Roslyn 分析器工作之間。 其他工作的範例包括 BinSkim 和反惡意程式碼掃描器。

## <a name="next-steps"></a>後續步驟

如果您需要額外的協助，Microsoft 安全性程式碼分析支援將于太平洋標準時間上午 9:00 AM 到 5:00 PM 的星期一到星期五提供。

- 上線：請參閱上[架檔](security-code-analysis-onboard.md)
  
- 支援：以電子郵件寄給我們的小組， [Microsoft 安全性程式碼分析支援](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)