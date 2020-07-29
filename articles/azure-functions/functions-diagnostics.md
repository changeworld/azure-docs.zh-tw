---
title: Azure Functions 診斷概觀
description: 了解如何使用 Azure Functions 診斷來針對函式應用程式的問題進行疑難排解。
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83122355"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure Functions 診斷概觀

當您執行函式應用程式時，您會希望自己已做好準備而能夠應付可能發生的任何問題 (從4xx 錯誤到觸發程序失敗)。 Azure Functions 診斷是一種智慧型和互動式的體驗，可協助您對函式應用程式進行疑難排解，而不必進行設定或產生額外成本。 當您的函式應用程式真的發生問題時，Azure Functions 診斷會指出問題所在。 其會引導您找得正確資訊，讓您更輕鬆、快速地進行疑難排解並解決問題。 本文會說明一些基本概念，讓您了解如何使用 Azure Functions 診斷更快速地診斷和解決常見的函式應用程式問題。

## <a name="start-azure-functions-diagnostics"></a>啟動 Azure Functions 診斷

若要啟動 Azure Functions 診斷：

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至您的函式應用程式。
1. 選取 [診斷並解決問題] 以開啟 Azure Functions 診斷。
1. 在 [首頁] 圖格中使用關鍵字，選擇最能描述函式應用程式問題的類別。 您也可以在搜尋列中輸入最能描述問題的關鍵字。 例如，您可以輸入 `execution` 來查看與函式應用程式執行相關的診斷報告清單，並直接從首頁開啟這些報告。

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="搜尋 Azure Functions 診斷。" border="true":::

## <a name="use-the-interactive-interface"></a>使用互動式介面

選取了最符合函式應用程式問題的首頁類別後，名為 Genie 的 Azure Functions 診斷互動式介面就可以引導您診斷並解決應用程式的問題。 您可以使用 Genie 所提供的圖格快速鍵，來檢視您有興趣了解的問題類別所擁有的完整診斷報告。 圖格快速鍵可讓您直接存取診斷計量。

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Genie 是 Azure Functions 診斷的介面。" border="false":::

選取圖格之後，您可以看到與圖格中所述問題相關的主題清單。 這些主題會提供完整報告中值得注意的資訊片段。 選取任一主題即可進一步調查問題。 此外，您也可以選取 [檢視完整報告] 以在單一頁面上瀏覽所有主題。

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="診斷報告預覽" border="false":::

## <a name="view-a-diagnostic-report"></a>檢視診斷報告

選擇主題之後，您可以檢視函式應用程式特定的診斷報告。 診斷報表會使用狀態圖示來指出您的應用程式是否有任何特定問題。 您會看到問題的詳細描述、建議的動作、相關的計量，以及會有所幫助的文件。透過對函式應用程式所執行的一系列檢查會產生自訂的診斷報告。 診斷報告會是很實用的工具，可指出函式應用程式中的問題，並引導您解決問題。

## <a name="find-the-problem-code"></a>尋找問題程式碼

對於以指令碼為基礎的函式，您可以使用 [函式應用程式關閉或報告錯誤] 底下的 [函式執行和錯誤]，來縮小造成例外狀況或錯誤的程式碼範圍。 您可以使用此工具來取得根本原因，並修正特定程式碼的問題。 此選項不適用於先行編譯的 C# 和 Java 函式。

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="函式執行錯誤的診斷報告" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="例外狀況詳細資料的檢視。" border="false":::

## <a name="next-steps"></a>後續步驟

您可以在 [UserVoice](https://feedback.azure.com/forums/355860-azure-functions) 中提出問題或提供關於 Azure Functions 診斷的意見反應。 請在您的意見反應標題中包含 `[Diag]`。

> [!div class="nextstepaction"]
> [監視您的函式應用程式](functions-monitoring.md)
