---
title: 觀看 Azure Advisor 的重要建議
description: 查看和篩選 Azure Advisor 建議以減少雜訊。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75422362"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>觀看 Azure Advisor 的重要建議

Azure Advisor 提供可協助您優化 Azure 部署的建議。 在 Advisor 中，您可以存取一些功能，協助您將建議範圍縮小至僅限於您所需的內容。

## <a name="configure-subscriptions-and-resource-groups"></a>設定訂用帳戶和資源群組

Advisor 可讓您選取與您和您的組織相關的訂用帳戶和資源群組。 您只會看到您所選取的訂用帳戶和資源群組的建議。 預設會選取 [全部]。 設定會套用至訂用帳戶或資源群組，因此相同的設定會套用至可存取該訂用帳戶或資源群組的每個人。 您可以在 Azure 入口網站中或以程式設計方式變更設定。

若要在 Azure 入口網站中進行變更：

1. 在 Azure 入口網站中開啟[Azure Advisor](https://aka.ms/azureadvisordashboard) 。

1. 從功能表**中選取 [** 設定]。

   ![Advisor 設定功能表](./media/view-recommendations/configuration.png)

1. 核取任何訂用帳戶或資源群組的 [**包含**] 資料行中的方塊，以接收 Advisor 建議。 如果此方塊已停用，表示您可能沒有在該訂用帳戶或資源群組上進行設定變更的許可權。 深入瞭解[Azure Advisor 中的許可權](permissions.md)。

1. 進行變更**之後，請按一下底部**的 [套用]。

## <a name="filtering-your-view-in-the-azure-portal"></a>在 Azure 入口網站中篩選您的視圖

設定會保持作用中狀態，直到變更為止。 如果您想要限制單一觀看的建議，您可以使用 Advisor 面板頂端提供的下拉式清單。 在 [總覽]、[高可用性]、[安全性]、[效能]、[成本] 和 [所有建議] 面板中，您可以選取您想要查看的訂用帳戶、資源類型和建議狀態。

   ![Advisor 篩選功能表](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>關閉和延遲建議

Azure Advisor 可讓您在單一資源上關閉或延遲建議。 如果您關閉建議，除非手動啟動，否則不會再次顯示。 不過，延後建議可讓您指定持續時間，之後會再次自動啟用建議。 延遲可以在 Azure 入口網站或以程式設計方式完成。

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>延遲 Azure 入口網站中的單一建議 

1. 在 Azure 入口網站中開啟[Azure Advisor](https://aka.ms/azureadvisordashboard) 。
1. 選取建議類別以查看您的建議
1. 從建議清單中選取建議
1. 針對您想要延期或關閉的建議選取 [延遲] 或 [關閉]

     ![Advisor 篩選功能表](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>延遲或關閉 Azure 入口網站中的多個建議

1. 在 Azure 入口網站中開啟[Azure Advisor](https://aka.ms/azureadvisordashboard) 。
1. 選取建議類別以查看您的建議。
1. 從建議清單中選取建議。
1. 針對您想要延期或關閉建議的所有資源，選取資料列左邊的核取方塊。
1. 選取資料表左上方的 [**延期**] 或 [**關閉**]。

     ![Advisor 篩選功能表](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> 您需要「參與者」或「擁有者」許可權，才能解除或延遲建議。 深入瞭解 Azure Advisor 中的許可權。

> [!NOTE]
> 如果選取方塊已停用，建議可能仍在載入中。 請等候所有建議載入後，再嘗試延期或關閉。

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>重新啟用已延後或已關閉的建議

您可以啟用已延後或已關閉的建議。 這個動作可以在 Azure 入口網站或以程式設計方式完成。 在 Azure 入口網站中：

1. 在 Azure 入口網站中開啟[Azure Advisor](https://aka.ms/azureadvisordashboard) 。

1. 將 [總覽] 面板上的篩選變更為 [已**延**後]。 Advisor 接著會顯示已延遲或已關閉的建議。

    ![Advisor 篩選功能表](./media/view-recommendations/activate-postponed.png)

1. 選取類別以查看**延遲**和**關閉**的建議。

1. 從建議清單中選取建議。 這會開啟建議，其中已選取 [延後的 **& 已關閉**] 索引標籤，以顯示已延後或關閉此建議的資源。

1. 按一下資料列結尾的 [**啟用**]。 一旦按下，該資源的建議就會生效，因此從這個資料表中移除。 建議現在會顯示在 [使用**中] 索引**標籤中。
 
     ![Advisor 篩選功能表](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>後續步驟

本文說明如何在 Azure Advisor 中，查看對您很重要的建議。 若要深入了解 Advisor，請參閱︰ 

- [何謂 Azure 建議程式？](advisor-overview.md)
- [使用 Advisor 消費者入門](advisor-get-started.md)
- [Azure Advisor 中的許可權](permissions.md)



