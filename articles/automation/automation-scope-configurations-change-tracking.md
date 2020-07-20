---
title: 限制 Azure 自動化變更追蹤和清查部署範圍
description: 本文說明如何使用範圍設定來限制變更追蹤和清查部署的範圍。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 353f29d9b4c6599226619d40e4378e21618bcad4
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185903"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>限制變更追蹤和清查部署範圍

本文說明如何在使用[變更追蹤和清查](change-tracking.md)功能將變更部署至您的 vm 時，使用範圍設定。 如需詳細資訊，請參閱[Azure 監視器 (Preview) 中的目標監視解決方案](../azure-monitor/insights/solution-targeting.md)。 

## <a name="about-scope-configurations"></a>關於範圍設定

範圍設定是一或多個已儲存搜尋的群組 (查詢) 用來將變更追蹤和清查範圍限制在特定電腦上。 在 Log Analytics 工作區中使用範圍設定，將目標設為要啟用的電腦。 當您從功能將電腦新增至變更時，電腦也會新增至工作區中已儲存的搜尋。

## <a name="set-the-scope-limit"></a>設定範圍限制

若要限制變更追蹤和清查部署的範圍：

1. 在您的自動化帳戶中，選取 [**相關資源**] 底下的 [**連結工作區**]。

2. 按一下 [**移至工作區**]。

3. 選取 [**工作區] [資料來源**] 下的 [範圍設定] ** (預覽) ** 。

4. 選取範圍設定右邊的省略號 `MicrosoftDefaultScopeConfig-ChangeTracking` ，然後按一下 [**編輯**]。 

5. 在編輯窗格中，選取 [選取電腦群組]。 電腦群組窗格會顯示用來建立範圍設定的已儲存搜尋。 變更追蹤和清查所使用的已儲存搜尋是：

    |名稱     |類別  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

6. 選取已儲存的搜尋，以查看並編輯用來填入群組的查詢。 下圖顯示查詢與其結果：

    ![已儲存的搜尋](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>後續步驟

* 若要使用變更追蹤和清查，請參閱[管理變更追蹤和清查](change-tracking-file-contents.md)。
* 若要針對一般功能問題進行疑難排解，請參閱[針對變更追蹤和清查問題進行疑難排解](troubleshoot/change-tracking.md)。
