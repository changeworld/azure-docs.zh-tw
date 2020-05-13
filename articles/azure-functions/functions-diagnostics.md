---
title: Azure Functions 診斷總覽
description: 瞭解如何使用 Azure Functions 診斷來疑難排解函數應用程式的問題。
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122355"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure Functions 診斷總覽

當您執行函式應用程式時，您想要針對任何可能發生的問題（從4xx 錯誤到觸發失敗）進行準備。 Azure Functions 診斷是一種智慧型且互動式的體驗，可協助您針對函式應用程式進行無設定或額外成本的疑難排解。 當您的函數應用程式發生問題時，Azure Functions 診斷會指出錯誤的原因。 它會引導您取得正確的資訊，讓您更輕鬆快速地進行疑難排解並解決問題。 本文說明如何使用 Azure Functions 診斷，更快速地診斷和解決常見函數應用程式問題的基本概念。

## <a name="start-azure-functions-diagnostics"></a>啟動 Azure Functions 診斷

若要啟動 Azure Functions 診斷：

1. 在[Azure 入口網站](https://portal.azure.com)中流覽至您的函數應用程式。
1. 選取 [**診斷並解決問題**] 以開啟 Azure Functions 診斷。
1. 使用 [首頁] 磚中的關鍵字，選擇最能描述函數應用程式問題的類別。 您也可以在搜尋列中輸入最能描述您問題的關鍵字。 例如，您可以輸入， `execution` 以查看與函數應用程式執行相關的診斷報告清單，並直接從首頁開啟它們。

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="搜尋 Azure Functions 診斷。" border="true":::

## <a name="use-the-interactive-interface"></a>使用互動式介面

一旦您選取最符合函式應用程式問題的首頁類別，名為 Genie 的 Azure Functions 診斷 ' 互動式介面就可以引導您完成應用程式的診斷和解決問題。 您可以使用 Genie 所提供的磚快捷方式，來查看您感興趣之問題類別的完整診斷報告。 磚快捷方式可讓您直接存取診斷計量。

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Genie 是 Azure Functions 診斷 ' 介面。" border="false":::

選取磚之後，您可以看到與磚中所述問題相關的主題清單。 這些主題提供完整報告中值得注意的資訊片段。 選取任一主題以進一步調查問題。 此外，您也可以選取 [**查看完整報表**]，以流覽單一頁面上的所有主題。

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="診斷報表預覽" border="false":::

## <a name="view-a-diagnostic-report"></a>查看診斷報表

選擇主題之後，您可以查看函式應用程式特定的診斷報告。 診斷報表會使用狀態圖示來指出您的應用程式是否有任何特定的問題。 您會看到問題的詳細描述、建議的動作、相關的計量，以及實用的檔。自訂的診斷報告是從函式應用程式上執行的一系列檢查所產生。 診斷報告可以用來在函數應用程式中查明問題，並引導您解決問題。

## <a name="find-the-problem-code"></a>找出問題代碼

針對以腳本為基礎的函式，您可以使用**函數應用程式下**的**函數執行和錯誤**，或回報錯誤，以縮小造成例外狀況或錯誤的程式程式碼。 您可以使用此工具來取得根本原因，並修正特定程式程式碼的問題。 此選項不適用於先行編譯的 c # 和 JAVA 函式。

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="功能執行錯誤的診斷報告" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="例外狀況詳細資料的查看。" border="false":::

## <a name="next-steps"></a>後續步驟

您可以在[UserVoice](https://feedback.azure.com/forums/355860-azure-functions)提出問題或提供 Azure Functions 診斷的意見反應。 包含 `[Diag]` 在您的意見反應標題中。

> [!div class="nextstepaction"]
> [監視您的函數應用程式](functions-monitoring.md)
