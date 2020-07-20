---
title: 規劃 Azure Service Fabric 叢集部署
description: 瞭解如何規劃和準備生產環境 Service Fabric 叢集部署至 Azure。
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 462548d7f32a015701ef12e9777e8d9b1b1350f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610586"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>規劃及準備叢集部署

規劃和準備生產叢集部署非常重要。  有許多因素需要考慮。  這篇文章會逐步引導您完成準備叢集部署的步驟。

## <a name="read-the-best-practices-information"></a>閱讀最佳做法資訊
若要成功管理 Azure Service Fabric 應用程式和叢集，我們強烈建議您執行一些作業，以優化生產環境的可靠性。  如需詳細資訊，請參閱[Service Fabric 應用程式和叢集最佳做法](service-fabric-best-practices-overview.md)。

## <a name="select-the-os-for-the-cluster"></a>選取叢集的 OS
Service Fabric 可讓您在執行 Windows Server 或 Linux 的任何 VM 或電腦上建立 Service Fabric 叢集。  部署叢集之前，您必須選擇 OS： Windows 或 Linux。  叢集中的每個節點（虛擬機器）都會執行相同的作業系統，您無法在相同的叢集中混用 Windows 和 Linux Vm。

## <a name="capacity-planning"></a>容量規劃
對於任何生產部署而言，容量規劃都是一個很重要的步驟。 以下是一些您在該程序中必須考量的事情。

* 叢集節點類型的初始數目 
* 每個節點類型的屬性（大小、實例數目、主要、網際網路面向、Vm 數目等）
* 叢集的可靠性和持久性的特性

### <a name="select-the-initial-number-of-node-types"></a>選取節點類型的初始數目
首先，您必須找出您要建立之叢集的用途。 您計劃將哪些類型的應用程式部署到此叢集中？ 您的應用程式是否有多個服務，而且其中是否有任何服務必須是公開或網際網路對向的服務？ 您 (構成應用程式) 的服務是否有不同的基礎結構需求，例如，更多的 RAM 或更高的 CPU 週期？ Service Fabric 叢集可以包含一個以上的節點類型：主要節點類型和一或多個非主要節點類型。 每個節點類型都會對應到虛擬機器擴展集。 然後每個節點類型可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並可以有不同的容量度量。 [節點屬性和放置條件約束][placementconstraints]可設定為將特定服務限制為特定的節點類型。  如需詳細資訊，請參閱[Service Fabric 叢集容量規劃](service-fabric-cluster-capacity.md)。

### <a name="select-node-properties-for-each-node-type"></a>針對每個節點類型選取節點屬性
節點類型會定義相關聯擴展集中 vm 的 VM SKU、數目和屬性。

每個節點類型的 Vm 大小下限取決於您為節點類型選擇的[持久性層級][durability]。

主要節點類型的 VM 數目下限取決於您選擇的[可靠性層級][reliability]。

