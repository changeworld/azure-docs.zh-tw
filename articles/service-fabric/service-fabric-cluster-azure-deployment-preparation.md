---
title: 規劃 Azure 服務結構群集部署
description: 瞭解如何規劃和準備生產服務結構群集部署到 Azure。
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 1762a6975448301957579b3437a8af5c89b3accd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193471"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>規劃和準備群集部署

規劃和準備生產群集部署非常重要。  有許多因素需要考慮。  本文將引導您完成準備群集部署的步驟。

## <a name="read-the-best-practices-information"></a>閱讀最佳實踐資訊
為了成功管理 Azure 服務結構應用程式和群集，我們強烈建議您執行一些操作來優化生產環境的可靠性。  有關詳細資訊，請閱讀[Service Fabric 應用程式和群集最佳實踐](service-fabric-best-practices-overview.md)。

## <a name="select-the-os-for-the-cluster"></a>選擇群集的作業系統
Service Fabric 可讓您在執行 Windows Server 或 Linux 的任何 VM 或電腦上建立 Service Fabric 叢集。  在部署群集之前，必須選擇作業系統：Windows 或 Linux。  群集中的每個節點（虛擬機器）都運行相同的作業系統，您不能在同一群集中混合 Windows 和 Linux VM。

## <a name="capacity-planning"></a>容量規劃
對於任何生產部署而言，容量規劃都是一個很重要的步驟。 以下是一些您在該程序中必須考量的事情。

* 群集的初始節點類型數 
* 每個節點類型的屬性（大小、實例數、主節點、面向互聯網、VM 數等）
* 叢集的可靠性和持久性的特性

### <a name="select-the-initial-number-of-node-types"></a>選擇節點類型的初始數量
首先，您必須找出您要建立之叢集的用途。 您計劃將哪些類型的應用程式部署到此叢集中？ 您的應用程式是否有多個服務，而且其中是否有任何服務必須是公開或網際網路對向的服務？ 您 (構成應用程式) 的服務是否有不同的基礎結構需求，例如，更多的 RAM 或更高的 CPU 週期？ Service Fabric 群集可以由多個節點類型組成：主節點類型和一個或多個非主節點類型。 每個節點類型都會對應到虛擬機器擴展集。 然後每個節點類型可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並可以有不同的容量度量。 可以設置[節點屬性和放置約束][placementconstraints]，以將特定服務約束到特定的節點類型。  有關詳細資訊，請閱讀[群集需要從 開始的節點類型數](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with)。

### <a name="select-node-properties-for-each-node-type"></a>為每個節點類型選擇節點屬性
節點類型定義關聯比例集中 VM 的 VM SKU、編號和屬性。

每個節點類型的 VM 最小大小由您為節點類型選擇的[持久性層][durability]決定。

主要節點類型的 VM 數目下限取決於您選擇的[可靠性層級][reliability]。

請參閱[主節點類型](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance)、[非主節點類型的有狀態工作負載](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)和非[主節點類型的無狀態工作負載的最低](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads)建議。

任何超過最小節點數的節點應基於要在此節點類型中運行的應用程式/服務的副本數。  [Service Fabric 應用程式的容量規劃](service-fabric-capacity-planning.md)可説明您估計運行應用程式所需的資源。 您始終可以在以後向上或向下擴展群集，以便進行調整以更改應用程式工作負載。 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>對虛擬機器規模集使用臨時 OS 磁片

*臨時 OS 磁片*是在本地虛擬機器 （VM） 上創建的存儲，而不是保存到遠端 Azure 存儲。 建議所有 Service Fabric 節點類型（主節點和輔助節點）使用它們，因為與傳統持久性 OS 磁片相比，臨時 OS 磁片：

* 減少作業系統磁片的讀取/寫入延遲
* 實現更快的重置/重新映射節點管理操作
* 降低總體成本（磁片是免費的，不會產生額外的存儲成本）

臨時 OS 磁片不是特定的 Service Fabric 功能，而是映射到 Service Fabric 節點類型的 Azure*虛擬機器縮放集*的一項功能。 將它們與 Service Fabric 一起使用需要在群集 Azure 資源管理器範本中執行以下操作：

