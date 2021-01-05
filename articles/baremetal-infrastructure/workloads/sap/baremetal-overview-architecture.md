---
title: Azure 中的 BareMetal 基礎結構預覽總覽
description: 瞭解 Azure 中的 BareMetal 基礎結構。
ms.custom: references_regions
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: eb4dc129719dc410f7101598e3d72e68f17809c1
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97860974"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>什麼是 Azure 上的 BareMetal 基礎結構預覽？

Azure BareMetal 基礎結構提供一個安全的解決方案，可供您遷移企業自訂工作負載。 BareMetal 實例是指派給您的非共用主機/伺服器硬體。 它會使用需要經認證的硬體、授權和支援協定的特殊工作負載，來將您的內部內部部署解決方案解除鎖定。 Azure 會為您處理基礎結構監視和維護，而客體作業系統 (OS) 監視和應用程式監視落在您擁有權內。

BareMetal 基礎結構提供的途徑可讓您將基礎結構現代化，同時維持您現有的投資和架構。 使用 BareMetal 基礎結構，您可以將特製化工作負載帶入 Azure，讓您可以存取並與低延遲的 Azure 服務整合。

## <a name="sku-availability-in-azure-regions"></a>Azure 區域中的 SKU 可用性
適用于特殊化和一般用途工作負載的 BareMetal 基礎結構可供使用，從四個區域開始，以修訂 4.2 (Rev 4.2) 戳記：
- 西歐
- 北歐
- 美國東部 2
- 美國中南部

>[!NOTE]
>**Rev 4.2** 是最新的更名 BareMetal 基礎結構，使用現有的 Rev 4 架構。  Rev 4 可 (VM) 主機，更接近 Azure 虛擬機器。 它大幅改善了 Azure Vm 和 BareMetal 實例單位（部署于 Rev 4 戳記或資料列）之間的網路延遲。  您可以透過 Azure 入口網站存取和管理您的 BareMetal 實例。 

## <a name="support"></a>支援
BareMetal 基礎結構是符合 ISO 27001、ISO 27017、SOC 1 和 SOC 2。  它也會使用自備授權 (BYOL) 模型：作業系統、特製化工作負載和協力廠商應用程式。  

一旦您收到根存取權和完全控制，您就會承擔下列責任：
- 設計和執行備份和復原解決方案、高可用性和嚴重損壞修復
- 作業系統和協力廠商軟體的授權、安全性和支援

Microsoft 負責：
- 提供特製化工作負載的硬體 
- 布建 OS

:::image type="content" source="media/baremetal-support-model.png" alt-text="BareMetal 基礎結構支援模型" border="false":::

## <a name="compute"></a>計算
BareMetal 基礎結構針對特製化工作負載提供多個 Sku。 可用的 Sku 範圍從較小的雙插槽系統到24個通訊端系統。 針對您的特定工作負載使用工作負載專屬的 Sku。

BareMetal 實例戳記本身結合了下列元件：

- **計算：** 以不同的 Intel 以上處理器世代為基礎的伺服器，可提供必要的運算能力並經過特殊工作負載的認證。

- **網路：** 整合高速網路網狀架構互連運算、存放裝置和 LAN 元件。

- **儲存體：** 透過整合網路網狀架構存取的基礎結構。

在 BareMetal 戳記的多租使用者基礎結構內，客戶會部署在隔離的租使用者中。 部署租使用者時，您會將 Azure 訂用帳戶命名于您的 Azure 註冊中。 此 Azure 訂用帳戶是 BareMetal 實例計費的訂用帳戶。

>[!NOTE]
>部署在 BareMetal 實例中的客戶會隔離到租使用者中。 每個租用戶的網路、儲存體和計算層會彼此區隔開來。 指派給不同租使用者的儲存體和計算單位無法看到彼此，也無法在 BareMetal 實例上彼此通訊。

