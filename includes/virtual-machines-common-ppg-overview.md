---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: fb2eb2d237a1245627bbdb6f4f2eacbb9966a2c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81422205"
---
將 Vm 放在單一區域中可減少實例之間的實體距離。 將它們放在單一可用性區域中，也會使它們實體在一起。 不過，隨著 Azure 使用量的增加，單一可用性區域可能會跨越多個實體資料中心，這可能會導致網路延遲影響您的應用程式。 

若要盡可能接近 Vm，達到最低可能的延遲，您應該將它們部署在鄰近放置群組內。

鄰近放置群組是一種邏輯群組，用來確保 Azure 計算資源實際位於彼此接近的位置。 鄰近放置群組適用于需要低延遲的工作負載。


- 獨立 Vm 之間的低延遲。
- 單一可用性設定組或虛擬機器擴展集中的 Vm 之間的低延遲。 
- 獨立 Vm、多個可用性設定組中的 Vm，或多個擴展集之間的低延遲。 您可以在單一放置群組中有多個計算資源，將多層式應用程式整合在一起。 
- 使用不同硬體類型的多個應用層之間的低延遲。 例如，在可用性設定組中使用 M 系列來執行後端，以及在擴展集中，于單一鄰近放置群組中執行 D 系列實例的前端。


![鄰近放置群組的圖形](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>使用鄰近放置群組 

近接位置群組是 Azure 中的新資源類型。 您必須先建立一個，才能將它與其他資源搭配使用。 建立之後，即可與虛擬機器、可用性設定組或虛擬機器擴展集搭配使用。 建立提供鄰近放置群組識別碼的計算資源時，您可以指定鄰近放置群組。 

您也可以將現有的資源移至鄰近位置群組。 將資源移到近接位置群組時，您應該先停止（解除配置）資產，因為它可能會重新部署到區域中的不同資料中心，因此可滿足共置條件約束。 

在可用性設定組和虛擬機器擴展集的案例中，您應該在資源層級設定鄰近放置群組，而不是在個別的虛擬機器上。 

鄰近放置群組是共置條件約束，而不是釘選機制。 它會釘選到特定的資料中心，並部署第一個使用它的資源。 一旦使用鄰近放置群組的所有資源都已停止（已取消配置）或已刪除，就不會再釘選。 因此，當您使用具有多個 VM 系列的鄰近放置群組時，請務必在範本中指定所有必要的類型，或遵循部署順序，這樣可改善部署成功的機會。 如果您的部署失敗，請使用失敗的 VM 大小重新開機部署，做為要部署的第一個大小。

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>使用鄰近放置群組時的預期事項 
鄰近放置群組會在相同的資料中心內提供共同位置。 不過，由於鄰近性放置群組代表一個額外的部署條件約束，因此可能會發生配置失敗。 使用近接位置群組時，您可能會看到配置失敗的幾個使用案例：

- 當您要求鄰近放置群組中的第一部虛擬機器時，會自動選取 [資料中心]。 在某些情況下，針對不同的虛擬機器 SKU 的第二個要求可能會失敗（如果該資料中心不存在）。 在此情況下，會傳回**OverconstrainedAllocationRequest**錯誤。 若要避免這種情況，請嘗試變更部署 Sku 的順序，或使用單一 ARM 範本來部署這兩個資源。
-   在彈性工作負載的情況下，您可以在其中新增和移除 VM 實例，在部署上具有鄰近的放置群組條件約束，可能會導致無法滿足要求而導致**AllocationFailure**錯誤。 
- 視需要停止（解除配置）和啟動您的 Vm 是另一種達成彈性的方式。 由於在您停止（解除配置） VM 之後，不會保留容量，因此再次啟動它可能會導致**AllocationFailure**錯誤。


## <a name="best-practices"></a>最佳作法 
- 針對最低延遲，請將鄰近放置群組與加速網路搭配使用。 如需詳細資訊，請參閱[使用加速網路建立 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)或[使用加速網路建立 Windows 虛擬機器](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
- 在單一範本中部署所有 VM 大小。 為了避免登陸不支援您所需之所有 VM Sku 和大小的硬體，請在單一範本中包含所有應用層，使其全部都部署在同一時間。
- 如果您使用 PowerShell、CLI 或 SDK 來撰寫部署腳本，可能會收到配置錯誤`OverconstrainedAllocationRequest`。 在此情況下，您應該停止/解除配置所有現有的 Vm，然後將部署腳本中的順序變更為從失敗的 VM SKU/大小開始。 
- 重複使用已刪除 Vm 的現有放置群組時，請等候刪除作業完整完成，再將 Vm 新增至其中。
- 如果延遲是您的第一個優先順序，請將 Vm 放在鄰近放置群組中，並將整個解決方案放在可用性區域中。 但是，如果復原是您的最高優先順序，請將您的實例分散到多個可用性區域（單一鄰近放置群組不能跨越區域）。
