---
title: 從入口網站更新現有的指派
description: 瞭解在 Azure 藍圖中從入口網站更新現有藍圖指派的機制。
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: ddaac8110885d817bc96745965c1af7e522b1919
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007183"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>如何更新現有的藍圖指派

指派藍圖時，可以更新指派。 更新現有指派的原因有數個，包括：

- 新增或移除[資源鎖定](../concepts/resource-locking.md)
- 變更[動態參數](../concepts/parameters.md#dynamic-parameters)的值
- 將指派升級至藍圖的較新 **已發佈** 版本

## <a name="updating-assignments"></a>更新指派

1. 在左側窗格中選取 [所有服務]。 搜尋並選取 [藍圖]。

1. 選取頁面左側的 [指派的藍圖]。

1. 在藍圖清單中，選取藍圖指派。 然後使用 [ **更新指派** ] 按鈕，或以滑鼠右鍵按一下藍圖指派，然後選取 [ **更新指派**]。

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="[藍圖指派] 頁面的螢幕擷取畫面，其中反白顯示 [更新指派] 按鈕。" border="false":::

1. [ **指派藍圖** ] 頁面會載入預先填入原始指派的所有值。 您可以變更 **藍圖定義版本**、**鎖定指派** 狀態，以及任何存在於藍圖定義的動態參數。 選取 [完成變更時 **指派** ]。

1. 在更新後的指派詳細資料頁面上，查看新的狀態。 在此範例中，我們對指派新增了 **鎖定**。

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="已更新之藍圖指派的螢幕擷取畫面，顯示鎖定模式已變更。" border="false":::

1. 使用下拉式清單，探索其他 **指派作業** 的詳細資料。 受選指派作業所更新的 **受控資源** 表格。

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="已更新之藍圖指派的螢幕擷取畫面，其中顯示指派作業及其狀態。" border="false":::

## <a name="rules-for-updating-assignments"></a>用於更新指派的規則

所更新指派的部署會遵循幾個重要規則。 這些規則會決定已部署的資源發生什麼事情。 要求的變更以及所要部署或更新的成品資源類型可決定可以採取哪些動作。

- 角色指派
  - 如果角色或角色受託人 (使用者、群組或應用程式) 有所變更，便會建立新的角色指派。 先前部署的角色指派會留在原處。
- 原則指派
  - 如果原則指派參數有所變更，則現有指派也會更新。
  - 如果原則指派定義有所變更，則會建立新的原則指派。
    先前部署的原則指派會留在原處。
  - 如果從藍圖中移除原則指派成品，則部署的原則指派會留在原處。
- Azure Resource Manager 範本 (ARM 範本)
  - 範本會透過 Resource Manager 處理為 **PUT**。 因為每個資源類型對此動作的處理方式不同，請檢閱每個所含資源的文件，以判斷藍圖在執行此動作時會有什麼影響。

## <a name="possible-errors-on-updating-assignments"></a>更新指派時的可能錯誤

在更新指派時，所進行的變更可能會在執行時中斷。 例如，在資源群組部署好之後，變更其位置。 您可以進行 [Resource Manager](../../../azure-resource-manager/management/overview.md) 所支援的任何變更，但任何會透過 Resource Manager 導致錯誤的變更，也會導致指派失敗。

指派的更新次數沒有限制。 如果發生錯誤，請判斷錯誤並對指派進行其他更新。  範例錯誤案例：

- 參數不正確
- 已經存在的物件
- Resource Manager 不支援的變更

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。