---
title: 在 Azure 門戶中恢復已刪除的儀表板 |微軟文檔
description: 如果刪除 Azure 門戶中已發佈的儀表板，則可以恢復儀表板。
services: azure-portal
author: mgblythe
ms.author: mblythe
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: af0c72f0bc5dd8f3a3cbae7b82b1ac56447cbb08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133283"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>在 Azure 門戶中恢復已刪除的儀表板

如果您在公共 Azure 雲中，並且刪除了 Azure 門戶中_已發佈的_儀表板，則可以在刪除後的 14 天內恢復該儀表板。 如果您位於 Azure 政府雲中或儀表板未發佈，則無法恢復它，必須重新生成它。 有關發佈儀表板的詳細資訊，請參閱[發佈儀表板](azure-portal-dashboard-share-access.md#publish-dashboard)。 按照以下步驟恢復已發佈的儀表板：

1. 在 Azure 門戶功能表中，選擇 **"資源組**"，然後選擇發佈儀表板的資源組（預設情況下，它被命名為**儀表板**）。

1. 在 **"活動日誌**"下，展開 **"刪除儀表板"** 操作。 選擇"**更改歷史記錄**"選項卡，然後選擇**\<已刪除\>的資源**。

    ![更改歷史記錄選項卡的螢幕截圖](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. 選擇並複製左側窗格的內容，然後保存到具有 _.json_檔副檔名的文字檔中。 門戶使用 JSON 檔重新創建儀表板。

    ![更改歷史記錄差異的螢幕截圖](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. 在 Azure 門戶功能表中，選擇**儀表板**，然後選擇 **"上載**"。

    ![儀表板上傳的螢幕截圖](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. 選擇您保存的 JSON 檔。 門戶重新創建與已刪除儀表板具有相同名稱和元素的儀表板。

1. 選擇 **"共用**"以發佈儀表板並重新建立相應的存取控制。

    ![儀表板共用的螢幕截圖](media/recover-shared-deleted-dashboard/dashboard-share.png)
