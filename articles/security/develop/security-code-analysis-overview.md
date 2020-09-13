---
title: Microsoft 安全性程式碼分析檔總覽
description: 深入瞭解 Microsoft 安全性程式碼分析延伸模組。 使用此延伸模組，您可以將安全性程式碼分析新增至 Azure DevOps CI/識別碼管線。
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
ms.openlocfilehash: af4797d7d7d7cb866674747ea8871bebb059207d
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031240"
---
# <a name="about-microsoft-security-code-analysis"></a>關於 Microsoft 安全性程式碼分析

使用 Microsoft 安全性程式碼分析擴充功能，小組可以在 Azure DevOps 的持續整合和傳遞 (CI/CD) 管線中新增安全性程式碼分析。 這項分析建議是 Microsoft 的 [SDL) 專家 (的安全開發週期 ](https://www.microsoft.com/securityengineering/sdl/practices) 。

一致的 UX 藉由隱藏執行工具的複雜性來簡化安全性。 透過以 NuGet 為基礎的工具傳遞，小組不再需要管理工具的安裝或更新。 使用建立工作的命令列和基本介面，所有使用者都可以在所需的工具上擁有最多控制權。

小組也可以使用功能強大的後處理功能，例如：

- 發行記錄以進行保留。
- 產生可操作、以開發人員為焦點的報表。
- 設定迴歸測試的組建中斷。

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>為什麼我應該使用 Microsoft 安全性程式碼分析？

### <a name="security-simplified"></a>簡化安全性

將 Microsoft 安全性程式碼分析工具新增至您的 Azure DevOps 管線，就像新增工作一樣簡單。 自訂工作或使用其預設行為。 工作會做為 Azure DevOps 管線的一部分來執行，並產生詳細的記錄，以詳述許多種類的結果。

### <a name="clean-builds"></a>清除組建

解決工具所報告的初始問題之後，您可以設定延伸模組，以中斷新問題的組建。在每個提取要求上設定持續整合組建很簡單。

### <a name="set-it-and-forget-it"></a>設定並忘記密碼

依預設，組建工作和工具會保持最新狀態。 如果有更新版本的工具，您就不需要下載並安裝它。 延伸模組會為您處理更新。

### <a name="under-the-hood"></a>背後原理

延伸模組的組建工作會隱藏其複雜性：
  - 正在執行安全性靜態分析工具。
  - 處理記錄檔中的結果，以建立摘要報表或中斷組建。

## <a name="microsoft-security-code-analysis-tool-set"></a>Microsoft 安全性程式碼分析工具集

Microsoft 安全性程式碼分析延伸模組可讓您隨時使用最新版本的重要分析工具。 此延伸模組包括 Microsoft 管理的工具和開放原始碼工具。

當您使用對應的組建工作來設定和執行管線之後，這些工具會自動下載到雲端託管的代理程式。

此區段會列出延伸模組中目前可用的一組工具。 請留意其他工具的新增。 此外，請將您的建議傳送給我們，以取得您希望我們新增的工具。

### <a name="anti-malware-scanner"></a>反惡意程式碼掃描器

反惡意程式碼掃描器組建工作現在包含在 Microsoft 安全性程式碼分析延伸模組中。 這項工作必須在已安裝 Windows Defender 的組建代理程式上執行。 如需詳細資訊，請參閱 [Windows Defender 網站](https://aka.ms/defender)。

### <a name="binskim"></a>Binskim 等

Binskim 等是可移植的可執行檔 (PE) 輕量掃描器，可驗證編譯器設定、連結器設定，以及二進位檔案的其他安全性相關特性。 此組建工作會在 binskim.exe 主控台應用程式周圍提供命令列包裝函式。 Binskim 等是開放原始碼工具。 如需詳細資訊，請參閱 [GitHub 上的 binskim 等](https://github.com/Microsoft/binskim)。

### <a name="credential-scanner"></a>認證掃描器

儲存在原始程式碼中的密碼和其他秘密是很大的問題。 認證掃描器是專屬的靜態分析工具，可協助解決此問題。 此工具會偵測原始程式碼中的認證、秘密、憑證和其他機密內容，以及您的組建輸出。

### <a name="roslyn-analyzers"></a>Roslyn 分析器

Roslyn 分析器是 Microsoft 的編譯器整合式工具，可用於靜態分析 managed c # 和 Visual Basic 程式碼。 如需詳細資訊，請參閱以 [Roslyn 為基礎的分析器](https://docs.microsoft.com/dotnet/standard/analyzers/api-analyzer)。

### <a name="tslint"></a>TSLint

TSLint 是可延伸的靜態分析工具，可檢查 TypeScript 程式碼的功能是否有可讀性、可維護性和錯誤。 新式的編輯器和組建系統受到廣泛的支援。 您可以使用自己不起毛的規則、設定和格式器進行自訂。 TSLint 是開放原始碼工具。 如需詳細資訊，請參閱 [GitHub 上的 TSLint](https://github.com/palantir/tslint)。

## <a name="analysis-and-post-processing-of-results"></a>結果的分析和後置處理

Microsoft 安全性程式碼分析延伸模組也有三個作業中的工作。 這些工作可協助您分析安全性工具工作所找到的結果。 新增至管線時，這些工作通常會遵循所有其他工具工作。

### <a name="publish-security-analysis-logs"></a>發佈安全性分析記錄

[發行安全性分析記錄] 組建工作會保留在組建期間執行之安全性工具的記錄檔。 您可以讀取這些記錄以進行調查和後續追蹤。

您可以將記錄檔發行至 Azure Artifacts 的 .zip 檔案。 您也可以從私用組建代理程式將它們複製到可存取的檔案共用。

### <a name="security-report"></a>安全性報告

安全性報表組建工作會剖析記錄檔。 這些檔案是由組建期間執行的安全性工具所建立。 然後，組建工作會建立單一摘要報告檔。 此檔案會顯示分析工具所找到的所有問題。

您可以設定此工作來報告特定工具或所有工具的結果。 您也可以選擇要報告的問題層級，例如僅限錯誤或錯誤和警告。

### <a name="post-analysis-build-break"></a>分析後 (組建中斷) 

使用後置分析組建工作時，您可以插入會刻意造成組建失敗的組建中斷。 如果有一或多個分析工具報告程式碼中的問題，您可以插入組建中斷。

您可以設定這項工作，以針對特定工具或所有工具所找到的問題中斷組建。 您也可以根據所找到問題的嚴重性（例如錯誤或警告）進行設定。

>[!NOTE]
>根據設計，如果工作成功完成，每個組建工作都會成功。 無論工具是否找到問題，都是如此，如此一來，就可以藉由允許所有工具執行來完成組建。

## <a name="next-steps"></a>接下來的步驟

如需如何上架及安裝 Microsoft 安全性程式碼分析的指示，請參閱我們的上線 [和安裝指南](security-code-analysis-onboard.md)。

如需設定組建工作的詳細資訊，請參閱我們的設定 [指南](security-code-analysis-customize.md) 或 [YAML 設定指南](yaml-configuration.md)。

如果您對於擴充功能和所提供的工具有進一步的疑問，請參閱我們的 [常見問題頁面](security-code-analysis-faq.md)。
