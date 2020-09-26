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
ms.openlocfilehash: b5827d60b5968eb9f5e9e0a2ca5ec884366aea3d
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376675"
---
將 Vm 放入單一區域可減少實例之間的實體距離。 將它們放在單一可用性區域內，也會讓它們彼此緊密地結合在一起。 不過，當 Azure 使用量增加時，單一可用性區域可能橫跨多個實體資料中心，這可能會導致網路延遲影響您的應用程式。 

若要盡可能關閉 Vm，請達到最低可能的延遲，您應該將它們部署在鄰近放置群組內。

鄰近放置群組是邏輯群組，可用來確保 Azure 計算資源實際位於彼此接近的位置。 鄰近放置群組很適合用於具備低延遲需求的工作負載。


- 獨立 Vm 之間的低延遲。
- 單一可用性設定組或虛擬機器擴展集中的 Vm 之間的低延遲。 
- 獨立 Vm、多個可用性設定組中的 Vm 或多個擴展集之間的低延遲。 您可以在單一放置群組中有多個計算資源，以將多層式應用程式整合在一起。 
- 使用不同硬體類型的多個應用層之間的低延遲。 例如，在可用性設定組中使用 M 系列，以及在擴展集中的一個鄰近放置群組中的 D 系列實例前端執行後端。


![鄰近位置群組的圖形](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>使用鄰近位置群組 

鄰近位置群組是 Azure 中的新資源類型。 您需要先建立一個，才能將它與其他資源搭配使用。 一旦建立之後，就可以搭配虛擬機器、可用性設定組或虛擬機器擴展集使用。 您可以在建立提供鄰近放置群組識別碼的計算資源時，指定鄰近放置群組。 

您也可以將現有的資源移至鄰近放置群組。 將資源移至鄰近放置群組時，您應該先停止 (解除配置資產) ，因為它會重新部署到區域中不同的資料中心，以滿足共置條件約束。 

在可用性設定組和虛擬機器擴展集的情況下，您應該在資源層級（而不是個別的虛擬機器）設定鄰近放置群組。 

鄰近位置群組是共置條件約束，而不是釘選機制。 它會釘選到特定的資料中心，並部署第一個使用它的資源。 當使用鄰近放置群組的所有資源都已停止 (取消配置) 或刪除時，就不會再釘選。 因此，當您使用具有多個 VM 系列的鄰近放置群組時，請務必盡可能在範本中指定所有必要的型別，或遵循部署順序，這樣可改善部署成功的機會。 如果您的部署失敗，請使用失敗的 VM 大小重新開機部署，作為第一個要部署的大小。

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>使用相近放置群組時的預期 
鄰近位置群組會在相同的資料中心內提供共置。 不過，由於鄰近位置群組代表額外的部署條件約束，因此可能會發生配置失敗。 使用相近放置群組時，您可能會發現配置失敗的使用案例如下：

- 當您要求鄰近放置群組中的第一部虛擬機器時，系統會自動選取資料中心。 在某些情況下，對於不同的虛擬機器 SKU 的第二個要求，如果該資料中心不存在，可能會失敗。 在此情況下，會傳回 **OverconstrainedAllocationRequest** 錯誤。 若要避免這種情況，請嘗試變更您部署 Sku 的順序，或使用單一 ARM 範本部署這兩個資源。
-   如果是彈性工作負載，您可以在其中新增和移除 VM 實例，在您的部署上具有鄰近放置群組條件約束，可能會導致無法滿足要求，導致 **AllocationFailure** 錯誤。 
- 您可以視需要停止 (解除配置) 和啟動您的 Vm，是另一種達成彈性的方式。 因為當您停止 (將 VM 解除配置) VM 時，不會保留容量，因此再次啟動它可能會導致 **AllocationFailure** 錯誤。

## <a name="planned-maintenance-and-proximity-placement-groups"></a>預定維修和鄰近放置群組

規劃的維護事件（例如 Azure 資料中心的硬體解除委任）可能會影響鄰近放置群組中的資源對齊。 資源可能會移至不同的資料中心，因而中斷與鄰近放置群組相關聯的共置和延遲期望。

### <a name="check-the-alignment-status"></a>檢查對齊狀態

您可以執行下列動作來檢查鄰近放置群組的對齊狀態。


- 您可以使用入口網站、CLI 和 PowerShell 來查看鄰近放置群組共置狀態。

    -   使用 PowerShell 時，您可以使用 AzProximityPlacementGroup Cmdlet 來取得共置狀態，方法是包含選擇性參數 '-ColocationStatus '。

    -   使用 CLI 時，可透過 `az ppg show` 包含選擇性參數 '--include-共置-status ' 來取得共置狀態。

- 針對每個鄰近放置群組， **共置狀態** 屬性會提供群組資源的目前對齊狀態摘要。 

    - **對齊**：資源位於鄰近放置群組的相同延遲波封中。

    - **未知**：至少有一個 VM 資源解除配置。 一旦成功啟動它們之後，狀態應該會返回 [已 **對齊**]。

    - **未對齊**：至少有一個 VM 資源未對齊鄰近放置群組。 未對齊的特定資源也會在成員資格區段中分開呼叫

- 針對可用性設定組，您可以在 [可用性設定組] 總覽頁面中查看個別 Vm 對齊的相關資訊。

- 針對擴展集，您可以在擴展集的 [**總覽**] 頁面的 [**實例**] 索引標籤中，看到個別實例對齊的相關資訊。 


### <a name="re-align-resources"></a>重新調整資源 

如果鄰近放置群組為 `Not Aligned` ，您可以 stop\deallocate 並重新啟動受影響的資源。 如果 VM 位於可用性設定組或擴展集中，則必須先 stopped\deallocated 可用性設定組或擴展集中的所有 Vm，才能重新開機。

如果因為部署限制而發生配置失敗，您可能必須 stop\deallocate 受影響的鄰近放置群組中的所有資源 (包括) 的對齊資源，然後重新開機它們以還原對齊。

## <a name="best-practices"></a>最佳作法 
- 針對最低延遲，請將鄰近位置群組與加速網路搭配使用。 如需詳細資訊，請參閱 [使用加速網路建立 Linux 虛擬機器](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ，或 [建立具有加速網路功能的 Windows 虛擬](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)機。
- 在單一範本中部署所有 VM 大小。 為了避免登入不支援所有 VM Sku 和您所需大小的硬體，請在單一範本中包含所有應用層，如此一來，就會同時部署它們。
- 如果您要使用 PowerShell、CLI 或 SDK 編寫部署腳本，您可能會收到配置錯誤 `OverconstrainedAllocationRequest` 。 在此情況下，您應該停止/解除配置所有現有的 Vm，並變更部署腳本中的順序，以開始使用失敗的 VM SKU/大小。 
- 重複使用已刪除 Vm 的現有放置群組時，請等候刪除完成，再將 Vm 新增至該群組。
- 如果延遲是您的第一個優先順序，請將 Vm 放在鄰近放置群組，並將整個解決方案放在可用性區域中。 但是，如果復原是您的最高優先順序，請將您的實例分散到多個可用性區域， (單一鄰近放置群組無法跨越區域) 。
