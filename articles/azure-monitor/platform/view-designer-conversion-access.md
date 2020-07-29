---
title: Azure 監視器視圖設計工具轉換成活頁簿轉換摘要和存取
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: fb53a966b0dc4959253ac0786ef09ef3c497e809
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658841"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>將設計工具設為活頁簿轉換摘要和存取
「[視圖設計](view-designer.md)工具」是 Azure 監視器的功能，可讓您建立自訂的視圖，以協助您將 Log Analytics 工作區中的資料視覺化，其中包含圖表、清單和時間軸。 它們會被淘汰，並以活頁簿取代，以提供額外的功能。 本文會詳細說明如何建立存取活頁簿所需的總覽摘要和許可權。

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>從 Azure 儀表板建立工作區摘要
View designer 使用者可能很熟悉具有 [總覽] 磚來表示一組視圖。 為了維護如 [視圖設計工具工作區摘要] 的視覺總覽，活頁簿提供固定的步驟，可釘選到您的[Azure 入口網站儀表板](../../azure-portal/azure-portal-dashboards.md)。 就像工作區摘要中的總覽磚一樣，釘選的活頁簿專案會直接連結到活頁簿視圖。

您可以利用 Azure 儀表板所提供的高階自訂功能，以允許自動重新整理、移動、調整大小，以及針對釘選的專案和視覺效果進行額外的篩選。 

![儀表板](media/view-designer-conversion-access/dashboard.png)

建立新的 Azure 儀表板，或選取現有的儀表板以開始釘選活頁簿專案。

若要釘選個別專案，您必須啟用特定步驟的釘選圖示。 若要這麼做，請選取步驟的對應 [**編輯**] 按鈕，然後選取齒輪圖示以開啟 [**高級設定**]。 核取 [**一律在此步驟顯示釘選圖示**] 的選項，而圖釘圖示會出現在步驟的右上角。 此 pin 可讓您將特定的視覺效果釘選到儀表板，例如 [總覽] 磚。

![Pin 步驟](media/view-designer-conversion-access/pin-step.png)


您也可能想要將活頁簿或整個活頁簿內容中的多個視覺效果釘選到儀表板。 若要釘選整個活頁簿，請選取頂端工具列中的 [**編輯**] 來切換**編輯模式**。 釘選圖示將會出現，讓您可以釘選整個活頁簿專案或活頁簿中的所有個別步驟和視覺效果。

![全部釘選](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>共用和查看許可權 
活頁簿的優點是私用或共用的檔。 儲存的活頁簿預設會儲存在**我的報表**底下，這表示只有建立者可以觀看此活頁簿。

您可以在**編輯模式**中，從頂端工具列選取 [**共用**] 圖示來共用您的活頁簿。 系統會提示您將活頁簿移至 [**共用報表**]，這會產生可直接存取活頁簿的連結。

為了讓使用者能夠查看共用的活頁簿，他們必須能夠存取儲存活頁簿的訂用帳戶和資源群組。

![以訂用帳戶為基礎的存取](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>後續步驟

- [常見工作](view-designer-conversion-tasks.md)