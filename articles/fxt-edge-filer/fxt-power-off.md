---
title: 如何關閉 Microsoft Azure FXT Edge 檔案管理工具單元
description: 瞭解使用叢集控制台軟體啟動和安全關閉 Azure FXT Edge Filer 節點的程式。
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 01c34304ac0e3e7faa42611758d77893e149a2f8
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92218726"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>如何安全地關閉 Azure FXT Edge Filer 硬體電源

雖然您可以使用實體電源按鈕來切換個別節點，但您不應該在正常情況下使用它來關閉單位。

使用 Azure FXT Edge Filer 節點作為叢集的一部分之後，您應該使用叢集控制台軟體來關閉硬體。

> [!NOTE]
> 若要避免可能的資料遺失或損毀，請一律使用主控台軟體關閉 Azure FXT Edge Filer。 除非 Microsoft 客戶服務和支援人員指示您這麼做，否則請勿使用實體電源按鈕進行關機。
>
> 在電力緊急中，中斷電源線或使用您的資料中心電力中斷連線機制。

## <a name="shut-down-a-node-from-the-control-panel"></a>從主控台關閉節點

請遵循下列指示，安全地關閉 Azure FXT Edge Filer 節點的電源：

1. 登入叢集主控台。 [開啟 [設定] 頁面中的](fxt-cluster-create.md#open-the-settings-pages) (指示) 
1. 按一下 [**設定**] 索引標籤，然後**載入 [叢集**  >  **FXT 節點**] 頁面。
1. 在叢集節點清單中，尋找您想要關閉的節點。 按一下 [**動作**] 資料行中的 [**關閉電源**] 按鈕。
1. 請稍候幾分鐘。 節點將會關閉並關閉電源。

## <a name="next-steps"></a>後續步驟

* 深入瞭解監視中的狀態 Led 和其他指標 [Azure FXT Edge Filer 硬體狀態](fxt-monitor.md)。
* 深入瞭解 [連接電源線](fxt-network-power.md#connect-power-cables)中 Azure FXT Edge Filer 電源供應器。
