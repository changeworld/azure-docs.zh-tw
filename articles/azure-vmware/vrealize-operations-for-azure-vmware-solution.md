---
title: 設定 Azure VMware 解決方案的 vRealize 作業
description: 瞭解如何為您的 Azure VMware 解決方案私人雲端設定 vRealize 作業。
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 25469089cf1fef076711bfaf1492fad43edbcf33
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371778"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>設定 Azure VMware 解決方案的 vRealize 作業


vRealize Operations Manager 是一種操作管理平臺，可讓 VMware 基礎結構系統管理員監視系統資源。 這些系統資源可以是應用層級或基礎結構層級 (實體和虛擬) 物件。 大部分的 VMware 系統管理員已使用 vRealize 作業來監視和管理 VMware 私用雲端元件（vCenter、ESXi、NSX-T、vSAN 和 VMware HCX）。  每個布建的 Azure VMware 解決方案私用雲端都包含專用的 vCenter、NSX-T、vSAN 和 HCX 部署。 

[在開始之前](#before-you-begin)和[必要條件](#prerequisites)之前，請先仔細檢查。 接著，我們將逐步引導您完成兩個典型的部署拓撲：

> [!div class="checklist"]
> * [管理 Azure VMware 解決方案部署的內部部署 vRealize 作業](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [在 Azure VMware 解決方案部署上執行的 vRealize 作業](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>開始之前
* 若要深入瞭解如何部署 vRealize 作業，請參閱 [vRealize Operations Manager 產品檔](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 。 
* 複習 Software-Defined Datacenter (SDDC) [教學課程系列](tutorial-network-checklist.md)的基本 Azure VMware 解決方案。
* （選擇性）請參閱管理 Azure VMware 解決方案部署選項之內部部署 vRealize 作業的 [VRealize Operations 遠端控制器](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) 產品檔。 



## <a name="prerequisites"></a>先決條件
* 在內部部署與 Azure VMware Solution SDDC 之間設定的 VPN 或 Azure ExpressRoute。
* Azure VMware 解決方案私人雲端已部署在 Azure 中。



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>管理 Azure VMware 解決方案部署的內部部署 vRealize 作業
大部分的客戶都有現有的內部部署 vRealize 作業，可管理一或多個內部部署 vCenters 網域。 當他們布建 Azure VMware 解決方案私人雲端時，他們會使用 Azure ExpressRoute 或第3層 VPN 解決方案，將其內部部署環境與其私人雲端連線。  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="管理 Azure VMware 解決方案部署的內部部署 vRealize 作業"  border="false":::

若要將 vRealize 作業功能延伸至 Azure VMware 解決方案私人雲端，您可以建立 [私人雲端資源](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html)的介面卡實例。 它會從 Azure VMware Solution 私用雲端收集資料，並將它帶入內部部署的 vRealize 作業。 內部部署 vRealize Operations Manager 實例可以直接連接到 Azure VMware 解決方案上的 vCenter 和 NSX-T 管理員。 （選擇性）您可以在 Azure VMware 解決方案私人雲端上部署 vRealize 作業遠端收集器。 收集器會先壓縮並加密從私人雲端收集的資料，然後再透過 ExpressRoute 或 VPN 網路將其傳送至內部部署的 vRealize Operations Manager。 

> [!TIP]
> 如需安裝 vRealize Operations Manager 的逐步指南，請參閱 [VMware 檔](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 。 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>在 Azure VMware 解決方案部署上執行的 vRealize 作業

另一個選項是在私用雲端中的 vSphere 叢集上部署 vRealize Operations Manager 的實例。 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="管理 Azure VMware 解決方案部署的內部部署 vRealize 作業" border="false":::

部署實例之後，您可以設定 vRealize 作業，以收集來自 vCenter、ESXi、NSX-T、vSAN 和 HCX 的資料。 

> [!TIP]
> 如需安裝 vRealize Operations Manager 的逐步指南，請參閱 [VMware 檔](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) 。


## <a name="known-limitations"></a>已知的限制

- Azure VMware 解決方案中的 **cloudadmin \@ vsphere。本機** 使用者具有 [有限的許可權](concepts-role-based-access-control.md)。  Azure VMware 解決方案上的虛擬機器 (Vm) 不支援使用 VMware 工具的來賓記憶體收集。  在此情況下，作用中和已耗用的記憶體使用量仍會繼續運作。
- 以主機為基礎之商務意圖的工作負載優化無法運作，因為 Azure VMware 解決方案會管理叢集設定，包括 DRS 設定。
- VRealize Operations Manager 8.0 和更新版本中，已完全支援使用叢集型商務意圖在 SDDC 中放置跨叢集的工作負載優化。 不過，工作負載優化不會察覺資源集區，而會將 Vm 放在叢集層級。 使用者可以在 Azure VMware Solution vCenter Server 介面中手動更正它。
- 您無法使用 Azure VMware 解決方案 vCenter Server 認證來登入 vRealize Operations Manager。 
- Azure VMware 解決方案不支援 vRealize Operations Manager 外掛程式。

當您使用 vCenter Server 雲端帳戶將 Azure VMware 解決方案 vCenter 連線到 vRealize Operations Manager 時，您會看到一則警告：

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="管理 Azure VMware 解決方案部署的內部部署 vRealize 作業":::

發生此警告的原因是 Azure VMware 解決方案中的 **cloudadmin \@ vsphere** ，沒有足夠的許可權來執行註冊所需的所有 vCenter Server 動作。 不過，這些許可權足以讓介面卡實例進行資料收集，如下所示：

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="管理 Azure VMware 解決方案部署的內部部署 vRealize 作業":::

如需詳細資訊，請參閱設定 [VCenter Adapter 實例所需的許可權](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html)。

<!-- LINKS - external -->


<!-- LINKS - internal -->