1. 確保節點類型為臨時 OS 磁片指定[受支援的 Azure VM 大小](../virtual-machines/windows/ephemeral-os-disks.md)，並確保 VM 大小具有足夠的緩存大小以支援其 OS 磁片大小（請參閱下面的*注釋*）。例如：

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > 請務必選擇緩存大小等於或大於 VM 本身的 OS 磁片大小的 VM 大小，否則 Azure 部署可能會導致錯誤（即使最初接受）。

2. 指定虛擬機器縮放集版本 （`vmssApiVersion`）`2018-06-01`或更高版本：

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. 在部署範本的虛擬機器縮放集部分中，指定`Local`選項： `diffDiskSettings`

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

有關詳細資訊和進一步配置選項，請參閱[Azure VM 的臨時作業系統磁片](../virtual-machines/windows/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>選擇群集的耐久性和可靠性級別
持久性層級用來向系統指示您的 VM 對於基本 Azure 基礎結構所擁有的權限。 在主要節點類型中，此權限可讓 Service Fabric 暫停會影響系統服務及具狀態服務的仲裁需求的任何 VM 層級基礎結構要求 (例如，VM 重新開機、VM 重新安裝映像，或 VM 移轉)。 在非主要節點類型中，此權限可讓 Service Fabric 暫停會影響具狀態服務之仲裁需求的任何 VM 層級基礎結構要求 (例如，VM 重新開機、VM 重新安裝映像和 VM 移轉)。  有關不同級別的優點和關於使用哪個級別和何時使用的建議，請參閱[群集的持久性特性][durability]。

可靠性層級用來設定您想要在此叢集中的主要節點類型上執行的系統服務複本數目。 複本數目越多，叢集中的系統服務越可靠。  有關不同級別的優點和關於使用哪個級別和何時使用的建議，請參閱[群集的可靠性特徵][reliability]。 

## <a name="enable-reverse-proxy-andor-dns"></a>啟用反向代理和/或 DNS
叢集內彼此連接的服務通常可以直接存取其他服務的端點，因為叢集中的節點位於相同的本機網路上。 為了更輕鬆地在服務之間連接，Service Fabric 提供了其他服務[：DNS 服務和](service-fabric-dnsservice.md)[反向代理服務](service-fabric-reverseproxy.md)。  部署群集時，可以啟用這兩個服務。

由於許多服務（尤其是容器化服務）可以具有現有的 URL 名稱，因此能夠使用標準 DNS 協定（而不是命名服務協定）解析這些 URL 操作很方便，尤其是在應用程式"提升和移動"方案中。 這就是 DNS 服務的功能所在。 它可讓您將 DNS 服務對應到某個服務名稱，再由此解析端點 IP 位址。

反向代理處理群集中公開 HTTP 終結點（包括 HTTPS）的服務。 反向代理通過提供特定的 URI 格式大大簡化了調用其他服務的功能。  反向代理還處理一個服務與另一個服務通信所需的解析、連接和重試步驟。

## <a name="prepare-for-disaster-recovery"></a>準備進行災害復原
提供高可用性的關鍵在於確保服務能夠承受所有不同類型的故障。 這對於預料之外且無法控制的故障而言特別重要。 [準備災害復原](service-fabric-disaster-recovery.md)描述了一些常見的故障模式，如果建模和管理不正確，這些模式可能是災難。 它還討論了災難發生時的緩解措施和行動。

## <a name="production-readiness-checklist"></a>實際執行整備檢查清單
您的應用程式和叢集已經準備好要接受生產環境流量嗎？ 在將群集部署到生產之前，請運行[生產就緒性檢查表](service-fabric-production-readiness-checklist.md)。 通過處理此清單中的項，使應用程式和群集平穩運行。 我們強烈建議在投入生產前檢查所有這些專案。

## <a name="next-steps"></a>後續步驟
* [創建運行 Windows 的服務結構群集](service-fabric-best-practices-overview.md)
* [創建運行 Linux 的服務交換矩陣群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster