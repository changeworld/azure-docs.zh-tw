---
title: 適用於 Azure 的 Avere vFXT 示範專案
description: 這些範例顯示 Avere vFXT for Azure 的主要功能和使用案例：影片轉譯、高效能運算、vFXT 效能及用戶端設定。
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 31e32bfc0a2c3279375148bdf3da7d4a4829af1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88271068"
---
# <a name="avere-vfxt-demo-projects"></a>Avere vFXT 示範專案

[GitHub](https://github.com/Azure/Avere) 上有提供範例教學課程。 這些小專案示範「適用於 Azure 的 Avere vFXT」的主要功能和使用案例。

## <a name="video-rendering"></a>影片轉譯

* [使用 Azure Batch 和 Avere vFXT](https://github.com/Azure/Avere/blob/master/docs/maya_azure_batch_avere_vfxt_demo.md) 的60分鐘專案進行轉譯，示範如何使用 Autodesk Maya 搭配 Azure Batch 和 Avere vFXT 叢集來產生動畫電影

* [為什麼要使用 Avere vFXT 進行轉譯？](https://github.com/Azure/Avere/blob/master/docs/why_avere_for_rendering.md) -一種示範，可比較網路連接儲存體的轉譯時間與無 Avere vFXT 叢集

## <a name="high-performance-computing"></a>高效能運算

* [改善 Azure 虛擬機器 (VM) 開機時間的最佳作法](https://github.com/Azure/Avere/blob/master/docs/azure_vm_provision_best_practices.md) -可重現的測試，可在快速啟動數千個計算用戶端時，使用 Avere vFXT for Azure 來改善開機時間

## <a name="vfxt-performance"></a>vFXT 效能

* [使用 Vdbench 來測量 vFXT 效能](https://github.com/Azure/Avere/blob/master/docs/vdbench.md) - 這個基本測試會產生小型和中型工作負載，以測試 vFXT 記憶體和磁碟子系統

## <a name="client-setup"></a>用戶端設定

* [適用於 Avere vFXT 的 Windows 10 工作站](https://github.com/Azure/Avere/blob/master/docs/windows_10_avere_vfxt_mounted_workstation.md) - 這會示範如何設定 Windows 工作站，並將它掛接至 Avere vFXT 叢集
