---
title: 調整 Azure 串流分析叢集大小
description: 了解如何擴大和縮減 Azure 串流分析叢集的大小。
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 0763e56de6c72a36d39b17d153db6fc4d7dd821a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943994"
---
# <a name="resize-an-azure-stream-analytics-cluster"></a>調整 Azure 串流分析叢集大小

串流分析叢集的容量是以串流處理單位 (SU) 來測量。 只要指派給所有執行中作業的 SU 總和不超過叢集的容量，多個作業就可以在同一個叢集中平行執行。

叢集的容量可以擴大和縮減，以符合您的串流工作負載大小。 調整叢集需要的時間較長，不建議經常調整。 建議您以需取用的精確 SU 數目來規劃和佈建叢集。

## <a name="change-the-scale-of-your-cluster"></a>變更叢集規模

1. 在 Azure 入口網站中，找出並選取您的串流分析叢集。

1. 在**概觀**區段中，選取 [縮放]。 您可以看到指派給您叢集的 SU 數目。 視需要使用選取器來增加或減少 SU。

   ![縮放叢集](./media/scale-cluster/scale-cluster.png)

縮放作業不會影響目前正在執行的任何作業。

## <a name="next-steps"></a>後續步驟

您現在知道如何擴大和縮減您的串流分析叢集。 接下來，您可以學習如何管理私人端點並自動縮放您的作業：

* [在 Azure 串流分析叢集中管理私人端點](private-endpoints.md)
* [管理 Azure 串流分析叢集中的作業](manage-jobs-cluster.md)
