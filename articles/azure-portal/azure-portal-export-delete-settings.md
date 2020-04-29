---
title: 匯出或刪除 Azure 入口網站設定
description: 瞭解如何匯出或刪除您的使用者設定、私人儀表板，以及 Azure 入口網站中的自訂設定。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76900780"
---
# <a name="export-or-delete-user-settings"></a>匯出或刪除使用者設定

您可以使用 Azure 入口網站中的設定和功能來建立自訂體驗。 您的自訂設定的相關資訊會儲存在 Azure 中。 您可以匯出或刪除下列使用者資料：

* Azure 入口網站中的私用儀表板
* 使用者設定，例如我的最愛訂閱或目錄，以及最後一個登入的目錄
* 主題和其他自訂入口網站設定

在刪除您的設定之前，最好先匯出並加以檢查。 重建儀表板或重做自訂設定可能相當耗時。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>匯出或刪除您的入口網站設定

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在入口網站的標頭中， ![選取 [](media/azure-portal-export-delete-settings/settings-icon.png)設定] 圖示 [**設定**]。

1. 選取 [匯出所有設定]**** 或 [刪除所有設定和私人儀表板]****。

    ![Azure 入口網站設定和設定選項](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      下表描述這些動作。

      | 動作 | 描述 |
      | --- | --- |
      | **匯出所有設定** | 建立*json*檔案，其中包含您的使用者設定，例如您的色彩主題、我的最愛和私人儀表板。|
      | **刪除所有設定和私人儀表板** | 刪除私人儀表板的所有連結，以及您在入口網站中所做的其他自訂設定。 |

> [!NOTE]
> 由於使用者設定的動態性質和資料損毀的風險，因此您無法從*json*檔案匯入設定。
>
>

## <a name="next-steps"></a>後續步驟

* [使用角色型存取控制來共用 Azure 儀表板](azure-portal-dashboard-share-access.md)
* [新增、移除和重新排列我的最愛](azure-portal-add-remove-sort-favorites.md)
