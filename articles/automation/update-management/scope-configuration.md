---
title: 限制更新管理部署範圍 Azure 自動化
description: 本文說明如何使用範圍設定來限制更新管理部署的範圍。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 76063c479950d12985d5f3f52393f9bb0d5ecd8d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222045"
---
# <a name="limit-update-management-deployment-scope"></a>限制更新管理部署範圍

本文說明如何使用 [更新管理](overview.md) 功能將更新和修補程式部署至您的 vm，以使用範圍設定。 如需詳細資訊，請參閱 [Azure 監視器 (Preview) 中的目標監視解決方案 ](../../azure-monitor/insights/solution-targeting.md)。

## <a name="about-scope-configurations"></a>關於範圍設定

範圍設定是一或多個已儲存搜尋的群組 (查詢) 用來限制特定電腦更新管理的範圍。 您可以在 Log Analytics 工作區中使用範圍設定，將目標設為要啟用的電腦。 當您新增要從更新管理接收更新的電腦時，也會將電腦新增至工作區中已儲存的搜尋。

## <a name="set-the-scope-limit"></a>設定範圍限制

若要限制更新管理部署的範圍：

1. 在您的自動化帳戶中，選取 [**相關資源**] 底下的 [**連結工作區**]。

2. 選取 [前往工作區]。

3. 選取 [**工作區資料來源**] 下的 [範圍設定] ** (預覽) ** 。

4. 選取範圍設定右邊的省略號  `MicrosoftDefaultScopeConfig-Updates` ，然後選取 [ **編輯**]。

5. 在編輯窗格中，展開 [ **選取電腦群組**]。 電腦群組窗格會顯示用來建立範圍設定的已儲存搜尋。 更新管理所使用的已儲存搜尋是：

    |名稱     |類別  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

6. 選取已儲存的搜尋，以查看並編輯用來填入群組的查詢。 下圖顯示查詢與其結果：

    [![儲存的搜尋](./media/scope-configuration/logsearch.png)](./media/scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>後續步驟

您可以 [查詢 Azure 監視器記錄](query-logs.md) ，以分析更新評估、部署和其他相關的管理工作。 其中包含預先定義的查詢，可協助您開始使用。
