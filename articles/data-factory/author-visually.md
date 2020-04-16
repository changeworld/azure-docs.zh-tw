---
title: 視覺創作
description: 了解如何使用 Azure Data Factory 中的視覺化撰寫
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: e7de92878dac72470c0b65d1cf18c1a2d526a0bb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418485"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory 中的視覺化撰寫

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 使用者介面體驗 (UX) 可讓您透過視覺化方式撰寫及部署資料處理站的資源，而不必編寫任何程式碼。 您可以將活動拖放到管線畫布上、執行測試回合、反覆進行偵錯，以及部署和監視管線回合。

目前,Azure 數據工廠 UX 僅在 Microsoft 邊緣和 Google Chrome 中支援。

## <a name="authoring-canvas"></a>創作畫布

要打開**創作畫布**,請按下鉛筆圖示。 

![創作畫布](media/author-visually/authoring-canvas.png)

在這裡,您將創作構成工廠的管道、活動、數據集、連結服務、數據流、觸發器和集成運行時。 要開始使用創作畫布產生導管,請參閱[使用複製活動複製資料](tutorial-copy-data-portal.md)。 

默認視覺創作體驗直接使用數據工廠服務。 還支援 Azure Repos Git 或 GitHub 整合,以允許原始碼管理和協作,以便處理資料工廠管道。 要瞭解有關這些創作體驗之間的差異,請參閱[Azure 資料工廠中的原始程式碼管理](source-control.md)。

## <a name="expressions-and-functions"></a>運算式和函式

運算式和函數可用於在 Azure 數據工廠中指定許多屬性,而不是靜態值。

要指定屬性值的運算式,請選擇「**添加動態內容」** 或單擊**Alt + P,** 同時關注該欄位。

![新增動態內容](media/author-visually/dynamic-content-1.png)

這將打開**資料工廠運算式產生器,** 您可以在其中從受支援的系統變數、活動輸出、函數和使用者指定的變數或參數生成運算式。 

![運算式產生器](media/author-visually/dynamic-content-2.png)

有關表示式語言的資訊,請參閱[Azure 資料工廠 中的運算式和函數](control-flow-expression-language-functions.md)。

## <a name="provide-feedback"></a>提供意見反應

選取 [意見反應]**** 可為功能加上註解，也可以向 Microsoft 通報工具問題：

![意見反應](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>後續步驟

若要深入了解如何監視和管理管線，請參閱[以程式設計方式監視和管理管線](monitor-programmatically.md)。
