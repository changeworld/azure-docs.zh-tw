---
title: 教學課程：縮放私人雲端
description: 在本教學課程中，您會使用 Azure 入口網站來縮放 Azure VMware 解決方案 (AVS) 預覽私人雲端。
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 70d8c1c555badd6102f4b2547492bdea54e55783
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739723"
---
# <a name="tutorial-scale-an-azure-vmware-solution-avs-preview-private-cloud"></a>教學課程：縮放 Azure VMware 解決方案 (AVS) 預覽私人雲端

若要充分運用您的 AVS 預覽私人雲端體驗，請縮放叢集和主機，以反映已規劃工作負載所需的功能。 因為 AVS 不會在預覽期間支援您的內部部署 vCenter，所以必須使用已透過 Azure 入口網站建立的項目進行。

您可以依應用程式工作負載需求，縮放私人雲端中的叢集數目和主機數目。 在您的 AVS 預覽雲端環境中，特定服務的效能和可用性限制必須以案例為基礎來解決。 [私人雲端概念文章](concepts-private-clouds-clusters.md)中提供了私人雲端中的叢集和主機限制。

在本教學課程中，您會使用 Azure 入口網站來：

> [!div class="checklist"]
> * 將叢集新增至現有的私人雲端
> * 將主機新增至現有叢集

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要私人雲端。 如果您尚未建立私人雲端，請使用[建立私人雲端教學課程](tutorial-create-private-cloud.md)建立私人雲端，並在 Azure 中為您的 VMware 私人雲端設定網路功能，以設定所需的虛擬網路。

## <a name="add-a-new-cluster"></a>新增叢集

在現有私人雲端概觀頁面的 [管理]  下，選取 [縮放私人雲端]  。 接下來，選取 [+新增叢集]  。

:::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="選取新增叢集" border="true":::

在 [新增叢集]  頁面上，使用滑桿來選取主機的數目。 選取 [儲存]  。

:::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="設定新的私人雲端叢集" border="true":::

系統將會開始部署新叢集。

## <a name="scale-a-cluster"></a>調整叢集 

在現有私人雲端的概觀頁面上，選取 [縮放私人雲端]  ，然後選取鉛筆圖示來編輯叢集。

:::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="在概觀中選取縮放私人雲端" border="true":::

在 [編輯叢集]  頁面上，使用滑桿來選取主機的數目。 選取 [儲存]  。

:::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="設定新的私人雲端叢集" border="true":::

系統會開始將主機新增至叢集。

## <a name="next-steps"></a>後續步驟

如果您需要另一個 AVS 私人雲端，請[建立另一個私人雲端](tutorial-create-private-cloud.md)，並使用相同的網路必要條件、叢集和主機限制等。

<!-- LINKS - external-->

<!-- LINKS - internal -->
