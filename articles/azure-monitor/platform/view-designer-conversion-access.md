---
title: Azure 監視器視圖設計工具轉換為活頁簿轉換摘要和存取
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7614dd1d60bad5c124269ae2af02d30a5aacfe3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564027"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>視圖設計工具到活頁簿轉換摘要和存取
[View designer](view-designer.md) 是 Azure 監視器的功能，可讓您建立自訂視圖，以協助您在 Log Analytics 工作區中以圖表、清單和時程表來視覺化資料。 它們會被淘汰，並以活頁簿取代，以提供額外的功能。 本文會詳細說明如何建立存取活頁簿所需的總覽摘要和許可權。

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>從 Azure 儀表板建立您的工作區摘要
視圖設計工具使用者可能很熟悉具有表示一組視圖的總覽磚。 活頁簿會提供釘選的步驟，可以釘選到您的 [Azure 入口網站儀表板](../../azure-portal/azure-portal-dashboards.md)，以像是視圖設計工具工作區摘要。 如同工作區摘要中的總覽磚，釘選的活頁簿專案會直接連結到活頁簿的觀點。

您可以利用 Azure 儀表板所提供的高階自訂功能，讓您可以自動重新整理、移動、調整大小，以及針對您釘選的專案和視覺效果進行額外的篩選。 

![螢幕擷取畫面顯示標題為 [工作區摘要] 的自訂 Azure 儀表板。](media/view-designer-conversion-access/dashboard.png)

建立新的 Azure 儀表板，或選取現有的儀表板以開始釘選活頁簿專案。

若要釘選個別專案，您必須啟用特定步驟的釘選圖示。 若要這樣做，請為您的步驟選取對應的 [ **編輯** ] 按鈕，然後選取齒輪圖示以開啟 [ **Advanced Settings**]。 核取 [ **永遠顯示此步驟的釘**選] 圖示的選項，而且您步驟的右上角會出現釘選圖示。 此 pin 可讓您將特定的視覺效果釘選到儀表板，例如總覽磚。

![釘選步驟](media/view-designer-conversion-access/pin-step.png)


您也可能想要將活頁簿或整個活頁簿內容中的多個視覺效果釘選到儀表板。 若要釘選整個活頁簿，請選取頂端工具列中的 [ **編輯** ]，以切換 **編輯模式**。 釘選圖示將會出現，可讓您釘選整個活頁簿專案或活頁簿中的所有個別步驟和視覺效果。

![全部釘選](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>共用和查看許可權 
活頁簿的優點是私用或共用檔。 根據預設，儲存的活頁簿會儲存在 **我的報表**下，這表示只有建立者可以查看這個活頁簿。

您可以在**編輯模式**中，從頂端工具列中選取 [**共用**] 圖示來共用您的活頁簿。 系統會提示您將活頁簿移至 **共用報表**，而這些報表會產生可直接存取活頁簿的連結。

為了讓使用者能夠查看共用的活頁簿，他們必須能夠存取活頁簿所儲存的訂用帳戶和資源群組。

![以訂用帳戶為基礎的存取](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>後續步驟

- [常見工作](view-designer-conversion-tasks.md)