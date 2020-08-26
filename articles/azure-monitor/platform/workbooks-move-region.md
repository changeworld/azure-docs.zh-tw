---
title: Azure 監視器活頁簿-移動區域
description: 如何將活頁簿移至不同的區域
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d3b8bfbef544e754f684421daa847f1724435d53
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875618"
---
# <a name="move-an-azure-workbook-to-another-region"></a>將 Azure 活頁簿移至另一個區域

本文說明如何將 Azure 活頁簿資源移至不同的 Azure 區域。 您可能會因為許多原因而將資源移至另一個區域。 例如，若要利用新的 Azure 區域，只部署特定區域中提供的功能或服務，以符合內部原則和治理需求，或為了回應容量規劃需求。

## <a name="prerequisites"></a>先決條件

* 確定目的地區域中支援活頁簿。

* 這些指示適用于共用活頁簿 (`microsoft.insights/workbooks`) 和私人活頁簿 (`microsoft.insights/myworkbooks`) 儲存在 Azure 監視器和大部分的資源類型上。

  不過，針對特別連結至 Application Insights 資源類型的活頁簿，這些活頁簿會儲存在儲存 Application Insights 資源的 Azure 區域中。

  這些活頁簿無法個別移至另一個區域。

## <a name="move"></a>移動

移動 Azure 活頁簿最簡單的方式，就是在入口網站 UI 的活頁簿工具中使用 [另存新檔]：

1. 在活頁簿檢視器中開啟目標活頁簿。
2. 使用 [編輯] 工具列按鈕進入編輯模式。
3. 使用 [另存新檔] 工具列按鈕。
4. 在 [儲存表單] 中，選擇活頁簿的名稱和所需的區域。 確定訂用帳戶、資源群組和共用的其他欄位都適用。

   > [!NOTE]
   > 您可能需要使用新的活頁簿名稱，以避免任何重複的名稱。

5. 儲存。 

## <a name="verify"></a>Verify

使用 Azure 活頁簿流覽 UI 來尋找新的活頁簿。 確定位置是目標位置。

## <a name="clean-up"></a>清理

在新區域中建立活頁簿之後，請刪除先前區域中的原始活頁簿。
1. 在活頁簿檢視器中開啟原始活頁簿。
2. 使用 [編輯] 工具列按鈕進入編輯模式。
3. 從 [編輯工具] 下拉式清單中 (鉛筆圖示) ，選擇 [刪除活頁簿]。

如果您重新命名活頁簿以將其匯入新的區域，您可以在使用編輯模式工具列的 [編輯工具] 下拉式清單 [重新命名] 專案刪除原始活頁簿之後，將活頁簿重新命名為先前的名稱。

## <a name="next-steps"></a>後續步驟

需要移動活頁簿範本，而不是活頁簿嗎？ 瞭解如何 [將 Azure 活頁簿範本移至另一個區域](./workbook-templates-move-region.md)。

瞭解如何透過 ARM 範本 [部署活頁簿和活頁簿範本](./workbooks-automate.md) 。
