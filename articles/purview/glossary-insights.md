---
title: 使用範疇 Insights 針對您的資料進行詞彙報告
description: 本操作指南說明如何在您的資料上查看和使用範疇 Insights 詞彙報表。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 9f7d81423c75c3e1a51f5b5d5f37c54307488eb3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552029"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Azure 範疇中資料的詞彙見解

本操作指南說明如何存取、查看和篩選資料的範疇詞彙深入解析報表。

在本操作指南中，您將瞭解如何：

> - 從您的範疇帳戶前往見解
> - 取得資料的鳥瞰圖

## <a name="prerequisites"></a>先決條件

開始使用範疇 insights 之前，請確定您已完成下列步驟：

- 設定您的 Azure 資源，並將資料填入帳戶

- 在來源類型上設定和完成掃描

- 設定詞彙並將資產附加至詞彙

如需詳細資訊，請參閱 [管理 Azure 範疇中的資料來源 (預覽) ](manage-data-sources.md)。

## <a name="use-purview-glossary-insights"></a>使用範疇詞彙見解

在 Azure 範疇中，您可以建立詞彙條款，並將其附加至資產。 稍後，您可以在詞彙見解中查看詞彙分佈。 這會根據附加至資產的詞彙來告訴您詞彙的狀態。 它也會依使用者數目的狀態和角色分佈，來告訴您條款。

**若要查看詞彙見解：**

1. 移至 [Azure 入口網站中](https://aka.ms/purviewportal)的 [ **Azure 範疇** 實例] 畫面，然後選取您的範疇帳戶。

1. 在 [ **總覽** ] 頁面的 [ **開始** ] 區段中，選取 [ **啟動範疇** 帳戶] 磚。

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="從 Azure 入口網站啟動範疇":::

1. 在範疇 **首頁** 上，選取 [ **View insights** ] 磚以存取您的 **深入** 解析 :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: 區域。

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="在 Azure 入口網站中查看您的見解":::

1. 在 [ **見解**] :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: 區域中，選取 [ **詞彙** ] 以顯示範疇 **詞彙見解** 報表。

[ **詞彙見解** ] 頁面會顯示下欄區域：
1. 顯示術語和目錄使用者的 **高階 kpi**

2. **頂尖詞彙和資產計數** 會顯示附加資產的五個詞彙詞彙。 所有其他資產都會在圖形中的「其他」類別中。

3. **詞彙條款依詞彙狀態** 會依狀態顯示詞彙的分佈，例如「草稿」、「已核准」、「警示」和「已過期」。 

1. 將滑鼠暫留或按一下具有狀態的圖形配量，並記下具有該狀態的詞彙計數。

1. **依使用者數目的角色散發** 會依範疇中的每個角色的使用者數目顯示角色分佈。

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="查看詞彙見解":::

## <a name="next-steps"></a>後續步驟

深入瞭解透過[資產深入](./asset-insights.md)解析的 Azure 範疇深入解析報表