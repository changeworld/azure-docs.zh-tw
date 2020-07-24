---
title: 如何手動修正 ServiceNow 同步問題
description: 重設 ServiceNow 的連線，讓 Microsoft Azure 中的警示可以再次呼叫 ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a3382f93990612b0ab34eb0848cbf3d6577c44ff
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087929"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>如何手動修正 ServiceNow 同步問題

Azure 監視器可以連接到協力廠商的 IT 服務管理（ITSM）提供者。 ServiceNow 是其中一個提供者。

基於安全性理由，您可能需要重新整理用於與 ServiceNow 連線的驗證權杖。
使用下列同步處理常式重新啟用連線，並重新整理權杖：


1. 在頂端搜尋橫幅中搜尋解決方案，然後選取相關的解決方案

    ![新增連線](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. 在 [方案畫面] 中，選擇訂用帳戶篩選中的 [全選]，然後依 "ServiceDesk" 篩選

    ![新增連線](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. 選取 ITSM 連接的解決方案。
1. 在左邊的橫幅中選取 [ITSM 連線]。

    ![新增連線](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. 從清單中選取每個連接器。 
    1. 按一下連接器名稱以進行設定
    1. 刪除不再使用的任何連接器

    1. 根據您的合作夥伴類型，依據[這些定義](./itsmc-connections.md)更新欄位

    1. 按一下 [同步]

       ![新增連線](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. 按一下 [儲存]

        ![新增連線](media/itsmc-resync-servicenow/save-8bit.png)

f.    查看通知以查看進程是否已順利完成 

## <a name="next-steps"></a>後續步驟

深入瞭解[IT 服務管理](itsmc-connections.md)連線
