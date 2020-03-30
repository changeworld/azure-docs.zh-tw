---
title: 查看對您重要的 Azure 顧問建議
description: 查看和篩選 Azure 顧問建議以減少噪音。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422362"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>查看對您重要的 Azure 顧問建議

Azure 顧問提供建議，以説明您優化 Azure 部署。 在 Advisor 中，您可以訪問一些功能，這些功能可説明您將建議縮小到僅對您重要的建議。

## <a name="configure-subscriptions-and-resource-groups"></a>配置訂閱和資源組

Advisor 使您能夠選擇對您和您的組織重要的訂閱和資源組。 您只能看到您選擇的訂閱和資源組的建議。 預設情況下，所有選項都已選中。 配置設置應用於訂閱或資源組，因此相同的設置適用于有權訪問該訂閱或資源組的每個人。 可以在 Azure 門戶中更改配置設置，也可以以程式設計方式更改配置設置。

要在 Azure 門戶中進行更改，

1. 在 Azure 門戶中打開[Azure 顧問](https://aka.ms/azureadvisordashboard)。

1. 從功能表中選擇 **"配置**"。

   ![顧問配置功能表](./media/view-recommendations/configuration.png)

1. 選中"**包含**"列中用於接收顧問建議的任何訂閱或資源組核取方塊。 如果禁用該框，您可能無權對該訂閱或資源組進行配置更改。 瞭解有關[Azure 顧問 中許可權](permissions.md)的更多內容。

1. 進行更改後，按一下底部的 **"應用**"。

## <a name="filtering-your-view-in-the-azure-portal"></a>在 Azure 門戶中篩選視圖

配置設置保持活動狀態，直到更改。 如果要限制單個查看的建議視圖，可以使用"顧問"面板頂部提供的下拉清單。 在"概述、高可用性、安全性、性能、成本和所有建議"面板中，您可以選擇要查看的"訂閱、資源類型和建議"狀態。

   ![顧問篩選功能表](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>拒絕和推遲建議

Azure Advisor 允許您關閉或推遲對單個資源的建議。 如果拒絕建議，則除非手動啟動建議，否則不會再次看到它。 但是，推遲建議允許您指定一個持續時間，之後建議將自動再次啟動。 推遲可以在 Azure 門戶中完成，也可以以程式設計方式完成。

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>推遲 Azure 門戶中的單個建議 

1. 在 Azure 門戶中打開[Azure 顧問](https://aka.ms/azureadvisordashboard)。
1. 選擇推薦類別以查看您的建議
1. 從建議清單中選擇建議
1. 選擇"推遲或關閉"以表示要推遲或關閉的建議

     ![顧問篩選功能表](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>推遲或關閉 Azure 門戶中的多個建議

1. 在 Azure 門戶中打開[Azure 顧問](https://aka.ms/azureadvisordashboard)。
1. 選擇推薦類別以查看您的建議。
1. 從建議清單中選擇一個建議。
1. 選擇要推遲或關閉建議的所有資源的行左側核取方塊。
1. 選擇 **"在**表的左上角"推遲或**關閉**。

     ![顧問篩選功能表](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> 您需要參與者或擁有者的許可才能拒絕或推遲建議。 瞭解有關 Azure 顧問中許可權的更多內容。

> [!NOTE]
> 如果禁用選擇框，則建議可能仍在載入。 請等待載入所有建議，然後再嘗試推遲或關閉。

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>重新啟動已推遲或已取消的建議

您可以啟動已推遲或已取消的建議。 此操作可以在 Azure 門戶中完成，也可以以程式設計方式完成。 在 Azure 入口網站中：

1. 在 Azure 門戶中打開[Azure 顧問](https://aka.ms/azureadvisordashboard)。

1. 將"概述"面板上的篩選器更改為 **"已延遲**"。 然後，顧問顯示推遲或已取消的建議。

    ![顧問篩選功能表](./media/view-recommendations/activate-postponed.png)

1. 選擇一個類別以查看 **"已推遲**"和 **"已取消**"建議。

1. 從建議清單中選擇一個建議。 這將打開已選擇 **"延遲&已取消**"選項卡以顯示已推遲或已取消此建議的資源的建議。

1. 按一下行末尾的 **"啟動**"。 按一下後，該資源的建議處於活動狀態，因此從此表中刪除。 該建議現在在 **"活動"** 選項卡中可見。
 
     ![顧問篩選功能表](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>後續步驟

本文介紹如何在 Azure 顧問中查看對您至關重要的建議。 若要深入了解 Advisor，請參閱︰ 

- [何謂 Azure 建議程式？](advisor-overview.md)
- [開始使用顧問](advisor-get-started.md)
- [Azure 顧問中的許可權](permissions.md)



