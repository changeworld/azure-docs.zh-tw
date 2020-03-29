---
title: 微軟安全代碼分析文檔概述
description: 本文概述了 Microsoft 安全代碼分析擴展
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
ms.openlocfilehash: 963bc909b69962cded0a50d717e3a653d3d69769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851463"
---
# <a name="about-microsoft-security-code-analysis"></a>關於微軟安全代碼分析

使用 Microsoft 安全代碼分析擴展，團隊可以將安全代碼分析添加到其 Azure DevOps 持續集成和交付 （CI/CD） 管道中。 此分析是由 Microsoft[的安全開發生命週期 （SDL）](https://www.microsoft.com/securityengineering/sdl/practices)專家推薦的。

一致的 UX 通過隱藏運行工具的複雜性來簡化安全性。 通過基於 NuGet 的工具交付，團隊不再需要管理工具的安裝或更新。 使用生成任務的命令列和基本介面，所有使用者都可以根據需要對工具進行盡可能多的控制。

團隊還可以使用強大的後處理功能，例如：

- 發佈日誌以保留。
- 生成可操作的、以開發人員為中心的報告。
- 在迴歸測試中配置生成中斷。

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>為什麼我應該使用 Microsoft 安全代碼分析？

### <a name="security-simplified"></a>安全簡化

將 Microsoft 安全代碼分析工具添加到 Azure DevOps 管道就像添加新任務一樣簡單。 自訂任務或使用其預設行為。 任務作為 Azure DevOps 管道的一部分運行，並生成詳細說明多種結果的日誌。

### <a name="clean-builds"></a>清理生成

解決工具報告的初始問題後，可以將擴展配置為在新問題上中斷構建。在每個拉取請求的基礎上設置持續集成構建非常簡單。

### <a name="set-it-and-forget-it"></a>設置它，忘記它

預設情況下，生成任務和工具保持最新。 如果有工具的更新版本，則無需下載並安裝它。 擴展負責為您更新。

### <a name="under-the-hood"></a>背後原理

擴展的生成任務隱藏了以下的複雜性：
  - 運行安全靜態分析工具。
  - 處理日誌檔的結果以創建摘要報告或中斷生成。

## <a name="microsoft-security-code-analysis-tool-set"></a>微軟安全代碼分析工具集

Microsoft 安全代碼分析擴展使重要分析工具的最新版本隨時可供您使用。 擴展包括 Microsoft 管理的工具和開源工具。

使用相應的生成任務配置和運行管道後，這些工具將自動下載到雲託管代理。

本節列出了擴展中當前可用的工具集。 注意添加更多工具。 此外，請向我們發送您希望我們添加的工具的建議。

### <a name="anti-malware-scanner"></a>反惡意軟體掃描器

反惡意軟體掃描程式生成任務現在包含在 Microsoft 安全代碼分析擴展中。 此任務必須在已安裝 Windows Defender 的生成代理上運行。 有關詳細資訊，請參閱 Windows[防禦者網站](https://aka.ms/defender)。

### <a name="binskim"></a>賓斯基姆

BinSkim 是一種可移植可執行 （PE） 羽量級掃描器，用於驗證二進位檔案的編譯器設置、連結器設置和其他與安全相關的特徵。 此生成任務在 binskim.exe 主控台應用程式周圍提供命令列包裝器。 BinSkim 是一個開源工具。 有關詳細資訊，請參閱[GitHub 上的 BinSkim。](https://github.com/Microsoft/binskim)

### <a name="credential-scanner"></a>憑據掃描程式

存儲在原始程式碼中的密碼和其他機密是一個嚴重的問題。 憑據掃描程式是一種專有的靜態分析工具，可説明解決此問題。 該工具檢測原始程式碼和生成輸出中的憑據、機密、證書和其他敏感內容。

### <a name="microsoft-security-risk-detection"></a>微軟安全風險檢測

Microsoft 安全風險檢測 （MSRD） 是一種基於雲的模糊測試服務。 它標識軟體中的可利用安全錯誤。 此服務需要單獨的訂閱和啟動。 有關詳細資訊，請參閱[MSRD 開發人員中心](https://docs.microsoft.com/security-risk-detection/)。

### <a name="roslyn-analyzers"></a>Roslyn 分析器

Roslyn 分析器是 Microsoft 的編譯器集成工具，用於靜態分析託管 C# 和視覺化基本代碼。 有關詳細資訊，請參閱[基於羅斯林的分析儀](https://docs.microsoft.com/dotnet/standard/analyzers/)。

### <a name="tslint"></a>茨林特

TSLint 是一種可擴展的靜態分析工具，用於檢查 TypeScript 代碼的可讀性、可維護性和功能上的錯誤。 它得到了現代編輯器和構建系統的廣泛支援。 您可以使用自己的絨毛規則、配置和事事對其進行自訂。 TSLint 是一個開源工具。 有關詳細資訊，請參閱[GitHub 上的 TSLint。](https://github.com/palantir/tslint)

## <a name="analysis-and-post-processing-of-results"></a>結果分析和後處理

Microsoft 安全代碼分析擴展也有三個後處理任務。 這些任務可説明您分析安全工具任務的結果。 添加到管道時，這些任務通常遵循所有其他工具任務。

### <a name="publish-security-analysis-logs"></a>發佈安全分析日誌

發佈安全分析日誌生成任務保留生成期間運行的安全工具的日誌檔。 您可以閱讀這些日誌進行調查和跟進。

可以將日誌檔作為 .ZIP 檔案發佈到 Azure 專案。 您還可以將它們複製到來自私有生成代理的可訪問檔共用。

### <a name="security-report"></a>安全報告

安全報告生成任務解析日誌檔。 這些檔由生成期間運行的安全工具創建。 然後，生成任務將創建單個摘要報表檔。 此檔顯示分析工具發現的所有問題。

您可以將此任務配置為報告特定工具或所有工具的結果。 您還可以選擇要報告的問題級別，例如僅錯誤或錯誤和警告。

### <a name="post-analysis-build-break"></a>分析後（生成中斷）

使用"分析後"生成任務時，可以注入有意導致生成失敗的生成中斷。 如果一個或多個分析工具報告代碼中的問題，則注入生成中斷。

可以配置此任務以中斷特定工具或所有工具發現的問題的生成。 您還可以根據發現的問題（如錯誤或警告）的嚴重性對其進行配置。

>[!NOTE]
>根據設計，如果任務成功完成，則每個生成任務都會成功。 無論工具是否發現問題，都是如此，因此，通過允許運行所有工具，生成可以運行到完成。

## <a name="next-steps"></a>後續步驟

有關如何載入和安裝 Microsoft 安全代碼分析的說明，請參閱我們的[載入和安裝指南](security-code-analysis-onboard.md)。

有關配置生成任務的詳細資訊，請參閱我們的[配置指南](security-code-analysis-customize.md)或[YAML 配置指南](yaml-configuration.md)。

如果您對擴展和提供的工具有任何疑問，請查看我們的[常見問題頁面](security-code-analysis-faq.md)。
