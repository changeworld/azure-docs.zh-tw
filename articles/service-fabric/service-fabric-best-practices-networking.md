---
title: Azure Service Fabric 網路的最佳作法
description: 使用 Azure Service Fabric 管理網路連線能力的規則和設計考慮。
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: b8db69792b31fd82646757423e669e39e8539d06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630697"
---
# <a name="networking"></a>網路功能

當您建立和管理 Azure Service Fabric 叢集時，您會為節點和應用程式提供網路連線。 這些網路資源包括 IP 位址範圍、虛擬網路、負載平衡器和網路安全性群組。 在本文中，您將了解這些資源的最佳做法。

請參閱 Azure [Service Fabric 網路功能模式](./service-fabric-patterns-networking.md) ，以瞭解如何建立使用下列功能的叢集：現有的虛擬網路或子網、靜態公用 IP 位址、僅限內部的負載平衡器，或內部和外部負載平衡器。

## <a name="infrastructure-networking"></a>基礎結構網路功能
藉由在 Resource Manager 範本中宣告 *enableAcceleratedNetworking* 屬性，下列程式碼片段是可啟用加速網路功能的虛擬機器擴展集 NetworkInterfaceConfigurations，可讓您以加速網路將虛擬機器的效能最大化。

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Service Fabric 叢集可以佈建在[使用加速網路的 Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 上，以及[使用加速網路的 Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) 上。

Azure 虛擬機器系列 Sku 支援加速網路： D/DSv2、D/DSv3、E/ESv3、F/FS、FSv2 和 Ms/Mms。 加速網路已使用01/23/2019 上的 Standard_DS8_v3 SKU （適用于 Service Fabric Windows 叢集）成功測試，並針對 Service Fabric Linux 叢集使用01/29/2019 上的 Standard_DS12_v2。

若要在現有的 Service Fabric 叢集上啟用加速網路，您必須先[透過新增虛擬機器擴展集將 Service Fabric 叢集相應放大](./virtual-machine-scale-set-scale-node-type-scale-out.md)，以執行下列動作：
1. 佈建已啟用加速網路的 NodeType
2. 將您的服務及其狀態移轉至已佈建並啟用加速網路的 NodeType

要將基礎結構相應放大，必須在現有的叢集上啟用加速網路，因為要就地啟用加速網路，必須將可用性設定組中的所有虛擬機器全部[停止和解除配置，才能在任何現有的 NIC 上啟用加速網路](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)，因此會導致停機時間。

## <a name="cluster-networking"></a>叢集網路功能

* Service Fabric 叢集可依照 [Service Fabric 網路功能模式](./service-fabric-patterns-networking.md)中說明的步驟部署到現有的虛擬網路中。

* 建議將網路安全性群組 (Nsg) 用於節點類型，以限制其叢集的輸入和輸出流量。 請確實在 NSG 中開啟必要的連接埠。 

* 主要節點類型包含 Service Fabric 系統服務，因此不需要透過外部負載平衡器來公開，而可由[內部負載平衡器](./service-fabric-patterns-networking.md#internal-only-load-balancer)來公開

* 為您的叢集建立[靜態公用 IP 位址](./service-fabric-patterns-networking.md#static-public-ip-address-1)。

## <a name="network-security-rules"></a>網路安全性規則

此處的基本規則是 Azure 受控 Service Fabric 叢集安全性鎖定的最小值。 若無法開啟下列埠或核准 IP/URL，將會導致叢集無法正常運作，而且可能不受支援。 使用此規則集時，絕對必須使用 [自動 OS 映射升級](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)，否則將需要開啟其他埠。

### <a name="inbound"></a>輸入 
|優先順序   |名稱               |連接埠        |通訊協定  |來源             |Destination       |動作   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |Internet           |VirtualNetwork    |允許
|3910       |用戶端             |19000       |TCP       |Internet           |VirtualNetwork    |允許
|3920       |叢集            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |允許
|3930       |短暫          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |允許
|3940       |應用程式        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |允許
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |允許
|3960       |RDP                |3389-3488   |TCP       |Internet           |VirtualNetwork    |拒絕
|3970       |SSH                |22          |TCP       |Internet           |VirtualNetwork    |拒絕
|3980       |自訂端點    |80          |TCP       |Internet           |VirtualNetwork    |允許
|4100       |封鎖輸入      |443         |任意       |任意                |任意               |Allow

輸入安全性規則的詳細資訊：

* **Azure**。 Service Fabric Explorer 會使用此埠來流覽和管理您的叢集，而且 Service Fabric 資源提供者也會使用此埠來查詢您叢集的相關資訊，以便在 Azure 管理入口網站中顯示。 如果無法從 Service Fabric 資源提供者存取此埠，您會在 Azure 入口網站中看到一則訊息，例如「找不到節點」或「UpgradeServiceNotReachable」，而您的節點和應用程式清單會顯示為空白。 這表示，如果您想要在 Azure 管理入口網站中看到您的叢集，您的負載平衡器必須公開公用 IP 位址，且您的 NSG 必須允許傳入的19080流量。  

* **用戶端**。 Api （例如 REST/PowerShell/CLI）的用戶端連接端點。 

* **群集**。 用於節點間通訊;永遠不會遭到封鎖。

* **暫時**。 Service Fabric 會使用一部分連接埠做為應用程式連接埠，其餘連接埠則可供 OS 使用。 它也會將此範圍對應至 OS 中存在的現有範圍，因此針對所有用途，您可以使用範例中提供的範圍。 請確保頭尾連接埠之間相差至少 255。 如果這項差異太低，您可能會遇到衝突，因為這個範圍會與 OS 共用。 若要查看已設定的動態埠範圍，請執行 *netsh int ipv4 show 動態埠 tcp*。 Linux 叢集不需要這些埠。

* **應用程式**。 應用程式連接埠範圍應該足以涵蓋應用程式的端點需求。 此範圍應該排除於電腦上的動態連接埠範圍 (也就是在組態中設定的 ephemeralPorts 範圍) 之外。 Service Fabric 在需要新埠時使用這些埠，並負責在節點上開啟這些埠的防火牆。

* **SMB**。 ImageStore 服務使用 SMB 通訊協定進行兩個案例。 需要此埠才能從 ImageStore 下載套件，以及在複本之間複寫這些封裝。 

* **RDP**。 如果需要從網際網路或 VirtualNetwork 進行 jumpbox 案例，則可選擇是否要進行 RDP。 

* **SSH**。 如果需要來自網際網路的 SSH 或 jumpbox 案例的 VirtualNetwork，則為選擇性。

* **自訂端點**。 讓您的應用程式啟用網際網路可存取端點的範例。

### <a name="outbound"></a>輸出

|優先順序   |名稱               |連接埠        |通訊協定  |來源             |Destination       |動作   
|---        |---                |---         |---       |---                |---               |---
|3900       |網路            |任意         |TCP       |VirtualNetwork     |VirtualNetwork    |允許
|3910       |資源提供者  |443         |TCP       |VirtualNetwork     |ServiceFabric     |允許
|3920       |升級            |443         |TCP       |VirtualNetwork     |Internet          |允許
|3950       |封鎖輸出     |任意         |任意       |任意                |任意               |拒絕

輸出安全性規則的詳細資訊：

* **網路**。 子網和另一個虛擬網路的通道。

* **資源提供者**。 由 UpgradeService 連接，以執行 Service Fabric 資源提供者的所有 ARM 部署。

* **升級**。 使用 address download.microsoft.com 來取得 bits 的升級服務，這是安裝程式、重新映射與執行時間升級的必要項。 服務會使用動態 IP 位址來操作。 在「僅內部」負載平衡器的案例中，必須將額外的外部負載平衡器新增至範本，並提供可允許埠443輸出流量的規則。 （選擇性）您可以在安裝成功之後封鎖此埠，但在此情況下，必須將升級套件散發至節點，否則必須在短時間內開啟該埠，之後需要手動升級。

使用具有 [NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-overview.md) 和使用 [分析](../network-watcher/traffic-analytics.md) 的 Azure 防火牆來追蹤安全性鎖定的問題。 [使用 NSG Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)的 ARM 範本是開始使用的絕佳範例。 


## <a name="application-networking"></a>應用程式網路功能

* 若要執行 Windows 容器工作負載，請使用[開放網路模式](./service-fabric-networking-modes.md#set-up-open-networking-mode)，以便進行服務對服務的通訊。

* 使用反向 Proxy (例如 [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) 或 [Service Fabric 反向 Proxy](./service-fabric-reverseproxy.md)) 公開常用的應用程式連接埠，例如 80 或 443。

* 針對在無法從 Azure 雲端儲存體提取基礎層的空調電腦上裝載的 Windows 容器，請使用 Docker daemon 中的 [--allow-nondistributable-構件](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) 旗標來覆寫外部層行為。

## <a name="next-steps"></a>後續步驟

* 在執行 Windows Server 的 VM 或電腦上建立叢集： [建立適用於 Windows Server 的 Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)
* 在 VM 或執行 Linux 的電腦上建立叢集：[建立 Linux 叢集](service-fabric-cluster-creation-via-portal.md)
* 瞭解 [Service Fabric 支援選項](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
