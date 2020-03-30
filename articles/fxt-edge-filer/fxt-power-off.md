---
title: 如何關閉微軟 Azure FXT 邊緣檔器單元
description: 啟動和安全關閉 Azure FXT 邊緣檔器節點的過程
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72255996"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>如何安全地關閉 Azure FXT 邊緣檔程式硬體

儘管可以使用物理電源按鈕打開單個節點，但在正常情況下，不應使用它關閉設備。

在 Azure FXT 邊緣檔程式節點作為群集的一部分使用後，應使用群集控制台軟體關閉硬體。 

> [!NOTE] 
> 為了避免可能的資料丟失或損壞，請始終使用控制台軟體關閉 Azure FXT 邊緣檔器。 除非 Microsoft 客戶服務和支援部門指示您關閉，否則請勿使用物理電源按鈕關閉。
> 
> 在緊急情況下，斷開電源線或使用資料中心的斷電機制。

## <a name="shut-down-a-node-from-the-control-panel"></a>從控制台關閉節點

按照以下說明安全地關閉 Azure FXT 邊緣檔器節點：

1. 登錄到群集控制台。 （[打開"設置"頁時](fxt-cluster-create.md#open-the-settings-pages)的說明）
1. 按一下 **"設置"** 選項卡，然後載入**群集** > **FXT 節點**頁面。
1. 在叢集節點清單中，找到要關閉的節點。 按一下其 **"操作"** 列中的 **"斷電**"按鈕。 
1. 等一下 節點將關閉並自行關閉電源。

## <a name="next-steps"></a>後續步驟

* 瞭解[監視器 Azure FXT 邊緣檔程式硬體狀態](fxt-monitor.md)中的狀態 LED 和其他指標。
* 閱讀有關[連接電源線](fxt-network-power.md#connect-power-cables)中的 Azure FXT 邊緣檔器電源的更多內容。
