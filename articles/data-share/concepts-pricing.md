---
title: 瞭解 Azure 資料共用定價
description: 瞭解 Azure 資料共用定價的運作方式
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136812"
---
# <a name="understand-azure-data-share-pricing"></a>瞭解 Azure 資料共用定價

針對以快照集為基礎的共用，Azure 資料共用會收取資料集快照和快照執行的費用。 這篇文章說明如何使用快照記錄資訊來預估資料集快照和快照集的執行。 目前，資料提供者會針對資料集快照和快照執行計費。

## <a name="dataset-snapshot"></a>Dataset 快照集

資料集快照是將資料集從其來源移至目的地的作業。 為共用建立快照集時，共用中每個資料集的快照集都是資料集快照集作業。 請遵循下列步驟來查看資料集快照集的清單。 

1. 在 Azure 入口網站中，流覽至您的資料共用資源。

1. 從 [已傳送的共用] 或 [已接收的共用] 選取共用。

1. 按一下 [歷程**記錄**] 索引標籤。

1. 按一下快照集的 [開始時間]。
 
    ![快照集歷程記錄](./media/concepts/concepts-pricing/pricing-snapshot-history.png "快照集歷程記錄") 

1. 查看資料集快照集作業的清單。 每個明細專案都會對應到資料集快照集作業。 在此範例中，有兩個資料集快照集。

    ![Dataset 快照集作業](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "Dataset 快照集作業")

## <a name="snapshot-execution"></a>快照集執行

快照執行包含將資料集從來源移動到目的地所需的資源。 針對每個資料集快照集作業，快照集執行的計算方式為虛擬核心數乘以快照集的持續時間。 費用會依分鐘數按比例計算，並無條件進位。 根據來源-目標群組和資料模式，動態選取 vCore 的數目。 請遵循下列步驟來查看快照集的開始時間、結束時間，以及用於資料集快照集作業的虛擬核心。

1. 在 Azure 入口網站中，流覽至您的資料共用資源。

1. 從 [已傳送的共用] 或 [已接收的共用] 選取共用。

1. 按一下 [歷程**記錄**] 索引標籤。

1. 按一下快照集的 [開始時間]。

    ![快照集歷程記錄](./media/concepts/concepts-pricing/pricing-snapshot-history.png "快照集歷程記錄") 

1. 按一下資料集快照集作業的 [狀態]。

    ![資料集快照狀態](./media/concepts/concepts-pricing/pricing-snapshot-status.png "資料集快照狀態")

1. 查看此資料集快照作業所使用的開始時間、結束時間和虛擬核心。 

    ![快照集執行](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "快照集執行")

## <a name="next-steps"></a>後續步驟

- 取得最新的定價資訊- [Azure 資料共用定價](https://azure.microsoft.com/pricing/details/data-share/)
- 使用 Azure 定價計算機來預估成本- [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/)
