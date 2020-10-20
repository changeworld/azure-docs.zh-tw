---
title: 限制 Azure 自動化變更追蹤和清查部署範圍
description: 本文說明如何使用範圍設定來限制變更追蹤和清查部署的範圍。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209514"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>限制變更追蹤和清查部署範圍

本文說明如何使用 [變更追蹤和清查](overview.md) 功能將變更部署至您的 vm，以使用範圍設定。 如需詳細資訊，請參閱 [Azure 監視器 (Preview) 中的目標監視解決方案 ](../../azure-monitor/insights/solution-targeting.md)。

## <a name="about-scope-configurations"></a>關於範圍設定

範圍設定是一或多個已儲存搜尋的群組 (查詢) 用來將變更追蹤和清查的範圍限制為特定電腦。 您可以在 Log Analytics 工作區中使用範圍設定，將目標設為要啟用的電腦。 當您將電腦新增至功能的變更時，也會將電腦新增至工作區中已儲存的搜尋。

## <a name="set-the-scope-limit"></a>設定範圍限制

若要限制變更追蹤和清查部署的範圍：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在 Azure 入口網站中，選取 [所有服務]。 在資源清單中輸入**自動化**。 當您開始輸入時，清單會根據您的輸入來篩選建議。 選取 [自動化帳戶] ****。

3. 在自動化帳戶的清單中，選取您啟用變更追蹤和清查時所選擇的帳戶。

4. 在您的自動化帳戶中，選取 [**相關資源**] 底下的 [**連結工作區**]。

5. 按一下 [ **移至工作區**]。

6. 選取 [**工作區資料來源**] 下的 [範圍設定] ** (預覽) ** 。

7. 選取範圍設定右邊的省略號  `MicrosoftDefaultScopeConfig-ChangeTracking` ，然後按一下 [ **編輯**]。

8. 在編輯窗格中，選取 [選取電腦群組]。 電腦群組窗格會顯示用來建立範圍設定的已儲存搜尋。 變更追蹤和清查所使用的已儲存搜尋為：

    |名稱     |類別  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. 選取已儲存的搜尋，以查看並編輯用來填入群組的查詢。 下圖顯示查詢與其結果：

    ![已儲存的搜尋](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>後續步驟

* 若要使用變更追蹤和清查，請參閱[管理變更追蹤和清查](manage-change-tracking.md)。
* 若要針對一般功能問題進行疑難排解，請參閱[針對變更追蹤和清查問題進行疑難排解](../troubleshoot/change-tracking.md)。
