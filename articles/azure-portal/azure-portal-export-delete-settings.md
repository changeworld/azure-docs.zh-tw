---
title: 匯出或刪除 Azure 門戶設置
description: 瞭解如何在 Azure 門戶中匯出或刪除使用者設置、專用儀表板和自訂設置。
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900780"
---
# <a name="export-or-delete-user-settings"></a>匯出或刪除使用者設定

您可以使用 Azure 門戶中的設置和功能來創建自訂體驗。 有關自訂設置的資訊存儲在 Azure 中。 您可以匯出或刪除以下使用者資料：

* Azure 門戶中的專用儀表板
* 使用者設置（如我的最愛訂閱或目錄）和上次登錄目錄
* 主題和其他自訂門戶設置

最好在刪除設置之前匯出並查看這些設置。 重建儀表板或重做自訂設置可能非常耗時。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>匯出或刪除您的入口網站設定

1. 登錄到 Azure[門戶](https://portal.azure.com)。

1. 在門戶的頁眉中，選擇![設置圖示](media/azure-portal-export-delete-settings/settings-icon.png)**"設置**"。

1. 選取 [匯出所有設定]**** 或 [刪除所有設定和私人儀表板]****。

    ![Azure 門戶設置和設置選項](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      下表描述了這些操作。

      | 動作 | 描述 |
      | --- | --- |
      | **匯出所有設定** | 創建包含使用者設置（如顏色主題、我的最愛和專用儀表板）的 *.json*檔。|
      | **刪除所有設定和私人儀表板** | 刪除指向門戶的專用儀表板和其他自訂設置的所有連結。 |

> [!NOTE]
> 由於使用者設置的動態性質和資料損壞的風險，無法從 *.json*檔導入設置。
>
>

## <a name="next-steps"></a>後續步驟

* [使用角色型存取控制來共用 Azure 儀表板](azure-portal-dashboard-share-access.md)
* [添加、刪除和重新排列我的最愛](azure-portal-add-remove-sort-favorites.md)