## <a name="os"></a>OS
布建 BareMetal 實例時，您可以選取要在電腦上安裝的 OS。 

>[!NOTE]
>請記住，BareMetal 基礎結構是 BYOL 模型。

可用的 Linux 作業系統版本如下：
- Red Hat Enterprise Linux (RHEL) 7。6
- SUSE Linux Enterprise Server (SLES)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>儲存體
以特定 SKU 類型為基礎的 BareMetal 實例隨附特定工作負載類型的預先定義 NFS 儲存體。 當您布建 BareMetal 時，您可以提交支援要求，根據預估成長來布建更多儲存體。 所有儲存體都會隨附修訂4.2 的全快閃磁片，並支援 NFSv3 和 NFSv4。 新的修訂 4.5 NVMe SSD 將可供使用。 如需儲存體大小調整的詳細資訊，請參閱 [BareMetal 工作負載類型](../../../virtual-machines/workloads/sap/get-started.md) 一節。

>[!NOTE]
>用於 BareMetal 的儲存體符合 [聯邦資訊處理標準 (FIPS) 發行集 140-2](/microsoft-365/compliance/offering-fips-140-2) 需求，預設會提供靜態加密。 資料會安全地儲存在磁片上。

## <a name="networking"></a>網路功能
Azure 網路服務的架構是在 BareMetal 實例中成功部署特製化工作負載的重要元件。 可能並非所有的 IT 系統都位於 Azure 中。 Azure 提供您網路技術，讓 Azure 看起來像是內部部署軟體部署的虛擬資料中心。 BareMetal 實例所需的 Azure 網路功能如下：

- Azure 虛擬網路會連線到 ExpressRoute 線路，以連接到您的內部部署網路資產。
- 將內部部署連線至 Azure 的 ExpressRoute 線路，其最小頻寬應為 1 Gbps 或更高。
- 擴充 Active directory 和 Azure 中的 DNS，或完全在 Azure 中執行。

使用 ExpressRoute 可讓您透過私人連線，將內部部署網路延伸至 Microsoft 雲端，透過連線提供者的說明。 您可以啟用 **Expressroute Premium** 以延伸跨地緣政治界限的連線，或使用 **expressroute Local** 在您想要的 Azure 區域附近的位置之間進行符合成本效益的資料傳輸。

BareMetal 實例會布建在您的 Azure VNET 伺服器 IP 位址範圍內。

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Azure BareMetal 基礎結構圖表" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

顯示的架構分成三個部分：
- **左邊：** 顯示客戶的內部部署基礎結構，此基礎結構會執行不同的應用程式，並透過夥伴或本機邊緣路由器（例如 Equinix）來連接。 如需詳細資訊，請參閱連線 [能力提供者和位置： Azure ExpressRoute](../../../expressroute/expressroute-locations.md)。
- **中心：** 顯示使用您的 azure 訂用帳戶布建的 [ExpressRoute](../../../expressroute/expressroute-introduction.md) ，以提供 azure edge 網路的連線能力。
- **Right：** 顯示 azure IaaS，在此案例中，使用 vm 來裝載您的應用程式，這些應用程式會布建在您的 Azure 虛擬網路中。
- **底部：** 顯示搭配 [expressroute FastPath](../../../expressroute/about-fastpath.md) 使用啟用的 expressroute 閘道，可提供低延遲的 BareMetal 連線能力。   
   >[!TIP]
   >為了支援這種情況，您的 ExpressRoute 閘道應該是 UltraPerformance。  如需詳細資訊，請參閱 [關於 ExpressRoute 虛擬網路閘道](../../../expressroute/expressroute-about-virtual-network-gateways.md)。

## <a name="next-steps"></a>後續步驟

下一步是瞭解如何透過 Azure 入口網站識別 BareMetal 實例單位並與其互動。

> [!div class="nextstepaction"]
> [透過 Azure 入口網站管理 BareMetal 執行個體](baremetal-infrastructure-portal.md)