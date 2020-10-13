---
title: 設定 Azure VMware 解決方案的 vRealize 作業
description: 瞭解如何為您的 Azure VMware 解決方案私人雲端設定 vRealize 作業。
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9e512d107ddc4d9bca28323658d09f4b4b378dc3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579940"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>設定 Azure VMware 解決方案的 vRealize 作業


vRealize Operations Manager 是一種操作管理平臺，可讓 VMware 基礎結構系統管理員監視系統資源。 這些系統資源可以是應用層級或基礎結構層級 (實體和虛擬) 物件。 在過去，大部分的 VMware 系統管理員已使用 vRealize 作業來監視和管理 VMware 私用雲端元件（vCenter、ESXi、NSX-T、vSAN 和混合式雲端擴充 (HCX) ）。 每個 Azure VMware 解決方案私人雲端都會布建專用的 vCenter、NSX-T、vSAN 和 HCX 部署。 

[在開始之前](#before-you-begin)和[必要條件](#prerequisites)之前，請先仔細檢查。 接著，我們將逐步引導您使用 Azure VMware 解決方案 vRealize Operations Manager 的兩個典型部署拓撲：

> [!div class="checklist"]
> * [管理 Azure VMware 解決方案部署的內部部署 vRealize 作業](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [在 Azure VMware 解決方案部署上執行的 vRealize 作業](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>開始之前
* 請參閱 [vRealize Operations Manager 產品檔](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) ，以深入瞭解如何部署 vRealize 作業。 
* 檢閱基本 Azure VMware 解決方案軟體定義資料中心 (SDDC) [教學課程系列](tutorial-network-checklist.md)。
* （選擇性）請參閱管理 Azure VMware 解決方案部署選項之內部部署 vRealize 作業的 [VRealize Operations 遠端控制器](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) 產品檔。 



## <a name="prerequisites"></a>必要條件
* 您應在內部部署與 Azure VMware Solution SDDC 之間設定 VPN 或 Azure ExpressRoute。
* Azure VMware 解決方案私人雲端已部署在 Azure 中。



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>管理 Azure VMware 解決方案部署的內部部署 vRealize 作業
大部分的客戶都有現有的內部部署 vRealize 作業，可用來管理一或多個內部部署 vCenters 網域。 當客戶在 Azure 中布建新的 Azure VMware 解決方案私人雲端時，它們通常會使用 Azure ExpressRoute 或第3層 VPN 解決方案，將其內部部署環境連線至 Azure VMware 解決方案，如下所示。   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="管理 Azure VMware 解決方案部署的內部部署 vRealize 作業"  border="false":::

若要將 vRealize 作業功能延伸至 Azure VMware 解決方案私人雲端，您可以建立 [Azure Vmware 解決方案私用雲端資源](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) 的介面卡實例，以從 Azure vmware 解決方案私人雲端收集資料，並將它帶入內部部署的 vRealize 作業。 內部部署 vRealize Operations Manager 實例可以直接連接到 Azure VMware 解決方案上的 vCenter 和 NSX-T 管理員，或者您可以選擇在 Azure VMware 解決方案私人雲端上部署 vRealize 作業遠端收集器。 VRealize 作業會從 Azure VMware 解決方案私人雲端壓縮和加密收集到的資料，然後再透過 ExpressRoute 或 VPN 網路將其傳送至內部部署的 vRealize Operations Manager。 

> [!TIP]
> 如需安裝 vRealize Operations Manager 的逐步指南，請參閱 [VMware 檔](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 。 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>在 Azure VMware 解決方案部署上執行的 vRealize 作業

另一個部署選項是在 Azure VMware Solution 私用雲端中的其中一個 vSphere 叢集上部署 vRealize Operations Manager 的實例，如下所示。 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="管理 Azure VMware 解決方案部署的內部部署 vRealize 作業" border="false":::

部署 vRealize 作業的 Azure VMware 解決方案實例之後，您可以設定 vRealize 作業，以收集來自 vCenter、ESXi、NSX-T、vSAN 和 HCX 的資料。 

> [!TIP]
> 如需安裝 vRealize Operations Manager 的逐步指南，請參閱 [VMware 檔](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 。


## <a name="known-limitations"></a>已知限制

- **cloudadmin@vsphere.local**Azure VMware 解決方案中的使用者具有[有限的許可權](concepts-role-based-access-control.md)。 在 Azure VMware 解決方案上)  (Vm 的虛擬機器，不支援使用 VMware 工具的內部來賓記憶體收集。 在此情況下，作用中和已耗用的記憶體使用率仍會繼續運作。
- 以主機為基礎之商務意圖的工作負載優化無法運作，因為 Azure VMware 解決方案會管理叢集設定，包括 DRS 設定。
- VRealize Operations Manager 8.0 和更新版本中，已完全支援使用叢集型商務意圖在 SDDC 中放置跨叢集的工作負載優化。 但是，工作負載優化不會察覺資源集區，而是將虛擬機器放置在叢集層級。 使用者可以在 Azure VMware Solution vCenter Server 介面中手動更正此錯誤。
- 您無法使用 Azure VMware 解決方案 vCenter Server 認證來登入 vRealize Operations Manager。 
- Azure VMware 解決方案不支援 vRealize Operations Manager 外掛程式。

使用 vCenter Server 雲端帳戶將 Azure VMware 解決方案 vCenter 連接到 vRealize Operations Manager 時，您將會遇到下列警告：

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="管理 Azure VMware 解決方案部署的內部部署 vRealize 作業":::

因為 **cloudadmin@vsphere.local** Azure VMware 解決方案中的使用者沒有足夠的許可權來執行註冊所需的所有 vCenter Server 動作，所以會發生此警告。 不過，這些許可權足以讓介面卡實例進行資料收集，如下所示：

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="管理 Azure VMware 解決方案部署的內部部署 vRealize 作業":::

如需詳細資訊，請參閱設定 [VCenter Adapter 實例所需的許可權](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html)。

<!-- LINKS - external -->


<!-- LINKS - internal -->




