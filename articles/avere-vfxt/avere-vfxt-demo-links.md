---
title: 適用於 Azure 的 Avere vFXT 示範專案
description: 連結至 Avere vFXT 的程式碼示範教學課程
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: b01bcb743e4ffc110f0182e50630baea288f16d2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "76153695"
---
# <a name="avere-vfxt-demo-projects"></a>Avere vFXT 示範專案

[GitHub](https://github.com/Azure/Avere) 上有提供範例教學課程。 這些小專案示範「適用於 Azure 的 Avere vFXT」的主要功能和使用案例。

## <a name="video-rendering"></a>影片轉譯

* [使用 Azure Batch 和 Avere vFXT](https://github.com/Azure/Avere/blob/master/docs/maya_azure_batch_avere_vfxt_demo.md)的轉譯-60 分鐘專案，示範如何使用 Autodesk Maya 搭配 Azure Batch 和 Avere vFXT 叢集來產生動畫電影

* [為什麼要使用 Avere vFXT 進行轉譯？](https://github.com/Azure/Avere/blob/master/docs/why_avere_for_rendering.md) -此示範會比較網路連接儲存裝置的轉譯時間與不含 Avere vFXT 叢集

## <a name="high-performance-computing"></a>高效能運算

* [改善 Azure 虛擬機器（VM）開機時間的最佳做法](https://github.com/Azure/Avere/blob/master/docs/azure_vm_provision_best_practices.md)-一種可重現的測試，使用 Avere vFXT for Azure 在快速啟動數千個計算用戶端時改善開機時間

## <a name="vfxt-performance"></a>vFXT 效能

* [使用 Vdbench 來測量 vFXT 效能](https://github.com/Azure/Avere/blob/master/docs/vdbench.md) - 這個基本測試會產生小型和中型工作負載，以測試 vFXT 記憶體和磁碟子系統

## <a name="client-setup"></a>用戶端設定

* [適用於 Avere vFXT 的 Windows 10 工作站](https://github.com/Azure/Avere/blob/master/docs/windows_10_avere_vfxt_mounted_workstation.md) - 這會示範如何設定 Windows 工作站，並將它掛接至 Avere vFXT 叢集
