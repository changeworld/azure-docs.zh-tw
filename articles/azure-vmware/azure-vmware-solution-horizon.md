---
title: Azure VMware 解決方案上的部署範圍
description: 瞭解如何在 Azure VMware 解決方案上部署 VMware 的範圍。
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 6a466aea5cbdf4452a2c46b455932042d920c3b9
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369007"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Azure VMware 解決方案上的部署範圍 

>[!NOTE]
>本檔著重于 VMware 的範圍產品，之前稱為「範圍7」。 範圍是與 Azure 上的雲端範圍不同的解決方案，不過有一些共用元件。 Azure VMware 解決方案的主要優點包括更簡單的調整大小方法，以及將 VMware Cloud Foundation 管理整合到 Azure 入口網站中。

[VMware](https://www.vmware.com/products/horizon.html)的®、虛擬桌面和應用程式平臺，在資料中心執行，並提供簡單且集中式的管理。 它可在任何裝置上隨時隨地提供虛擬桌面和應用程式。 範圍可讓您建立和建立與 Windows 和 Linux 虛擬桌面、遠端桌面伺服器 (RDS) 託管應用程式、桌上型電腦和實體機器之間的連線。

在這裡，我們將特別著重于在 Azure VMware 解決方案上部署的範圍。 如需 VMware 的一般資訊，請參閱集中生產檔：

* [VMware 的範圍為何？](https://www.vmware.com/products/horizon.html)

* [深入瞭解 VMware 範圍](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [範圍參考架構](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

有了 Azure VMware 解決方案的範圍簡介，現在有兩個虛擬桌面基礎結構 (VDI) Azure 平臺上的解決方案。 下圖摘要說明高層級的主要差異。

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Azure 上的 Azure VMware 解決方案和範圍雲端範圍" border="false":::

範圍8版的2006和更新版本支援內部部署和 Azure VMware 解決方案部署。 有一些在內部部署環境中支援但無法在 Azure VMware 解決方案上使用的範圍功能。 也支援範圍生態系統中的其他產品。 如需詳細資訊，請參閱功能同位 [和互通性](https://kb.vmware.com/s/article/80850)。

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>在混合式雲端中部署範圍

當您使用集中式雲端 Pod 架構 (CPA) 來與內部部署和 Azure 資料中心互連時，您可以在混合式雲端環境中部署範圍。 CPA 可擴大您的部署、建立混合式雲端，並供應商務持續性和嚴重損壞修復的冗余。  如需詳細資訊，請參閱 [擴充現有的範圍7環境](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874)。

>[!IMPORTANT]
>CPA 不是延伸的部署;每個水準 pod 都是相異的，而且所有屬於個別 pod 的連接伺服器都必須位於單一位置，並從網路的觀點來執行于相同的廣播網域上。

就像內部部署或私用資料中心一樣，範圍可以部署在 Azure VMware 解決方案私人雲端中。 在下列各節中，我們將討論在內部部署環境和 Azure VMware 解決方案中部署範圍的主要差異。

Azure 私用雲端在概念上與 VMware SDDC 相同，此詞彙通常用於範圍廣泛的檔。 本檔的其餘部分會使用 Azure 私用雲端和 VMware SDDC 可互換的詞彙。

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

每個 Azure 私用雲端和 SDDC 都可以處理4000桌面或應用程式會話，假設：

* 工作負載流量會與 LoginVSI 工作背景工作設定檔對齊。

* 只會考慮通訊協定流量，沒有使用者資料。

* NSX Edge 設定為大。

>[!NOTE]
>您的工作負載設定檔和需求可能會不同，因此結果可能會根據您的使用案例而有所不同。 使用者資料量可能會降低工作負載內容中的規模限制。 調整規模，並據以規劃您的部署。 如需詳細資訊，請參閱「 [適用于範圍部署的 Azure VMware 解決方案主機大小](#size-azure-vmware-solution-hosts-for-horizon-deployments) 」一節中的大小調整指導方針。

假設 Azure 私用雲端和 SDDC 的最大限制，我們建議您在 Azure 虛擬網路內執行 (UAGs) 的水準連線伺服器和 VMware 整合存取閘道的部署架構。 它會將每個 Azure 私用雲端和 SDDC 有效地轉換成一個區塊。 然後，將 Azure VMware 解決方案上執行的範圍擴充性最大化。

從 Azure 虛擬網路到 Azure 私用雲端/SDDCs 的連線，應使用 ExpressRoute FastPath 進行設定。 下圖顯示基本的範圍 pod 部署。

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Azure 上的 Azure VMware 解決方案和範圍雲端範圍" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>在 Azure VMware 解決方案上調整規模範圍的網路連線能力

本節使用一些常見的部署範例，以協助您在 Azure VMware 解決方案上進行大規模調整，以提供更高層級的網路架構。 重點是專門針對重要的網路元素。 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Azure VMware 解決方案上的單一範圍 pod

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Azure 上的 Azure VMware 解決方案和範圍雲端範圍" border="false":::

單一水準 pod 是最直接的部署案例，因為您只會在美國東部區域部署一個範圍的 pod。  由於每個私用雲端和 SDDC 都已預估為處理4000桌面會話，因此您會部署最大範圍的 pod 大小。  您可以規劃最多三個私用雲端/SDDCs 的部署。

透過將基礎結構虛擬機器 (Vm) 部署在 Azure 虛擬網路中，您可以觸達每個水準 pod 的12000會話。 每個私人雲端和 SDDC 與 Azure 虛擬網路之間的連線是 ExpressRoute Fast 路徑。  私用雲端之間不需要任何東西部流量。 

這個基本部署範例的主要假設包括：

* 您沒有想要使用雲端 Pod 架構連接到這個新 pod 的內部部署範圍 pod (CPA) 。

* 終端使用者透過網際網路 (連接到其虛擬桌面，而不是透過內部部署資料中心) 連接。

您可以透過 VPN 或 ExpressRoute 線路，將 Azure 虛擬網路中的 AD 網域控制站連線至內部部署 AD。

基本範例的變化可能是為了支援內部部署資源的連線能力。 例如，使用者可以使用 CPA 存取桌面並產生虛擬桌面應用程式流量，或連接到內部部署的範圍 pod。

此圖顯示如何支援內部部署資源的連線能力。 若要將您的公司網路連線到 Azure 虛擬網路，您需要 ExpressRoute 線路。  您也需要將您的公司網路與每個私用雲端連線，並使用 ExpressRoute 全球接觸來 SDDCs。  它允許從 SDDC 連接到 ExpressRoute 線路和內部部署資源。 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Azure 上的 Azure VMware 解決方案和範圍雲端範圍" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>跨多個區域的 Azure VMware 解決方案上有多個範圍的 pod

另一個案例是在多個 pod 之間調整規模。  在此案例中，您會在兩個不同的區域中部署兩個範圍的 pod，並使用 CPA 進行同盟。 它類似于前一個範例中的網路設定，但有一些額外的跨區域連結。 

您會將 Azure 虛擬網路輸入每個區域連線到另一個區域中的私人雲端/SDDCs。 它可讓 CPA 同盟的範圍內連線伺服器連接到管理下的所有桌面。 將其他私人雲端/SDDCs 新增至此設定，可讓您整體調整為24000個會話。 

如果您在相同區域中部署兩個水準 pod，則適用相同的原則。  請務必在 *不同的 Azure 虛擬網路*中部署第二個範圍的 pod。 就像單一 pod 範例一樣，您可以使用 ExpressRoute 和全球接觸來將您的公司網路和內部部署 pod 連線到此多 pod/區域範例。 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="Azure 上的 Azure VMware 解決方案和範圍雲端範圍" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>適用于範圍部署的 Azure VMware 解決方案主機大小 

在 Azure VMware 解決方案中執行的主機上，範圍的大小調整方法比在內部部署環境中的範圍更簡單。  這是因為 Azure VMware 解決方案主機已標準化。  確切的主機大小有助於判斷支援 VDI 需求所需的主機數目。  它是決定每一桌面成本的核心。

### <a name="azure-vmware-solution-host-instance"></a>Azure VMware 解決方案主機實例

* PowerEdge R640 伺服器-DSS 受限

* 36核心 \@ 2.3 ghz

* 576-GB RAM

* HBA330 12 Gbps SAS HBA 控制器 (非 RAID) 

* 1.92 TB SSD SATA 混合在熱插即用的 AG 磁片磁碟機中使用 6 Gbps 512 2.5，3 DWPD，10512 TBW

* Intel 1.6 TB、NVMe、混合使用 Express Flash、2.5 SFF 磁片磁碟機、U 2、P4600 與電訊廠商

* 2個 vSAN 磁片群組： 1.6 x 4 (1.92 TB) 

### <a name="horizon-sizing-inputs"></a>水準調整大小輸入

以下是您所規劃的工作負載需要收集的內容：

* 並行桌面數目

* 每一桌面的必要 vCPU

* 每一桌面的必要 vRAM

* 每個桌面的必要儲存體

一般來說，VDI 部署是 CPU 或 RAM 限制，可決定主機大小。 讓我們使用下列範例來 LoginVSI 知識工作者類型的工作負載，並以效能測試進行驗證：

* 2000並行桌面部署

* 每一桌面2vCPU。

* 每台桌面 vRAM 4 GB。

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

如果部署在 Azure VMware 解決方案和內部部署上，如同使用嚴重損壞修復使用案例，請選擇範圍通用訂用帳戶授權。 它包含內部部署的 vSphere 授權，因此成本較高。

請與您的 VMware EUC 銷售小組合作，以根據您的需求判斷範圍授權成本。

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Azure 虛擬網路上的範圍基礎結構 Vm 成本

根據標準部署架構，範圍基礎結構 Vm 是由連接伺服器、UAGs、應用程式磁片區管理員所組成。 它們是部署在客戶的 Azure 虛擬網路中。 需要額外的 Azure 原生實例，才能在 Azure 上支援高可用性 (HA) 、Microsoft SQL 或 Microsoft Active Directory (AD) 服務。 資料表會根據 2000-桌面部署範例列出 Azure 實例。 

>[!NOTE]
>若要能夠處理失敗，請部署一部以上的伺服器，而不是 (n + 1) 的連接數目所需的伺服器。 連線伺服器、UAG 和應用程式磁片區管理員的最小建議實例數目為2，而所需的數目將根據環境將支援的使用者數量成長。  單一連線伺服器最多可支援4000個會話，但建議使用2000作為最佳作法。 每個 pod 最多可支援7個連接伺服器，每個 pod 的建議為12000個作用中會話。 如需最新數目的相關資訊，請參閱 vmware [知識庫文章： vmware 趨勢調整限制和建議](https://kb.vmware.com/s/article/2150348)。

| 範圍基礎結構元件 | Azure 實例 | 2000-桌面) 所需的實例數 (    | 註解  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| 連接伺服器                | D4sv3          | 2       | *請參閱上述注意事項*                         |    
| UAG                              | F2sv2          | 2       | *請參閱上述注意事項*                         |
| 應用程式磁片區管理員              | D4sv3          | 2       | *請參閱上述注意事項*                         |
| 雲端連接器                  | D4sv3          | 1       |                                          |
| AD 控制器                    | D4sv3          | 2       | *使用 Azure 上的 MSFT AD 服務的選項* |
| MS SQL Database                  | D4sv3          | 2       | *在 Azure 上使用 SQL 服務的選項*     |
| Windows 檔案共用               | D4sv3          |         | *選擇性*                               |

在 \$ 上述範例中，每位使用者每個2000月每位使用者0.36 的基礎結構 VM 成本。 此範例會使用美國東部 Azure 實例2020年6月定價。 根據區域、選取的選項和時間，您的定價可能會有所不同。
