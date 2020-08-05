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
ms.openlocfilehash: 3d9caabe3dfde34964f662dbf01316b2cbb0bcab
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543005"
---
# <a name="about-microsoft-security-code-analysis"></a>關於 Microsoft 安全性程式碼分析

透過 Microsoft 安全性程式碼分析延伸模組，小組可以在 Azure DevOps 持續整合和傳遞 (CI/CD) 管線中，新增安全性程式碼分析。 這項分析是由 Microsoft 的[SDL) 專家 (的安全開發生命週期](https://www.microsoft.com/securityengineering/sdl/practices)所建議。

一致的 UX 藉由隱藏執行工具的複雜性來簡化安全性。 有了以 NuGet 為基礎的工具傳遞，小組就不再需要管理安裝或更新工具。 有了組建工作的命令列和基本介面，所有使用者都可以視需要對工具有很大的控制權。

小組也可以使用功能強大的後處理功能，例如：

- 發行記錄以進行保留。
- 產生可操作、以開發人員為主的報表。
- 設定迴歸測試的組建中斷。

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>為什麼我應該使用 Microsoft 安全性程式碼分析？

### <a name="security-simplified"></a>簡化安全性

將 Microsoft 安全性程式碼分析工具加入您的 Azure DevOps 管線，就像加入新工作一樣簡單。 自訂工作或使用其預設行為。 工作會做為 Azure DevOps 管線的一部分來執行，並產生記錄以詳細說明許多種類的結果。

### <a name="clean-builds"></a>清除組建

解決工具所報告的初始問題之後，您可以設定延伸模組，以中斷新問題的組建。在每個提取要求上設定持續整合組建很容易。

### <a name="set-it-and-forget-it"></a>加以設定並忘記

根據預設，組建工作和工具會保持最新狀態。 如果有工具的更新版本，您就不需要下載並安裝它。 延伸模組會為您處理更新。

### <a name="under-the-hood"></a>背後原理

延伸模組的組建工作會隱藏下列複雜度：
  - 正在執行安全性靜態分析工具。
  - 處理記錄檔的結果來建立摘要報告或中斷組建。

## <a name="microsoft-security-code-analysis-tool-set"></a>Microsoft 安全性程式碼分析工具集

Microsoft 安全性程式碼分析延伸模組讓您隨時都能使用最新版本的重要分析工具。 此延伸模組同時包含了 Microsoft 管理的工具和開放原始碼工具。

當您使用對應的組建工作來設定和執行管線之後，這些工具會自動下載到雲端裝載的代理程式。

本節列出延伸模組中目前可用的工具組。 請留意其他工具的新增。 此外，請將您的建議傳送給我們，以取得您想要我們新增的工具。

### <a name="anti-malware-scanner"></a>反惡意程式碼掃描器

[反惡意程式碼掃描器] 組建工作現在包含在 [Microsoft 安全性程式碼分析] 延伸模組中。 這項工作必須在已經安裝 Windows Defender 的組建代理程式上執行。 如需詳細資訊，請參閱[Windows Defender 網站](https://aka.ms/defender)。

### <a name="binskim"></a>BinSkim

BinSkim 是可移植的可執行檔 (PE) 輕量掃描器，可驗證編譯器設定、連結器設定，以及二進位檔案的其他安全性相關特性。 此組建工作會在 binskim.exe 主控台應用程式周圍提供命令列包裝函式。 BinSkim 是一個開放原始碼工具。 如需詳細資訊，請參閱[GitHub 上的 BinSkim](https://github.com/Microsoft/binskim)。

### <a name="credential-scanner"></a>認證掃描器

儲存在原始程式碼中的密碼和其他秘密都是嚴重的問題。 認證掃描器是一種專屬的靜態分析工具，可協助解決此問題。 此工具會偵測您的原始程式碼和組建輸出中的認證、秘密、憑證和其他機密內容。

### <a name="roslyn-analyzers"></a>Roslyn 分析器

Roslyn 分析器是 Microsoft 用來以靜態方式分析 managed c # 和 Visual Basic 程式碼的編譯器整合工具。 如需詳細資訊，請參閱[Roslyn 為基礎的分析器](https://docs.microsoft.com/dotnet/standard/analyzers/)。

### <a name="tslint"></a>TSLint

TSLint 是可延伸的靜態分析工具，可檢查 TypeScript 程式碼的可讀性、可維護性和錯誤的功能。 現代化的編輯器和組建系統廣泛支援。 您可以使用自己不起毛的規則、設定和格式器進行自訂。 TSLint 是一個開放原始碼工具。 如需詳細資訊，請參閱[GitHub 上的 TSLint](https://github.com/palantir/tslint)。

## <a name="analysis-and-post-processing-of-results"></a>分析和後置處理結果

Microsoft 安全性程式碼分析延伸模組也有三個後處理工作。 這些工作可協助您分析安全性工具工作所找到的結果。 新增至管線時，這些工作通常會遵循所有其他工具工作。

### <a name="publish-security-analysis-logs"></a>發行安全性分析記錄

[發行安全性分析記錄] 組建工作會保留在組建期間執行之安全性工具的記錄檔。 您可以閱讀這些記錄以進行調查和追蹤。

您可以將記錄檔發佈到 Azure Artifacts 做為 .zip 檔案。 您也可以從您的私用組建代理程式，將它們複製到可存取的檔案共用。

### <a name="security-report"></a>安全性報告

「安全性報表組建」工作會剖析記錄檔。 這些檔案是由組建期間執行的安全性工具所建立。 然後，組建工作會建立單一摘要報告檔案。 此檔案會顯示分析工具發現的所有問題。

您可以設定此工作來報告特定工具或所有工具的結果。 您也可以選擇要報告的問題層級，例如僅限錯誤或錯誤和警告。

### <a name="post-analysis-build-break"></a>後續分析 (組建中斷) 

使用 [後期分析組建] 工作，您可以插入刻意導致組建失敗的組建中斷。 如果程式碼中有一或多個分析工具回報問題，您會插入組建中斷。

您可以設定此工作來中斷組建，找出特定工具或所有工具所找到的問題。 您也可以根據找到的問題嚴重性來設定它，例如錯誤或警告。

>[!NOTE]
>根據設計，如果工作順利完成，每個組建工作都會成功。 無論工具是否發現問題，都是如此，如此一來，就能允許所有工具執行，讓組建執行完成。

## <a name="next-steps"></a>後續步驟

如需有關如何上架及安裝 Microsoft 安全性程式碼分析的指示，請參閱我們的上線[和安裝指南](security-code-analysis-onboard.md)。

如需設定組建工作的詳細資訊，請參閱我們的設定[指南](security-code-analysis-customize.md)或[YAML 設定指南](yaml-configuration.md)。

如果您有關于擴充功能和所提供工具的進一步問題，請查看我們的[常見問題頁面](security-code-analysis-faq.md)。
