---
title: 如何關閉 Microsoft Azure FXT Edge 檔案管理工具單位
description: Azure FXT Edge 檔案管理工具節點的啟動和安全關機程式
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 92364de82bc3de8229eced4ee02997a27afbde45
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85506407"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>如何安全地關閉 Azure FXT Edge 檔案管理工具硬體的電源

雖然您可以使用實體電源按鈕來切換個別節點，但您不應該在正常情況下使用它來關閉單位。

使用 Azure FXT Edge 檔案管理工具節點做為叢集的一部分之後，您應該使用「叢集控制台」軟體來關閉硬體。 

> [!NOTE] 
> 若要避免可能的資料遺失或損毀，請一律使用「控制台」軟體來關閉 Azure FXT Edge 檔案管理工具。 除非您已指示 Microsoft 客戶服務及支援人員執行此動作，否則請勿使用 [實體電源] 按鈕進行關機。
> 
> 在電力緊急情況下，中斷電源線的連線，或使用您的資料中心電力中斷連線機制。

## <a name="shut-down-a-node-from-the-control-panel"></a>從 [控制台] 關閉節點

請遵循這些指示來安全地關閉 Azure FXT Edge 檔案管理工具節點：

1. 登入叢集控制台。 （[開啟 [設定] 頁面中的](fxt-cluster-create.md#open-the-settings-pages)指示）
1. 按一下 [**設定**] 索引標籤，然後**載入 [叢集**  >  **FXT 節點**] 頁面。
1. 在叢集節點清單中，找出您想要關閉的節點。 按一下 [**動作**] 資料行中的 [**關閉電源**] 按鈕。 
1. 請稍候片刻。 節點將會關閉，並關閉其電源。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[監視 AZURE FXT Edge 檔案管理器硬體狀態](fxt-monitor.md)中的狀態 led 和其他指示器。
* 深入瞭解[連接電源纜線](fxt-network-power.md#connect-power-cables)中的 Azure FXT Edge 檔案管理工具電源供應器。
