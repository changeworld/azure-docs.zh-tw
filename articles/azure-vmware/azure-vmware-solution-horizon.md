---
title: Azure VMware 解決方案上的部署範圍
description: 瞭解如何在 Azure VMware 解決方案上部署 VMware 的範圍。
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 60207b0ed9e1df805ac667752b55f14a693ec25c
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91492416"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Azure VMware 解決方案上的部署範圍 

>[!NOTE]
>本檔著重于 VMware 的水準產品。 在產品名稱從範圍7變更為水準之前，這先前稱為範圍7。 範圍是與 Azure 上的雲端範圍不同的解決方案，不過有一些共用元件。 Azure VMware 解決方案的主要優點包括簡單的調整大小方法，以及將 VMware Cloud Foundation 管理整合到 Azure 入口網站中。

[VMware 範圍](https://www.vmware.com/products/horizon.html)®是在資料中心執行的虛擬桌面和應用程式平臺，並提供簡單且集中式的管理。 它會將虛擬桌面和應用程式提供給任何裝置上的終端使用者。 範圍可讓您建立和建立與 Windows 虛擬桌面、Linux 虛擬桌面電腦、遠端桌面伺服器 (RDS) 託管應用程式、桌上型電腦和實體機器之間的連線。

在這裡，我們將特別著重于在 Azure VMware 解決方案上部署的範圍。 如需 VMware 的一般資訊，請參閱集中生產檔：

* [VMware 的範圍為何？](https://www.vmware.com/products/horizon.html)

* [深入瞭解 VMware 範圍](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [範圍參考架構](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

有了 Azure VMware 解決方案的範圍簡介，現在有兩個虛擬桌面基礎結構 (VDI) Azure 平臺上的解決方案。 下圖摘要說明高層級的主要差異。

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Azure VMware 解決方案的範圍與 Azure 上的範圍雲端之間的差異" border="false":::

範圍8版的2006和更新版本支援內部部署和 Azure VMware 解決方案部署。 有一些在內部部署環境中支援但無法在 Azure VMware 解決方案上使用的範圍功能。 也支援範圍生態系統中的其他產品。 如需詳細資訊，請參閱功能同位 [和互通性](https://kb.vmware.com/s/article/80850)。

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>在混合式雲端中部署範圍

您可以在混合式雲端環境中部署範圍 (CPA) ，以將內部部署資料中心與 Azure 資料中心互連。 CPA 通常用來擴大您的部署、建立混合式雲端，以及供應商務持續性和嚴重損壞修復的冗余。 如需 VMware 範圍商務持續性指引的深入討論，請參閱 [擴充現有的範圍7環境](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874)。

>[!IMPORTANT]
>CPA 不是延伸的部署;每個水準 pod 都是相異的，而且所有屬於個別 pod 的連接伺服器都必須位於單一位置，並從網路的觀點來執行于相同的廣播網域上。

就像內部部署或私用資料中心一樣，範圍可以部署在 Azure VMware 解決方案私人雲端中。 在下列各節中，我們將討論在內部部署環境和 Azure VMware 解決方案中部署範圍的主要差異。

Azure 私用雲端在概念上與 VMware SDDC 相同，此詞彙通常用於範圍廣泛的檔。 本檔的其餘部分將使用 Azure 私用雲端和 VMware SDDC 可互換的詞彙。

若要管理訂用帳戶授權，Azure VMware 解決方案上的範圍必須要有範圍雲端連接器。 雲端連接器可以部署在 Azure 虛擬網路中，並與水準連線伺服器一起部署。

>[!IMPORTANT]
>目前尚無法使用 Azure VMware 解決方案範圍的範圍控制平面支援。 請務必下載範圍雲端連接器的 VHD 版本。

## <a name="vcenter-cloud-admin-role"></a>vCenter 雲端系統管理員角色

由於 Azure VMware 解決方案是一個 SDDC 服務，而 Azure 會管理 Azure VMware 解決方案上的 SDDC 生命週期，因此 Azure VMware 解決方案上的 vCenter 許可權模型受限於設計。

客戶必須使用雲端系統管理員角色，此角色具有一組有限的 vCenter 許可權。 您可以使用 Azure VMware 解決方案上的雲端系統管理員角色來修改範圍產品，特別是：

* 立即複製布建已修改為可在 Azure VMware 解決方案上執行。

* 特定的 vSAN 原則 (VMware_Horizon) 是在 Azure VMware 解決方案上建立，以搭配使用，因此必須可供使用，並可用於針對範圍部署的 SDDCs。

* 在 Azure VMware 解決方案上執行時，vSphere 以內容為基礎的讀取快取 (CBRC) （也稱為「查看儲存體加速器」）已停用。

>[!IMPORTANT]
>CBRC 不得重新開啟。

>[!NOTE]
>Azure VMware 解決方案會自動設定特定的範圍設定，只要您部署的範圍是 2006 (，也就是在「範圍8」分支上部署的範圍) ，然後在「水準連線伺服器」安裝程式中選取 [ **Azure** ] 選項。

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Azure VMware 解決方案部署架構的範圍

一般的範圍架構設計會使用 pod 和封鎖策略。 區塊是單一 vCenter，而多個區塊結合了 pod。 水準 pod 是由水準規模調整限制所決定的組織單位。 每個水準 pod 都有個別的入口網站，因此標準設計作法是將 pod 數目降至最低。

每個雲端都有自己的網路連接配置。 相較于 VMware SDDC 網路/NSX Edge，Azure VMware 解決方案網路連線提供了不同于內部部署之部署範圍的獨特需求。

每個 Azure 私用雲端/SDDC 都能夠處理4000的桌面或應用程式會話，其假設如下：

* 工作負載流量會與 LoginVSI 任務背景工作設定檔的流量一致。

* 只會考慮通訊協定流量，沒有使用者資料。

* NSX Edge 設定為大。

>[!NOTE]
>您的工作負載設定檔和需求可能會不同，因此結果可能會根據您的使用案例而有所不同。 使用者資料量可能會降低工作負載內容中的規模限制。 調整規模，並據以規劃您的部署。 如需詳細資訊，請參閱「 [適用于範圍部署的 Azure VMware 解決方案主機大小](#size-azure-vmware-solution-hosts-for-horizon-deployments) 」一節中的大小調整指導方針。

假設 Azure 私用雲端/SDDC 的最大限制，我們建議在 Azure 虛擬網路內執行 (UAGs) 的集中連線伺服器和 VMware 整合存取閘道的部署架構。 這會有效地將每個 Azure 私用雲端/SDDC 轉換成一個區塊。 如此一來，就能將 Azure VMware 解決方案上執行範圍的擴充性最大化。

從 Azure 虛擬網路到 Azure 私用雲端/SDDCs 的連線，應設定 ExpressPath Fast Path。 下圖顯示基本的範圍 pod 部署。

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Azure VMware 解決方案的範圍與 Azure 上的範圍雲端之間的差異" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>在 Azure VMware 解決方案上調整規模範圍的網路連線能力

本節會以高層級將網路架構的版面配置，以使用一些常見的部署範例來調整 Azure VMware 解決方案的範圍。 這裡的重點是專門針對重要的網路元素。

### <a name="single-horizon-pd-on-azure-vmware-solution"></a>Azure VMware 解決方案上的單一範圍 pd

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Azure VMware 解決方案的範圍與 Azure 上的範圍雲端之間的差異" border="false":::

單一的水準 pod 是最直接的部署案例。 在此範例中，您決定要在美國東部區域只部署一個範圍的 pod。 由於每個私用雲端/SDDC 估計大約會處理4000桌面會話的流量，因此若要部署最大範圍的 pod 大小，您可以規劃最多三個私用雲端/SDDCs 的部署。

因此，在此範例中，搭配基礎結構虛擬機器 (Vm) 部署在 Azure 虛擬網路中，您可以根據您的工作負載和資料需求，連線到每個水準 pod 的12000會話。 每個私人雲端和 SDDC 與 Azure 虛擬網路之間的連線是 ExpressRoute 快速路徑，因此不需要私人雲端之間的任何東西部流量。

這個基本部署範例的主要假設包括：

* 您沒有想要使用雲端 Pod 架構連接到這個新 pod 的內部部署範圍 pod (CPA) 。

* 終端使用者可透過網際網路 (連接到其虛擬桌面，而不是透過內部部署資料中心) 來連接。

在此基本範例中，您可以透過 VPN 或 ExpressRoute 線路，將 Azure 虛擬網路中的 AD 網域控制站與內部部署 Active Directory 連線。

所討論之基本範例的變化，可能是為了支援內部部署資源的連線能力。 這可能是存取桌面的使用者，以及使用 CPA 來產生虛擬桌面應用程式流量或連接到內部部署的範圍 pod。

下圖顯示如何進行此作業。若要將您的公司網路連線到 Azure 虛擬網路，您需要 ExpressRoute。您也需要使用「全球接觸」將您的公司網路與每個私用雲端/SDDCs 連線，以允許從 SDDC 到 ExpressRoute 和內部部署資源的連線能力。

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Azure VMware 解決方案的範圍與 Azure 上的範圍雲端之間的差異" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>跨多個區域的 Azure VMware 解決方案上有多個範圍的 pod

針對另一個水準 pod 範例，讓我們看看在多個 pod 之間顯示縮放比例的範例。在此範例中，您會在兩個不同的區域中部署兩個範圍的 pod，並使用 CPA 進行同盟。網路設定與上一個範例類似，有一些額外的跨區域連結。 

您必須將 Azure 虛擬網路輸入每個區域連線到另一個區域中的私人雲端/SDDCs，讓屬於 CPA 同盟一部分的連線伺服器連線到管理下的所有桌面。將其他私人雲端/SDDCs 新增至此設定，可讓您整體調整為24000個會話。 

雖然此範例顯示多個區域，但如果您想要在相同區域中部署兩個水準 pod，則適用相同的原則。 請注意，您必須確定第二個水準 pod 會部署在 *個別的 Azure 虛擬網路*中。最後，如同您在先前的單一 Pod 範例中所做的一樣，您可以使用客戶 ExpressRoute 和全球接觸來將您的公司網路和內部部署 Pod 連線到此多 Pod/區域範例。

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="Azure VMware 解決方案的範圍與 Azure 上的範圍雲端之間的差異" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>適用于範圍部署的 Azure VMware 解決方案主機大小 

在 Azure VMware 解決方案中執行的主機上，範圍的大小調整方法比在內部部署環境中執行的方式更簡單，因為 Azure VMware 方案主機實例已標準化。 精確的主機大小將有助於判斷支援您的 VDI 需求所需的主機數目，而且是決定每一桌面成本的核心。

### <a name="azure-vmware-solution-host-instance"></a>Azure VMware 解決方案主機實例

* PowerEdge R640 伺服器-DSS 受限

* 36核心 \@ 2.3 ghz

* 576-GB RAM

* HBA330 12 Gbps SAS HBA 控制器 (非 RAID) 

* 1.92 TB SSD SATA 混合在熱插即用的 AG 磁片磁碟機中使用 6 Gbps 512 2.5，3 DWPD，10512 TBW

* Intel 1.6 TB、NVMe、混合使用 Express Flash、2.5 SFF 磁片磁碟機、U 2、P4600 與電訊廠商

* 2個 vSAN 磁片群組： 1.6 x 4 (1.92 TB) 

### <a name="horizon-sizing-inputs"></a>水準調整大小輸入

針對您規劃的工作負載找出下列各項：

* 並行桌面數目

* 每一桌面的必要 vCPU

* 每一桌面的必要 vRAM

* 每個桌面的必要儲存體

一般來說，VDI 部署是 CPU 或 RAM 的限制，因為這些因素會決定主機大小。 讓我們使用下列範例來 LoginVSI 知識工作者類型的工作負載，並以效能測試進行驗證：

* 2000並行桌面部署

* 每一桌面2vCPU。

* 每個桌面 vRAM 4 GB。

* 每一桌面 50 GB 的儲存空間

在此範例中，主機的總總數為18，產生每個主機的 VM 每個主機密度111。

>[!IMPORTANT]
>客戶工作負載會與此 LoginVSI 知識工作者的範例不同。 在規劃部署的過程中，請與您的 VMware EUC Se 合作，以滿足您的特定大小和效能需求。 請務必先使用實際的規劃工作負載來執行您自己的效能測試，再完成主機大小調整並據以調整。

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Azure VMware 解決方案授權範圍 

在 Azure VMware 解決方案上執行範圍的整體成本有四個元件。 

### <a name="azure-vmware-solution-capacity-cost"></a>Azure VMware 解決方案容量成本

如需定價的詳細資訊，請參閱 [Azure VMware 解決方案定價](https://azure.microsoft.com/pricing/details/azure-vmware/) 頁面

### <a name="horizon-licensing-cost"></a>範圍授權成本

有兩個適用于 Azure VMware 解決方案的可用授權，可以是並行使用者 (CCU) 或名為使用者 (NU) ：

* 水準訂用帳戶授權

* 範圍通用訂用帳戶授權

如果只部署在 Azure VMware 解決方案的範圍，以供可預見的未來使用，則請使用「水準訂閱」授權，因為這是較低的成本。

如果在 Azure VMware 解決方案和內部部署環境中部署的範圍，就像是使用嚴重損壞修復使用案例一樣，選擇範圍通用訂用帳戶授權。 通用授權的成本較高，因為它包含內部部署的 vSphere 授權。

請與您的 VMware EUC 銷售小組合作，以根據您的需求判斷範圍授權成本。

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Azure 虛擬網路上的範圍基礎結構 Vm 成本

根據標準部署架構，範圍基礎結構 Vm 是由連線伺服器、UAGs、應用程式磁片區管理員所組成，而且會部署在客戶的 Azure 虛擬網路中。 需要額外的 Azure 原生實例，才能在 Azure 上支援高可用性 (HA) 、Microsoft SQL 或 Microsoft Active Directory (AD) 服務。 以下是以 2000-桌面部署範例為基礎的 Azure 實例清單。 

| 範圍基礎結構元件 | Azure 實例 | 2000-桌面) 所需的實例數 (    | 註解  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| 連接伺服器                | D4sv3          | 2       | *包含1個 HA 實例*             |    
| UAG                              | F2sv2          | 2       | *包含1個 HA 實例*             |
| 應用程式磁片區管理員              | D4sv3          | 2       | *包含1個 HA 實例*             |
| 雲端連接器                  | D4sv3          | 1       |                                          |
| AD 控制器                    | D4sv3          | 2       | *使用 Azure 上的 MSFT AD 服務的選項* |
| MS SQL Database                  | D4sv3          | 2       | *在 Azure 上使用 SQL 服務的選項*     |
| Windows 檔案共用               | D4sv3          |         | *選擇性*                               |

在 \$ 上述範例中，每位使用者每個2000月每位使用者0.36 的基礎結構 VM 成本。 請注意，在2020年6月起，此範例會使用美國東部 Azure 實例定價。 根據區域、選取的選項和時間，您的定價可能會有所不同。
