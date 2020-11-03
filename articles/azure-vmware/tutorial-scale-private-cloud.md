---
title: 教學課程 - 縮放私人雲端
description: 在本教學課程中，您會使用 Azure 入口網站來縮放 Azure VMware 解決方案私人雲端。
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: d49d973cc6d97280dc0c7ea6681f2602b871e1ba
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791234"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>教學課程：縮放 Azure VMware 解決方案私人雲端

若要充分運用您的 Azure VMware 解決方案私人雲端體驗，請縮放叢集和主機，以反映已規劃工作負載所需的功能。 您可以依應用程式工作負載需求，擴展私人雲端中的叢集和主機數目。 特定服務的效能和可用性限制應該以案例為基礎來解決。 [私人雲端概念](concepts-private-clouds-clusters.md)文章中提供了叢集和主機限制。

在本教學課程中，您將使用 Azure 入口網站執行下列動作：

> [!div class="checklist"]
> * 將叢集新增至現有的私人雲端
> * 將主機新增至現有叢集

## <a name="prerequisites"></a>Prerequisites

需要私人雲端才能完成本教學課程。 如果您尚未建立私人雲端，請使用 [建立私人雲端教學課程](tutorial-create-private-cloud.md) 建立。 在 Azure 中為 VMware 私人雲端設定網路以設定必要的虛擬網路。

## <a name="add-a-new-cluster"></a>新增叢集

1. 在現有私人雲端概觀頁面的 [管理]  下，選取 [縮放私人雲端]  。 接下來，選取 [+新增叢集]  。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="選取新增叢集" border="true":::

1. 在 [新增叢集]  頁面上，使用滑桿來選取主機的數目。 選取 [儲存]  。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="在 [新增叢集] 頁面上，使用滑桿來選取主機的數目。選取 [儲存]。" border="true":::

   系統將會開始部署新叢集。

## <a name="scale-a-cluster"></a>調整叢集 

1. 在現有私人雲端的概觀頁面上，選取 [縮放私人雲端]  ，然後選取鉛筆圖示來編輯叢集。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="在概觀中選取縮放私人雲端" border="true":::

1. 在 [編輯叢集]  頁面上，使用滑桿來選取主機的數目。 選取 [儲存]  。

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="在 [編輯叢集] 頁面上，使用滑桿來選取主機的數目。選取 [儲存]。" border="true":::

   系統會開始將主機新增至叢集。

## <a name="next-steps"></a>後續步驟

如果您需要另一個 Azure VMware 解決方案私人雲端，請 [建立另一個私人雲端](tutorial-create-private-cloud.md)，並使用相同的網路必要條件、叢集和主機限制等。

<!-- LINKS - external-->

<!-- LINKS - internal -->
