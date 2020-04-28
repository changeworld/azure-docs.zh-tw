---
title: 視覺效果撰寫
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418485"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory 中的視覺化撰寫

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 使用者介面體驗 (UX) 可讓您透過視覺化方式撰寫及部署資料處理站的資源，而不必編寫任何程式碼。 您可以將活動拖放到管線畫布上、執行測試回合、反覆進行偵錯，以及部署和監視管線回合。

目前，只有 Microsoft Edge 和 Google Chrome 支援 Azure Data Factory UX。

## <a name="authoring-canvas"></a>撰寫畫布

若要開啟**撰寫畫布**，請按一下鉛筆圖示。 

![撰寫畫布](media/author-visually/authoring-canvas.png)

在這裡，您將會撰寫組成 factory 的管線、活動、資料集、連結服務、資料流程、觸發程式和整合執行時間。 若要開始使用撰寫畫布建立管線，請參閱[使用複製活動來複製資料](tutorial-copy-data-portal.md)。 

預設的視覺效果撰寫體驗直接使用 Data Factory 服務。 也支援 Azure Repos Git 或 GitHub 整合，以允許在您的 data factory 管線上進行原始檔控制和共同作業。 若要深入瞭解這些撰寫體驗之間的差異，請參閱[Azure Data Factory 中的原始檔控制](source-control.md)。

## <a name="expressions-and-functions"></a>運算式和函式

運算式和函式可以用來取代靜態值，以指定 Azure Data Factory 中的許多屬性。

若要指定屬性值的運算式，請選取 [**新增動態內容**]，或按一下 [ **Alt + P** ]，同時將焦點放在欄位上。

![新增動態內容](media/author-visually/dynamic-content-1.png)

這會開啟 [ **Data Factory 運算式**產生器]，您可以在其中從支援的系統變數、活動輸出、函數和使用者指定的變數或參數建立運算式。 

![運算式產生器](media/author-visually/dynamic-content-2.png)

如需運算式語言的詳細資訊，請參閱[Azure Data Factory 中的運算式和函數](control-flow-expression-language-functions.md)。

## <a name="provide-feedback"></a>提供意見反應

選取 [意見反應]**** 可為功能加上註解，也可以向 Microsoft 通報工具問題：

![意見反應](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>後續步驟

若要深入了解如何監視和管理管線，請參閱[以程式設計方式監視和管理管線](monitor-programmatically.md)。
