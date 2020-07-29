---
title: 視覺化撰寫
description: 了解如何使用 Azure Data Factory 中的視覺化撰寫
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 05/15/2020
ms.openlocfilehash: ac51fd63adcc0328ff67f5fbe13dbfdb768f4bfe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84343103"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory 中的視覺化撰寫

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory 使用者介面體驗 (UX) 可讓您透過視覺化方式撰寫及部署資料處理站的資源，而不必編寫任何程式碼。 您可以將活動拖放到管線畫布上、執行測試回合、反覆進行偵錯，以及部署和監視管線回合。

目前，只有 Microsoft Edge 和 Google Chrome 支援 Azure Data Factory UX。

## <a name="authoring-canvas"></a>撰寫畫布

若要開啟**撰寫畫布**，請按一下鉛筆圖示。 

![撰寫畫布](media/author-visually/authoring-canvas.png)

在這裡，您會撰寫組成中心的管線、活動、資料集、已連結服務、資料流程、觸發程序，以及整合執行階段。 若要開始使用撰寫畫布建立管線，請參閱[使用複製活動複製資料](tutorial-copy-data-portal.md)。 

預設的視覺化撰寫體驗直接使用 Data Factory 服務。 也支援 Azure Repos Git 或 GitHub 整合，允許針對您資料處理站管線上的工作進行原始檔控制和共同作業。 若要深入了解這些撰寫體驗之間的差異，請參閱 [Azure Data Factory 中的原始檔控制](source-control.md)。

### <a name="properties-pane"></a>屬性窗格

對於最上層資源 (例如管線、資料集和資料流程)，可以在畫布右手邊的屬性窗格中編輯高階屬性。 屬性窗格包含名稱、描述、註釋和其他高階屬性之類的屬性。 您可以使用畫布底端部的面板，來編輯管線活動和資料流程轉換之類的子資源。 

![撰寫畫布](media/author-visually/properties-pane.png)

屬性窗格預設只會在建立資源時開啟。 若要進行編輯，請按一下位於畫布右上角的屬性窗格圖示。

## <a name="management-hub"></a>管理中樞

管理中樞（由 Azure Data Factory UX 中的 [*管理*] 索引標籤存取）是一個入口網站，可裝載您 Data Factory 的全域管理動作。 在這裡，您可以管理與資料存放區的連接，以及外部計算、原始檔控制設定和觸發程式設定。 如需詳細資訊，請瞭解[管理中樞](author-management-hub.md)的功能。

![管理連結服務](media/author-management-hub/management-hub-linked-services.png)

## <a name="expressions-and-functions"></a>運算式和函式

運算式和函式可以用來取代靜態值，以指定 Azure Data Factory 中的許多屬性。

若要指定屬性值的運算式，請選取 [新增動態內容] 或按一下 **Alt + P**，同時將焦點放在欄位上。

![新增動態內容](media/author-visually/dynamic-content-1.png)

這會開啟 **Data Factory 運算式產生器**，在這裡您可以從支援的系統變數、活動輸出、函式和使用者指定的變數或參數建立運算式。 

![運算式產生器](media/author-visually/dynamic-content-2.png)

如需運算式語言的相關資訊，請參閱 [Azure Data Factory 中的運算式和函式](control-flow-expression-language-functions.md)。

## <a name="provide-feedback"></a>提供意見反應

選取 [意見反應] 可為功能加上註解，也可以向 Microsoft 通報工具問題：

![意見反應](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>後續步驟

若要深入了解如何監視和管理管線，請參閱[以程式設計方式監視和管理管線](monitor-programmatically.md)。
