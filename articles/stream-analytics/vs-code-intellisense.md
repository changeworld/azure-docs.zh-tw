---
title: 以視覺化工作室代碼的 Azure 串流分析工具中的智慧感知
description: 本文介紹如何在 Azure 串流分析工具中使用 Visual Studio 代碼的 IntelliSense 功能。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394393"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>以視覺化工作室代碼的 Azure 串流分析工具中的智慧感知

IntelliSense 可用於[用於 VS 代碼的 Azure 串流分析工具](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview)中的[串流分析查詢語言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json)。 IntelliSense 是包含一些功能的程式碼完成輔助工具：列出成員、參數資訊、快速諮詢和自動完成文字。 IntelliSense 功能有時被其他名稱(如"代碼完成"、"內容輔助"和"代碼提示")調用。

![IntelliSense 簡報](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>Intellisense 功能

VS 代碼串流分析工具中的 IntelliSense 功能由語言服務提供支援。 語言服務分析原始碼,並根據語言語義提供智慧代碼完成。 如果語言服務知道可能的完成情況,則在鍵入時彈出 IntelliSense 建議。 如果繼續鍵入,將篩選成員清單(如變數和方法),以僅包括包含鍵入的字元的成員。 當您按`Tab``Enter`或 鍵時,IntelliSense 將插入您選擇的成員。

您可以通過鍵入觸發器字元(如點字`.`元 )在任何編輯器視窗中觸發 IntelliSense。

![對內感知自動完成](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> 建議小部件支援 CamelCase 篩選。 您可以在方法名稱中鍵入大寫字母以限制建議。 例如,"cra"會迅速提出"創建應用程式"。

### <a name="types-of-completions"></a>完成類型

VS 代碼 IntelliSense 的流分析工具提供不同類型的完成,包括語言伺服器建議、代碼段和簡單的基於單詞的文本完成。

|       |         |       |
| ----- | ------- | ----- |
| 關鍵字 | `keyword`
| 函式 | `build-in function`, `user defined function`  |
| 資料集名稱| `input`, `output`, `intermediate result set`|
| 資料集欄名稱|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>名稱完成

除了關鍵字自動完成外,VS Code 的流分析工具還讀取作業輸入和輸出名稱的清單,以及數據源中配置列的名稱。 延伸記住此資訊以提供可用於輸入幾個擊鍵的語句的名稱完成功能:

編碼時,無需離開編輯器即可對作業輸入名稱、輸出名稱和列名稱執行搜索。 您可以保留上下文、尋找所需的資訊、將元素直接插入到代碼中,以及讓 IntelliSense 為您完成鍵入。

請注意,您需要設定本地輸入或即時輸入,並保存設定檔才能使用名稱完成。

![名稱完成](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>參數資訊

IntelliSense**參數資訊**選項將打開一個參數清單,該清單提供有關函數所需的參數的數量、名稱和類型的資訊。 粗體參數表示鍵入函數時所需的下一個參數。

巢狀函數也有參數清單。 如果您將函數當作參數輸入到另一個函數中，參數清單會顯示內部函數的參數。 之後，當內部函數參數清單完成時，參數清單會回復成顯示外部函數參數。

![參數資訊](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>快速諮詢

根據語言服務提供,您可以在代碼中查看每個識別碼的快速**資訊**。 標識符的一些範例是輸入、輸出、中間結果集或函數。 將滑鼠指標移到標識符上時,其聲明將顯示在彈出視窗中。 將顯示輸入的屬性和資料架構(如果已配置)和中間資料集。

![快速資訊](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>針對 IntelliSense 進行疑難排解

此問題是由缺少提供數據的輸入配置引起的。 您可以檢查[本地輸入](visual-studio-code-local-run.md#define-a-local-input)或[即時輸入](visual-studio-code-local-run-live-input.md#define-a-live-stream-input)是否設定正確。

## <a name="next-steps"></a>後續步驟

* [快速入門:在可視化工作室代碼中創建 Azure 流分析作業](quick-create-vs-code.md)
* [使用視覺化工作室代碼使用範例資料在本地測試串流分析查詢](visual-studio-code-local-run.md)
* [使用視覺化工作室代碼在本地測試流分析查詢,反對即時流輸入](visual-studio-code-local-run-live-input.md)
