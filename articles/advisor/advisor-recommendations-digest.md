---
title: Azure 顧問的建議摘要
description: 獲取活動建議的定期摘要
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502460"
---
# <a name="configure-periodic-summary-for-recommendations"></a>為建議配置定期摘要

顧問**建議摘要**提供了一種簡單而主動的方式，可以跨不同類別查看您的主動建議。 此功能提供了為跨不同類別的所有活動建議摘要配置定期通知的功能。 您可以使用操作組為電子郵件、短信或其他通知選擇所需的頻道。 本文介紹如何為顧問建議設置**建議摘要**。


## <a name="setting-up-your-recommendation-digest"></a>設置您的推薦摘要 

**建議摘要**創建體驗可説明您配置摘要。 您可以為配置選擇以下參數：
1. 類別：我們有推薦類別，如成本、高可用性、性能和卓越運營。 此功能尚不適用於安全建議。
2. 摘要頻率：摘要通知的頻率可以是每週、每兩周和每月。
3. 操作組：您可以選擇現有操作組或創建新操作組。 要瞭解有關操作組的更多詳細資訊，請參閱[創建和管理操作組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。
4. 摘要語言
5. 建議摘要名稱：您可以使用方便使用的字串更好地跟蹤和監視摘要。

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>在 Azure 門戶中創建建議摘要的步驟

以下是創建**建議摘要的步驟：**
* **第 1 步：** 在 Azure 門戶中，轉到 **"顧問"，** 在 **"監視"** 部分下，選擇 **"建議摘要"** 

   ![建議摘要進入點](./media/digest-0.png)

* **第 2 步：** 從頂部欄中選擇 **"新建建議摘要**"，如下所示：

   ![創建建議摘要](./media/digest-5.png)

* **第 3 步：** 在 **"範圍"** 部分中，選擇摘要的**訂閱**

   ![提供建議摘要輸入](./media/digest-1.png)

* **第 4 步：** 在**條件**部分中，選擇**類別**、**頻率**和**語言**等配置

   ![提供建議摘要輸入條件](./media/digest-2.png)

* **第 5 步：** 在**操作組**部分中，選擇摘要**的操作組**。 您可以在此處瞭解更多資訊 -[創建和管理操作組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

   ![提供建議摘要輸入操作組](./media/digest-3.png)

* **第 6 步：** 在摘要**詳細資訊**的最後一節中，您可以將名稱和狀態分配給建議摘要。 按**創建建議摘要**以完成設置。
   ![完整的建議摘要創建](./media/digest-4.png)

## <a name="next-steps"></a>後續步驟

如需 Advisor 建議的詳細資訊，請參閱：
* [Azure 建議程式簡介](advisor-overview.md)
* [開始使用 Advisor](advisor-get-started.md)
* [Advisor 成本建議](advisor-cost-recommendations.md)
* [建議程式效能建議](advisor-performance-recommendations.md)
* [Advisor 安全性建議](advisor-security-recommendations.md)
* [顧問卓越運營建議](advisor-operational-excellence-recommendations.md)
* [顧問 REST API](https://docs.microsoft.com/rest/api/advisor/)
