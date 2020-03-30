---
title: Azure 監視器視圖設計器到活頁簿轉換摘要和訪問
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: fb53a966b0dc4959253ac0786ef09ef3c497e809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658841"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>查看設計器以活頁簿轉換摘要和訪問
[視圖設計器](view-designer.md)是 Azure 監視器的一項功能，允許您創建自訂視圖，以説明您在日誌分析工作區中視覺化資料，包括圖表、清單和時間表。 它們正在逐步淘汰，代之以活頁簿，從而提供了額外的功能。 本文詳細介紹了如何創建訪問活頁簿所需的概述摘要和許可權。

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>從 Azure 儀表板創建工作區摘要
視圖設計器使用者可能熟悉使用概覽磁貼來表示一組視圖。 要維護視覺化概覽（如視圖設計器工作區摘要），活頁簿提供固定步驟，這些步驟可以固定到 Azure[門戶儀表板](../../azure-portal/azure-portal-dashboards.md)。 與工作區摘要中的概述磁貼一樣，固定活頁簿項將直接連結到活頁簿視圖。

您可以利用 Azure 儀表板提供的高級別自訂功能，這些功能允許對固定項和視覺化進行自動刷新、移動、調整和附加篩選。 

![儀表板](media/view-designer-conversion-access/dashboard.png)

創建新的 Azure 儀表板或選擇現有儀表板以開始固定活頁簿項。

要固定單個專案，您需要為特定步驟啟用引腳圖示。 為此，請為您的步驟選擇相應的 **"編輯"** 按鈕，然後選擇齒輪圖示以打開 **"高級設置**"。 選中"**始終顯示此步驟上的引腳圖示**"選項，步驟右上角將顯示一個引腳圖示。 此引腳使您能夠將特定視覺化效果固定到儀表板，如概覽磁貼。

![固定步驟](media/view-designer-conversion-access/pin-step.png)


您可能還希望將活頁簿或整個活頁簿內容中的多個視覺化效果固定到儀表板。 要固定整個活頁簿，請在頂部工具列中選擇 **"編輯**"以切換 **"編輯模式**"。 將顯示一個固定圖示，允許您固定整個活頁簿項或活頁簿中的所有單個步驟和視覺化效果。

![固定所有](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>共用和查看許可權 
活頁簿的好處可以是私有文檔，也可以是共用文件。 預設情況下，保存的活頁簿將保存在 **"我的報告"** 下，這意味著只有建立者才能查看此活頁簿。

您可以在 **"編輯模式**"中從頂部工具列中選擇 **"共用**"圖示來共用活頁簿。 系統將提示您將活頁簿移動到**共用報表**，這將生成一個連結，提供對活頁簿的直接存取許可權。

為了使使用者查看共用活頁簿，他們必須同時有權訪問活頁簿保存下的訂閱和資源組。

![基於訂閱的訪問](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>後續步驟

- [常見任務](view-designer-conversion-tasks.md)