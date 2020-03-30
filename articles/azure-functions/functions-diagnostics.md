---
title: Azure 功能診斷概述
description: 瞭解如何使用 Azure 函數診斷解決函數應用的問題。
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834040"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure 函數診斷概述

運行函數應用時，需要為可能出現的任何問題做好準備，從 4xx 錯誤到觸發故障。 Azure 函數診斷是一種智慧互動式體驗，可説明您無需配置或額外費用解決功能應用故障。 當您遇到函數應用問題時，Azure 函數診斷會指出問題所在，以指導您獲得正確的資訊，以便更輕鬆、更快速地疑難排解並解決問題。 本文介紹如何使用 Azure 函數診斷來更快地診斷和解決常見函數應用問題的基礎知識。

## <a name="start-azure-functions-diagnostics"></a>啟動 Azure 函數診斷

要訪問 Azure 函數診斷：

1. 導航到[Azure 門戶](https://portal.azure.com)中的函數應用。
2. 選擇"**平臺功能**"選項卡。
3. 選擇"在**資源管理**下**診斷並解決問題**"，這將打開 Azure 函數診斷。
4. 使用主頁磁貼中的關鍵字選擇最能描述函數應用問題的類別。 您還可以在搜索欄中鍵入最能描述您的問題的關鍵字。 例如，您可以鍵入`execution`以查看與函數應用執行相關的診斷報告清單，並直接從主頁打開它們。

![首頁](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>使用互動式介面

選擇最適合功能應用問題的主頁類別後，Azure 函數診斷的互動式介面 Genie 可以指導您完成應用問題的診斷和解決。 您可以使用 Genie 提供的磁貼快捷方式查看您感興趣的問題類別的完整診斷報告。 磁貼快捷方式提供了訪問診斷指標的直接方法。

![精靈](./media/functions-diagnostics/genie.png)

選擇磁貼後，可以看到與磁貼中描述的問題相關的主題清單。 這些主題提供了完整報告中的顯著資訊片段。 您可以選擇其中任何一個主題以進一步調查問題。 此外，您還可以選擇 **"查看完整報告**"以流覽單個頁面上的所有主題。

![診斷報告預覽](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>查看診斷報告

選擇主題後，您可以查看特定于函數應用的診斷報告。 診斷報告使用狀態圖示來指示應用是否有任何特定問題。 您將看到問題的詳細描述、建議的操作、相關指標和有用的文檔。自訂診斷報告由在函數應用上運行的一系列檢查生成。 診斷報告是一個有用的工具，用於查明函數應用中的問題，並指導您解決問題。

## <a name="find-the-problem-code"></a>查找問題代碼

對於基於腳本的函數，可以使用**函數應用向下或報告錯誤**下的**函數執行和錯誤**來縮小導致異常或錯誤的程式碼的範圍。 此功能可以是獲取根本原因和修復特定程式碼問題的有用工具。 此選項不適用於預編譯的 C# 和 JAVA 函數。

![函數執行錯誤的診斷報告](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![函數異常](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>後續步驟

您可以在[UserVoice](https://feedback.azure.com/forums/355860-azure-functions)上提問或提供有關 Azure 功能診斷的回饋。 請在回饋`[Diag]`的標題中加入。

> [!div class="nextstepaction"]
> [監視功能應用](functions-monitoring.md)
