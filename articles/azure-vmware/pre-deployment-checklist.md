---
title: Azure VMware 解決方案預先部署檢查清單
description: 請使用此檢查清單作為預先部署規劃程式的一部分。
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 51480f72d6de6b1f49aa753e006c4683bbd48cc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579637"
---
# <a name="azure-vmware-solution-pre-deployment-checklist"></a>Azure VMware 解決方案預先部署檢查清單
您將在 [規劃階段](production-ready-deployment-steps.md)使用此預先部署檢查清單。

## <a name="success-criteria"></a>成功準則
定義您要執行的測試和預期的結果。

| 所需的基本資訊 | 您的回應 |
| ----------- | ------------- |
| 部署 SDDC | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 建立虛擬網路 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 建立跳躍箱 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 建立虛擬網路閘道 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 連接 ExpressRoute 全球存取範圍 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| 登入 NSX Manager 和 vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| 需要主要資訊 | 您的回應 |
| --------| --------------|
| 建立 DHCP 伺服器 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 建立網路區段 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 調整 (新增或刪除節點)  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 部署 VMware HCX | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 建立虛擬機器 (Vm)  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| 啟用網際網路 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 從內部部署 VM 遷移至私人雲端 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| 需要的其他資訊 | 您的回應 |
| --------| --------------|
| VM 快照集作業 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| 部署 NSX-T 負載平衡器 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Azure 整合<br><ul><li>共用內容庫</li><li>安全性整合</li><li>上傳 ISO</li><li>從 ISO 建立</li><li>Azure 備份</li></ul> | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 微分割 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 路由 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |




## <a name="azure-vmware-solution-information"></a>Azure VMware 解決方案資訊

#### <a name="azure-subscription"></a>Azure 訂用帳戶
提供 Azure VMware 解決方案的訂用帳戶名稱和訂用帳戶識別碼。 訂用帳戶可以是新的或現有的訂用帳戶。 請勿使用開發/測試訂用帳戶。

| 所需的資訊  | 您的回應 |
| ------------| ------------- |
| 訂用帳戶和識別碼 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 具有 EA 的訂用帳戶 | ☐是 &nbsp; &nbsp; ☐否  |
| 資源群組名稱 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Location | ☒美國東部 |
| Azure 系統管理員<br><br>提供系統管理員的名稱和連絡人<br>指派為從 marketplace 啟用服務。<br>參與者是所需的最小角色 <br>[註冊 Azure VMware 解決方案資源提供者](tutorial-create-private-cloud.md#register-the-resource-provider)。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="on-premises-vmware-information"></a>內部部署 VMware 資訊

| 所需的資訊  | 您的回應 |
| ------------| --------------|
| vSphere 版本 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| vCenter 版本 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vCenter 系統管理員 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| L2 擴充功能<br><br>如果使用 VMware HCX 擴充 L2 網路，<br>提供您將擴充的內部部署網路。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vSwitch<br><br>提供整個環境所使用的 vSwitch 類型。 | ☐標準 &nbsp; &nbsp; ☐分散式<br><br>如果使用標準版，則無法使用 VMware HCX。 |
| DNS 和 DHCP<br><br>需要適當的 DNS 和 DHCP 基礎結構。 <br>建議使用內建的 DHCP 服務 <br>NSX 或使用私人雲端中的本機 DHCP 伺服器 <br>與其路由傳送廣播 DHCP 流量 <br>WAN 回內部部署。 如需詳細資訊， <br>瞭解 [如何在 Azure VMWare 解決方案中建立和管理 DHCP](manage-dhcp.md)。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---azure-vmware-solution-infrastructure"></a>網路-Azure VMware 解決方案基礎結構 
所需的資料是協助您符合 Azure VMware 解決方案的網路需求，以進行詳細的初始網路測試。 

| 所需的資訊 | 您的回應 |
| ----------- | ------------- |
|Azure VMware 解決方案 CIDR<br><br>VSphere 主機、vSAN 和管理的必要元件 <br>Azure VMware 解決方案中的網路。 深入瞭解 <br>資訊，請參閱 [路由和子網考慮](tutorial-network-checklist.md#routing-and-subnet-considerations)。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Azure VMware 解決方案工作負載 CIDR (選擇性) <br><br>指派要在 Azure VMware 中使用的網路<br>初始測試的解決方案。 虛擬機器<br>將會部署以驗證網路連線能力 <br>從/到 Azure VMware 解決方案。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---connect-azure-vmware-solution-to-azure-virtual-network"></a>網路-將 Azure VMware 解決方案連線到 Azure 虛擬網路
勇敢面對考驗 Azure VMware 解決方案叢集之後所需的資料，可透過 ExpressRoute 線路連接到 Azure，這是 Azure VMware 解決方案服務的一部分。

| 所需的資訊 | 您的回應 |
| ------------------ | ------------- |
| 跳箱-Windows Server | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 建立虛擬網路 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 建立閘道子網路 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 建立虛擬網路閘道<br><br>如需詳細資訊，請參閱 [建立虛擬網路閘道](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---connect-azure-vmware-solution-to-an-on-premises-datacenter"></a>網路-將 Azure VMware 解決方案連線至內部部署資料中心

| 所需的資訊 | 您的回應 |
| ------------------ | ------------- |
| ExpressRoute 對等互連 CIDR<br><br>`/29`CIDR 位址區塊。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| ExpressRoute 授權金鑰和資源識別碼<br><br>提供授權金鑰和資源識別碼， <br>從目前的 ExpressRoute 產生的 <br>連接至內部部署資料中心的線路。  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 預設路由的方向<br><br>Azure VMware 解決方案中的虛擬機器 <br>透過提供的 Azure VMware 解決方案存取網際網路 <br>網際網路，或回到 ExpressRoute 線路 <br>預設路由的內部部署？ | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| 與服務通訊所需的網路連接埠<br><br>如需詳細資訊，請參閱 [必要的網路埠](tutorial-network-checklist.md#required-network-ports)。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---vmware-hcx"></a>網路-VMware HCX

| 所需的資訊 | 您的回應 |
| ------------------ | ------------- |
| 網路連接埠<br><br>如果有防火牆，請確定所需的網路埠 <br>會在內部部署和 Azure VMware 解決方案之間開啟。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| DNS<br><br>如需設定 DNS 的相關資訊， <br>請參閱 [網路-Azure VMware 解決方案基礎結構](#networking---azure-vmware-solution-infrastructure)。 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VMware HCX Cidr<br><br>您需要兩個 `/29` CIDR 區塊，用於 <br>VMware HCX 基礎結構元件內部部署。  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |

