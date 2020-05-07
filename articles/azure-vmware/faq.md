---
title: 常見問題集
description: 提供一些有關 Azure VMware 解決方案（AVS）常見問題的解答。
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 6d8e87dd52871b82109ccc794af04244efe95b06
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854593"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-avs-preview"></a>Azure VMware 解決方案（AVS）預覽的相關常見問題

有關 Azure VMware 解決方案（AVS）常見問題的解答。

## <a name="general"></a>一般

**什麼是 Azure VMware 解決方案 (AVS)？**

隨著企業採用 IT 現代化策略來提升業務靈活性、降低成本並加速創新，混合式雲端平臺也已成為客戶數位轉型的關鍵實現。 AVS 結合 VMware 的軟體定義資料中心（SDDC）軟體與 Microsoft Azure 的全球雲端服務生態系統。 可管理 AVS 解決方案，以符合效能、可用性、安全性和合規性需求。

## <a name="avs-service"></a>AVS 服務

**什麼是目前可使用的 AVS？**

在預覽期間，適用于美國東部的北美洲，以及西歐的阿姆斯特丹。

**在 Azure VMware 解決方案（AVS）實例中執行的工作負載是否可以使用或與 Azure 服務整合？**

所有 Azure 服務都可供 AVS 解決方案客戶使用。 特定服務的效能和可用性限制必須以案例為基礎來解決。

**我是否使用現在用來管理私人雲端資源的相同工具？**

可以。 Azure 入口網站用於部署和一些管理作業。 vCenter 和 NSX Manager 可用來管理 vSphere 和 NSX-T 資源。

**我可以使用我的內部部署 vCenter 來管理私人雲端嗎？**

在啟動時，AVS 不支援跨內部部署和私人雲端環境的單一管理體驗。 私人雲端叢集將會使用 vCenter 和在私人雲端的本地管理器本機來管理。

**我可以使用執行于內部部署的 vRealize Suite 嗎？** 

特定的整合和使用案例可能會以個案為基礎進行評估。

**我是否可以將 vSphere Vm 從內部部署環境遷移至 AVS 私人雲端？**

可以。 如果符合標準的跨 vCenter [vMotion 需求] [https://kb.vmware.com/s/article/210695]，則可以使用 vm 遷移和 VMotion 將 vm 移至私人雲端。

**內部部署環境中是否需要特定版本的 vSphere？**

由於所有雲端環境都隨附 HCX、vSphere 5.5 或更新版本，適用于 vMotion 的內部部署環境。

**變更控制流程看起來像什麼？**

對服務本身進行的更新將遵循 Microsoft Azure 的標準變更管理流程。 客戶須負責任何工作負載管理工作和相關聯的變更管理流程。

**這與 Azure VMware Solution by CloudSimple 有何不同？**

透過新的 Azure VMware 解決方案，Microsoft 和 VMware 具有直接的雲端提供者合作關係。 新解決方案完全是由 Microsoft 所設計、建立及支援，並由 VMware 背書。 在架構上，解決方案是一致的，而且 VMware 技術堆疊會在 Azure 專用基礎結構上執行。

**如果我是現有的 Azure VMware 解決方案客戶，此預覽對我有何意義？**

CloudSimple 不會對現有的 Azure VMware 解決方案進行任何變更。 我們會繼續在 Azure 上支援解決方案。 Azure VMware 解決方案受我們的服務等級協定[（SLA）](https://aka.ms/CSVMwareSLA)支援。 客戶應該繼續針對生產環境工作負載使用此服務;這是由[Microsoft 的服務條款](https://azure.microsoft.com/support/legal/)所控管的可用解決方案。

**我可以透過 CloudSimple 將 Azure VMware 解決方案遷移到這個新解決方案嗎？**

是的，Azure VMware 解決方案支援使用熟悉的 VMware 工具（例如 HCX）來進行遷移。 對於想要遷移至新解決方案的客戶，請與您的 Microsoft 帳戶小組合作，以探索選項和可用的支援。

## <a name="compute-network-and-storage"></a>計算、網路和儲存體

**是否有一種以上的主機可供使用？**

只有一種可用的主機。

**每種主機類型的 CPU 規格為何？**

伺服器有兩個雙18核心 2.3 GHz Intel Cpu。

**每部主機中有多少記憶體？**

伺服器具有 576 GB 的 RAM。

**每部主機的儲存容量為何？**

每個 ESXi 主機有兩個 VSAN diskgroups，其容量層級為 15.2 TB，而 3.2 TB NVMe 快取層（每個 diskgroup 中 1.6 TB）。

**每部 ESXi 主機中有多少網路頻寬可用？**

ESXi 主機支援最多 25 Gbps 的連接頻寬。

**資料是否儲存在待用時加密的 VSAN 資料存放區上？**

是，所有 VSAN 資料預設都會使用儲存在 Azure Key Vault 中的金鑰進行加密。

## <a name="hosts-clusters-and-private-clouds"></a>主機、叢集和私人雲端

**基礎結構是否共用？**

否，私用雲端主機和叢集是專用的，而且會在使用前後安全地清除。

**每個叢集的主機數目下限和上限為何？**

叢集可以在3到16個 ESXi 主機之間調整。 試用叢集限制為三部主機。

**我可以調整私用雲端叢集嗎？**

是，叢集會在 ESXi 主機的最小和最大數目之間進行調整。 試用叢集限制為三部主機。

**什麼是試用叢集？**

試用叢集是三個主機叢集，用於一個月的 AVS 私人雲端評估。

**我可以針對試用叢集使用高階主機嗎？**

不需要。 高階 ESXi 主機已保留供生產叢集使用。

## <a name="avs-and-vmware-software"></a>AVS 和 VMware 軟體

**哪些版本的 VMware 軟體用於私人雲端？**

私用雲端使用 vSphere 6.7、vSAN 6.7、HCX 和版本2.5 的 NSX-T。  

**私用雲端使用 VMware NSX 嗎？**

是，在 AVS 私人雲端中，會使用 NSX-T 2.5 作為軟體定義的網路功能。

**我可以在私人雲端中使用 VMware NSX-V 嗎？**

不需要。 NSX-T 是唯一支援的 NSX 版本。

**在內部部署環境或連線到私人雲端的網路中，需要有 NSX。**

否，您不需要在內部部署使用 NSX。

**在私人雲端中，VMware 軟體的升級和更新排程為何？**

私用雲端軟體配套升級的目的，是要將軟體保留在最新版本的軟體組合中，VMware 的一版。 私人雲端軟體版本可能與個別軟體元件的最新版本（ESXi、NSX-T、vCenter、VSAN）不同。

**私人雲端軟體堆疊的更新頻率為何？**

私用雲端軟體會依照排程進行升級，並隨著 VMware 的軟體配套發行而進行追蹤。 您的私用雲端不需要停機就能進行升級。

## <a name="connectivity"></a>連線能力

**若要將私人雲端與內部部署環境結合，需要何種網路 IP 位址規劃？**

部署 AVS 私人雲端需要私人網路/22 位址空間。 此私人位址空間不應與訂用帳戶中的其他虛擬網路或內部部署網路重迭。
 
**如何? 從內部部署環境連接到 AVS 私人雲端？**

您可以透過下列兩種方法的其中一種連接到服務： 

- 將 VM 或應用程式閘道部署在透過 ExpressRoute 對等互連至私人雲端的 Azure 虛擬網路上。
- 透過 ExpressRoute Global，從內部部署資料中心到 Azure ExpressRoute 線路。

**如何? 將工作負載 VM 連線到網際網路或 Azure 服務端點？**

在 [Azure 入口網站中，啟用私人雲端的網際網路連線能力。 使用 [NSX-T 管理員]，建立 NSX-T T1 路由器和邏輯交換器。 接著，您可以使用 vCenter 在邏輯交換器所定義的網路區段上部署 VM。 該 VM 將可存取網際網路和 Azure 服務的網路。

**我是否需要限制從網際網路到私人雲端中邏輯網路上 Vm 的存取權？**

不需要。 不允許從網際網路對私人雲端直接輸入的網路流量。

**我是否需要限制從邏輯網路上的 Vm 到網際網路的網際網路存取？**

可以。 您必須使用 NSX-T manager 來建立防火牆，以限制 VM 對網際網路的存取。

## <a name="accounts-and-privileges"></a>帳戶和許可權

**我的新的 AVS 私人雲端會有哪些帳戶和許可權？**

系統會為您提供 vCenter 中 cloudadmin 使用者的認證，以及在 NSX-T Manager 上的系統管理員存取權。 另外還有一個 CloudAdmin 群組可用於合併 Azure Active Directory。 如需詳細資訊，請參閱[存取和身分識別概念](concepts-identity.md)。

**可以具有 ESXi 主機的系統管理員存取權嗎？**

否，ESXi 的系統管理員存取許可權制為符合解決方案的安全性需求。

**VCenter 有哪些許可權和許可權？**

您將擁有 CloudAdmin 群組許可權。 如需詳細資訊，請參閱[存取和身分識別概念](concepts-identity.md)。

**有哪些許可權和許可權會在 NSX-T 管理員上？**

您會在 NSX-T 上擁有完整的系統管理員許可權，而且可以管理角色型存取控制，如同您在內部部署的資料中心。 如需詳細資訊，請參閱[存取和身分識別概念](concepts-identity.md)。

> [!NOTE]
> 建立一個 T0 路由器，並將其設定為私人雲端部署的一部分。 對該邏輯路由器或 NSX-T 邊緣節點 Vm 所做的任何修改，可能會影響到您私人雲端的連線能力。

## <a name="billing-and-support"></a>帳單與支援

**在您的 AVS 預覽期間，將如何計費**

在預覽期間，每月會以隨用隨付的方式計費 AVS。 公開上市時，將會提供其他選項。

**如何在 AVS 預覽期間將定價結構化？**

如需有關定價的一般問題，請參閱 Azure VMware 解決方案[定價](https://azure.microsoft.com/pricing/details/azure-vmware)頁面。 預覽定價適用于要求，請洽詢您的帳戶小組，或遵循定價頁面上的連結來聯絡銷售。

**誰支援 AVS？**

對 AVS 的支援是由 Microsoft 所提供。 請注意，根據我們的預覽方針，我們將在星期一到星期五的上午9點到下午5點的 PST 上班時間提供支援。 您可以從此[連結](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)提出支援票證

**我需要哪些帳戶才能建立 AVS 私人雲端？**

您將需要 azure 訂用帳戶中的 Azure 帳戶。

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
