---
title: 觀看 Azure Advisor 您對您有影響的建議
description: 查看並篩選 Azure Advisor 的建議，以減少雜訊。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 43bdaba7d774bba8857a6eb3ef296d7ab8e1c264
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986866"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>觀看 Azure Advisor 您對您有影響的建議

Azure Advisor 提供可協助您優化 Azure 部署的建議。 在 Advisor 內，您可以存取幾個功能，以協助您縮小建議範圍，使其僅限於您的建議。

## <a name="configure-subscriptions-and-resource-groups"></a>設定訂用帳戶和資源群組

Advisor 可讓您選取與您和您的組織相關的訂用帳戶和資源群組。 您只會看到您所選訂用帳戶和資源群組的建議。 預設會選取 [全部]。 設定會套用至訂用帳戶或資源群組，因此相同的設定會套用至有權存取該訂用帳戶或資源群組的每個人。 您可以在 Azure 入口網站或以程式設計方式變更設定。

若要在 Azure 入口網站中進行變更：

1. 開啟 Azure 入口網站中的 [Azure Advisor](https://aka.ms/azureadvisordashboard) 。

1. 從 **功能表** 選取 [設定]。

   ![Advisor 設定功能表](./media/view-recommendations/configuration.png)

1. 核取 [ **包含** ] 資料行中任何訂用帳戶或資源群組的方塊，以接收 Advisor 建議。 如果此方塊已停用，表示您可能沒有在該訂用帳戶或資源群組上進行設定變更的許可權。 深入瞭解 [Azure Advisor 中的許可權](permissions.md)。

1. 在您進行變更之後 **，按一下底部** 的 [套用]。

## <a name="filtering-your-view-in-the-azure-portal"></a>在 Azure 入口網站中篩選您的視圖

設定會保持作用中狀態，直到變更為止。 如果您想要限制單一觀看的建議視圖，您可以使用 [Advisor] 面板頂端提供的下拉式清單。 從 [總覽]、[高可用性]、[安全性]、[效能]、[成本] 和 [所有建議] 面板中，您可以選取想要查看的訂用帳戶、資源類型和建議狀態。

   :::image type="content" source="./media/view-recommendations/filtering.png" alt-text="顯示篩選選項 Azure Advisor 的螢幕擷取畫面。":::

## <a name="dismissing-and-postponing-recommendations"></a>關閉和延後建議

Azure Advisor 可讓您在單一資源上關閉或延遲建議。 如果您關閉建議，除非手動啟動，否則不會再次顯示。 不過，延後建議可讓您指定持續時間，之後會自動啟用建議。 延後可以在 Azure 入口網站或以程式設計方式完成。

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>延遲 Azure 入口網站中的單一建議 

1. 開啟 Azure 入口網站中的 [Azure Advisor](https://aka.ms/azureadvisordashboard) 。
1. 選取建議類別以查看您的建議
1. 從建議清單中選取建議
1. 針對您想要延期或關閉的建議選取 [延遲] 或 [關閉]

     :::image type="content" source="./media/view-recommendations/postpone-dismiss.png" alt-text="顯示篩選選項 Azure Advisor 的螢幕擷取畫面。":::

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>延遲或關閉 Azure 入口網站中的多個建議

1. 開啟 Azure 入口網站中的 [Azure Advisor](https://aka.ms/azureadvisordashboard) 。
1. 選取建議類別以查看您的建議。
1. 從建議清單中選取建議。
1. 針對您想要延期或關閉建議的所有資源，選取資料列左側的核取方塊。
1. 選取資料表左上角的 [ **延遲** ] 或 [ **關閉** ]。

     :::image type="content" source="./media/view-recommendations/postpone-dismiss-multiple.png" alt-text="顯示篩選選項 Azure Advisor 的螢幕擷取畫面。":::

> [!NOTE]
> 您需要參與者或擁有者許可權，才能關閉或延期建議。 深入瞭解 Azure Advisor 中的許可權。

> [!NOTE]
> 如果選取方塊已停用，可能仍在載入建議。 請等候所有建議載入，然後再嘗試延期或關閉。

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>重新啟用已延遲或已關閉的建議

您可以啟用已延後或已關閉的建議。 您可以在 Azure 入口網站或以程式設計方式完成此動作。 在 Azure 入口網站中：

1. 開啟 Azure 入口網站中的 [Azure Advisor](https://aka.ms/azureadvisordashboard) 。

1. 將 [總覽] 面板上的篩選準則變更為 [已 **延**後]。 Advisor 接著會顯示已延遲或已關閉的建議。

    :::image type="content" source="./media/view-recommendations/activate-postponed.png" alt-text="顯示篩選選項 Azure Advisor 的螢幕擷取畫面。":::

1. 選取類別以查看 **延** 後和已 **關閉** 的建議。

1. 從建議清單中選取建議。 這會開啟已選取 [延後的 **& 已關閉** ] 索引標籤的建議，以顯示已延後或關閉此建議的資源。

1. 按一下資料列結尾的 [ **啟用** ]。 按一下之後，該資源的建議會在作用中，因此會從此資料表中移除。 您現在 **可以在 [使用中] 索引** 標籤中看到建議。
 
     :::image type="content" source="./media/view-recommendations/activate-postponed-2.png" alt-text="顯示篩選選項 Azure Advisor 的螢幕擷取畫面。":::

## <a name="next-steps"></a>後續步驟

本文說明如何在 Azure Advisor 中查看對您有影響的建議。 若要深入了解 Advisor，請參閱︰ 

- [何謂 Azure 建議程式？](advisor-overview.md)
- [使用 Advisor 消費者入門](advisor-get-started.md)
- [Azure Advisor 中的許可權](permissions.md)