請參閱[主要節點類型](service-fabric-cluster-capacity.md#primary-node-type)的最小建議、[非主要節點類型的具狀態工作負載](service-fabric-cluster-capacity.md#stateful-workloads)，以及[非主要節點類型上的無狀態工作負載](service-fabric-cluster-capacity.md#stateless-workloads)。

節點數目下限應取決於您想要在此節點類型中執行的應用程式/服務複本數目。  [Service Fabric 應用程式的容量規劃](service-fabric-capacity-planning.md)可協助您估計執行應用程式所需的資源。 您之後可以隨時相應增加或相應減少叢集，以調整應用程式工作負載的變更。 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>使用虛擬機器擴展集的暫時 OS 磁片

*暫時的 OS 磁片*是在本機虛擬機器（VM）上建立的儲存體，並不會儲存至遠端 Azure 儲存體。 針對所有 Service Fabric 節點類型（主要和次要），建議使用它們，因為相較于傳統的持續性 OS 磁片，暫時的 OS 磁片：

* 減少 OS 磁片的讀取/寫入延遲
* 啟用更快速的重設/重新安裝映射節點管理作業
* 降低整體成本（磁片是免費的，不會產生額外的儲存體成本）

暫時性 OS 磁片不是特定的 Service Fabric 功能，而是對應至 Service Fabric 節點類型之 Azure*虛擬機器擴展集*的一項功能。 將它們與 Service Fabric 搭配使用時，您的叢集 Azure Resource Manager 範本中需要下列專案：

1. 請確定您的節點類型指定暫時 OS 磁片[支援的 AZURE VM 大小](../virtual-machines/windows/ephemeral-os-disks.md)，而且 VM 大小具有足夠的快取大小來支援其 OS 磁片大小（請參閱下面的*附注*）。例如：

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > 請務必選取快取大小等於或大於 VM 本身 OS 磁片大小的 VM 大小，否則您的 Azure 部署可能會導致錯誤（即使最初被接受）。

2. 指定或更新版本的虛擬機器擴展集版本（ `vmssApiVersion` ） `2018-06-01` ：

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. 在部署範本的 [虛擬機器擴展集] 區段中，指定 `Local` `diffDiskSettings` 下列選項：

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

> [!NOTE]
> 使用者應用程式不應該在 OS 磁片上有任何相依性/檔案/成品，因為 os 磁片會在作業系統升級時遺失。
> 因此，不建議將[PatchOrchestrationApplication](https://github.com/microsoft/Service-Fabric-POA)與暫時磁片搭配使用。
>

> [!NOTE]
> 現有的非暫時 VMSS 無法就地升級以使用暫時磁片。
> 若要遷移，使用者[必須新增具有](./virtual-machine-scale-set-scale-node-type-scale-out.md)暫時磁片的 nodeType，將工作負載移至新的 nodetype &[移除](./service-fabric-how-to-remove-node-type.md)現有的 nodetype。
>

如需詳細資訊和其他設定選項，請參閱[Azure vm 的暫時 OS 磁片](../virtual-machines/windows/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>選取叢集的持久性和可靠性層級
持久性層級用來向系統指示您的 VM 對於基本 Azure 基礎結構所擁有的權限。 在主要節點類型中，此權限可讓 Service Fabric 暫停會影響系統服務及具狀態服務的仲裁需求的任何 VM 層級基礎結構要求 (例如，VM 重新開機、VM 重新安裝映像，或 VM 移轉)。 在非主要節點類型中，此權限可讓 Service Fabric 暫停會影響具狀態服務之仲裁需求的任何 VM 層級基礎結構要求 (例如，VM 重新開機、VM 重新安裝映像和 VM 移轉)。  如需不同層級和建議的優點和時機，請參閱叢集[的持久性特性][durability]。

可靠性層級用來設定您想要在此叢集中的主要節點類型上執行的系統服務複本數目。 複本數目越多，叢集中的系統服務越可靠。  如需不同層級和建議的優點和時機，請參閱叢集[的可靠性特性][reliability]。 

## <a name="enable-reverse-proxy-andor-dns"></a>啟用反向 proxy 和/或 DNS
叢集內彼此連接的服務通常可以直接存取其他服務的端點，因為叢集中的節點位於相同的本機網路上。 為了讓您更輕鬆地在服務之間進行連接，Service Fabric 提供了額外的服務： [DNS 服務](service-fabric-dnsservice.md)和[反向 proxy 服務](service-fabric-reverseproxy.md)。  部署叢集時，可以啟用這兩項服務。

由於許多服務（特別是容器化服務）都可以有現有的 URL 名稱，因此能夠使用標準 DNS 通訊協定（而不是命名服務通訊協定）來解決這些問題很方便，尤其是在應用程式「隨即轉移」案例中。 這就是 DNS 服務的功能所在。 它可讓您將 DNS 服務對應到某個服務名稱，再由此解析端點 IP 位址。

反向 proxy 會定址叢集中公開 HTTP 端點（包括 HTTPS）的服務。 反向 proxy 會藉由提供特定的 URI 格式，大幅簡化呼叫其他服務的程式。  反向 proxy 也會處理一個服務與另一個服務進行通訊所需的解析、連接和重試步驟。

## <a name="prepare-for-disaster-recovery"></a>準備進行災害復原
提供高可用性的關鍵在於確保服務能夠承受所有不同類型的故障。 這對於預料之外且無法控制的故障而言特別重要。 「準備嚴重損壞[修復](service-fabric-disaster-recovery.md)」說明一些常見的失敗模式，如果未正確地進行模型化和管理，可能會造成災難。 它也會討論在發生嚴重損壞時，應採取的緩和措施和動作。

## <a name="production-readiness-checklist"></a>實際執行整備檢查清單
您的應用程式和叢集已經準備好要接受生產環境流量嗎？ 將叢集部署到生產環境之前，請先執行[生產環境就緒檢查清單](service-fabric-production-readiness-checklist.md)。 藉由使用此檢查清單中的專案，讓您的應用程式和叢集順暢地執行。 我們強烈建議您在進入生產環境之前，先核取這些專案。

## <a name="next-steps"></a>後續步驟
* [建立執行 Windows 的 Service Fabric 叢集](service-fabric-best-practices-overview.md)
* [建立執行 Linux 的 Service Fabric 叢集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster