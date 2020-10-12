---
title: 一次處理許多工作區中的 Azure Sentinel 事件 |Microsoft Docs
description: 如何在 Azure Sentinel 中同時查看多個工作區中的事件。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: yelevin
ms.openlocfilehash: 448998328ff15b74b0aa0b17e2435a7ff55c54a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83124160"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>一次處理許多工作區中的事件 

 若要充分利用 Azure Sentinel 的功能，Microsoft 建議使用單一工作區環境。 不過，在某些情況下，某些使用案例需要有數個工作區，例如， [受控安全性服務提供者的 (MSSP) ](./multiple-tenants-service-providers.md) 和其客戶–跨多個租使用者。 **多個工作區視圖** 可讓您跨多個工作區同時查看和處理安全性事件，甚至是跨租使用者，讓您可以維持組織安全性回應性的完整可見度和控制權。

## <a name="entering-multiple-workspace-view"></a>輸入多個工作區視圖

當您開啟 Azure Sentinel 時，會在所有選取的租使用者和訂用帳戶中，看到您具有存取權的所有工作區清單。 每個工作區名稱的左邊都有一個核取方塊。 按一下單一工作區的名稱，將帶您進入該工作區。 若要選擇多個工作區，請按一下所有對應的核取方塊，然後按一下頁面頂端的 [ **多個工作區] 視圖** 按鈕。

> [!IMPORTANT]
> 多個工作區視圖目前最多支援10個同時顯示的工作區。 
> 
> 如果您檢查超過10個工作區，則會出現警告訊息。

請注意，在工作區清單中，您可以看到與每個工作區相關聯的目錄、訂用帳戶、位置和資源群組。 目錄會對應至租使用者。

   ![選擇多個工作區](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>使用事件

在 **多個工作區視圖**中，目前只能使用 **事件** 畫面。 它的外觀和運作方式與一般 **事件** 畫面一樣。 但有幾個重要的差異：

   ![查看多個工作區中的事件](./media/multiple-workspace-view/incidents.png)

- 頁面 *開啟事件*、 *新事件*、 *進行中*等的計數器-共同顯示所有選定工作區的數目。

- 您將會在單一統一清單中看到 (租使用者) 的所有選取工作區和目錄中的事件。 除了 [一般 **事件** ] 畫面中的篩選之外，您還可以依工作區和目錄篩選清單。

- 您必須具有所選事件的所有工作區的讀取和寫入權限。 如果您只有部分工作區的讀取權限，則當您在這些工作區中選取事件時，將會看見警告訊息。 即使您有其他) 的許可權，您也將無法修改這些事件或您所選取的任何其他事件，以及這些事件的 (。

- 如果您選擇單一事件，然後按一下 [ **查看完整詳細資料** ] 或 [ **調查**]，您就會在該事件的工作區的資料內容中，而不是其他專案。

## <a name="next-steps"></a>接下來的步驟
在本檔中，您已瞭解如何同時在多個 Azure Sentinel 工作區中查看和處理事件。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

