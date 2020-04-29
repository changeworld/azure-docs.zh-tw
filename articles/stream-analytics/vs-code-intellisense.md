---
title: 適用于 Visual Studio Code 的 Azure 串流分析工具中的 IntelliSense
description: 本文說明如何使用適用于 Visual Studio Code 的 Azure 串流分析工具中的 IntelliSense 功能。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81394393"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>適用于 Visual Studio Code 的 Azure 串流分析工具中的 IntelliSense

IntelliSense 適用于適用于[VS Code 的 Azure 串流分析工具](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview)中的[串流分析查詢語言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json)。 IntelliSense 是包含一些功能的程式碼完成輔助工具：列出成員、參數資訊、快速諮詢和自動完成文字。 其他名稱有時會呼叫 IntelliSense 功能，例如「程式碼完成」、「內容協助」和「程式碼提示」。

![IntelliSense 示範](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>Intellisense 功能

適用于 VS Code 的串流分析工具中的 IntelliSense 功能是由語言服務提供技術支援。 語言服務會分析您的原始程式碼，並根據語言語義提供智慧型程式碼完成。 如果語言服務知道可能的完成，IntelliSense 建議會在您輸入時彈出。 如果您繼續輸入，則會篩選成員清單（例如變數和方法），只包含包含您所輸入之字元的成員。 當您按下`Tab`或`Enter`鍵時，IntelliSense 會插入您選取的成員。

您可以輸入觸發程式字元（例如點字元`.`），在任何編輯器視窗中觸發 IntelliSense。

![intellisense 自動完成](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> [建議] 小工具支援 CamelCase 篩選。 您可以輸入要在方法名稱中大寫的字母，以限制建議。 例如，"cra" 會快速顯示「createApplication」。

### <a name="types-of-completions"></a>完成的類型

適用于 VS Code IntelliSense 的串流分析工具提供不同類型的完成，包括語言伺服器建議、程式碼片段，以及簡單的單字文字完成。

|       |         |       |
| ----- | ------- | ----- |
| 關鍵字 | `keyword`
| 函式 | `build-in function`, `user defined function`  |
| 資料集名稱| `input`, `output`, `intermediate result set`|
| 資料集資料行名稱|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>名稱完成

除了關鍵字自動完成之外，VS Code 的串流分析工具會讀取作業輸入和輸出名稱的清單，以及資料來源中設定時的資料行名稱。 延伸模組會記住這種資訊，以提供名稱完成功能，適用于使用幾個按鍵輸入語句：

撰寫程式碼時，您不需要離開編輯器，就能對作業輸入名稱、輸出名稱和資料行名稱執行搜尋。 您可以保留您的內容、尋找所需的資訊、將元素直接插入程式碼中，並讓 IntelliSense 為您完成輸入。

請注意，您必須設定本機輸入或即時輸入，並儲存設定檔，才能使用名稱完成。

![名稱完成](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>參數資訊

IntelliSense 的 [**參數資訊**] 選項會開啟一個參數清單，其中提供函數所需參數的數目、名稱和類型的相關資訊。 粗體的參數表示當您輸入函數時所需的下一個參數。

巢狀函數也有參數清單。 如果您將函數當作參數輸入到另一個函數中，參數清單會顯示內部函數的參數。 之後，當內部函數參數清單完成時，參數清單會回復成顯示外部函數參數。

![參數資訊](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>快速諮詢

如語言服務所提供，您可以在程式碼中查看每個識別碼的**快速資訊**。 識別碼的範例包括輸入、輸出、中繼結果集或函數。 當您將滑鼠指標移到識別碼上方時，它的宣告會顯示在快顯視窗中。 會顯示輸入的屬性和資料架構（如果已設定）和元資料集。

![快速諮詢](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>針對 IntelliSense 進行疑難排解

此問題是由提供資料的遺漏輸入設定所造成。 您可以檢查[本機輸入](visual-studio-code-local-run.md#define-a-local-input)或[即時輸入](visual-studio-code-local-run-live-input.md#define-a-live-stream-input)是否已正確設定。

## <a name="next-steps"></a>後續步驟

* [快速入門：在 Visual Studio Code 中建立 Azure 串流分析作業](quick-create-vs-code.md)
* [使用 Visual Studio Code 和範例資料在本機測試串流分析查詢](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 在本機針對即時串流輸入測試串流分析查詢](visual-studio-code-local-run-live-input.md)
