---
title: 復原 Azure 入口網站中已刪除的儀表板 |Microsoft Docs
description: 如果您在 Azure 入口網站中刪除已發佈的儀表板，就可以復原儀表板。
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ead9dab61e0cb60505aed9db43c61eb73a0e22a9
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760193"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>復原 Azure 入口網站中已刪除的儀表板

如果您在 Azure 入口網站中刪除_已發佈_的儀表板，您可以在刪除後的14天內復原該儀表板。 如果儀表板尚未發行，您就無法加以復原，而且必須重建它。 如需發佈儀表板的詳細資訊，請參閱[發行儀表板](azure-portal-dashboard-share-access.md#publish-dashboard)。 請遵循下列步驟來復原已發佈的儀表板：

1. 從 [Azure 入口網站] 功能表中，選取 [**資源群組**]，然後選取您發佈儀表板的資源群組（預設為 [**儀表板**]）。

1. 在 [**活動記錄**] 底下，展開 [**刪除儀表板**] 作業。 選取 [**變更歷程記錄**] 索引標籤，然後選取 [ **\<刪除的資源\>** ]。

    ![[變更歷程記錄] 索引標籤的螢幕擷取畫面](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. 選取並複製左窗格的內容，然後儲存到副檔名為_json_的文字檔。 入口網站會使用 JSON 檔案來重新建立儀表板。

    ![變更歷程記錄差異的螢幕擷取畫面](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. 從 [Azure 入口網站] 功能表中，選取 [**儀表板**]，然後選取 **[上傳**]。

    ![儀表板上傳的螢幕擷取畫面](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. 選取您儲存的 JSON 檔案。 入口網站會重新建立儀表板，其名稱和元素與已刪除的儀表板相同。

1. 選取 [**共用**] 以發佈儀表板，並重新建立適當的存取控制。

    ![儀表板共用的螢幕擷取畫面](media/recover-shared-deleted-dashboard/dashboard-share.png)
