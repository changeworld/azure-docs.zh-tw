---
title: 'Azure 雲端服務 (延伸支援的常見問題) '
description: 'Azure 雲端服務 (延伸支援的常見問題) '
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 3338f7b6bd418cea2bfdbbcd40692b9342f48cfa
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744355"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Azure 雲端服務 (延伸支援) 常見問題集
本文涵蓋與 Azure 雲端服務 (延伸支援) 相關的常見問題。

## <a name="general"></a>一般

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>雲端服務 (傳統) & 雲端服務 (延伸支援) 的資源名稱為何？
- 雲端服務 (傳統) ： `microsoft.classiccompute/domainnames`
- 雲端服務 (延伸支援) ： `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>有哪些位置可用來部署雲端服務 (延伸支援) ？
雲端服務 (延伸支援) 適用于所有公用雲端區域。

### <a name="how-does-my-quota-change"></a>我的配額如何變更？ 
客戶將需要使用與任何其他 Azure Resource Manager 產品相同的程式來要求配額。Azure Resource Manager 中的配額是區域，每個區域都需要個別的配額要求。

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>為什麼我再也看不到生產環境 & 預備位置？
雲端服務 (延伸支援) 不支援託管服務的邏輯概念，其中包含兩個插槽 (生產 & 預備) 。 每個部署都是獨立的雲端服務 (延伸支援) 部署。 若要測試和預備新的雲端服務版本，請將雲端服務部署 (延伸支援) ，並將它標記為可透過其他雲端服務 (延伸支援的 VIP 交換) 

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>為什麼我無法再建立空的雲端服務？
託管服務名稱的概念不再存在，您無法建立空的雲端服務 (延伸支援) 。

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>雲端服務 (延伸支援) 支援資源健康狀態檢查 (RHC) 嗎？
否，雲端服務 (延伸支援) 不支援資源健康狀態檢查 (RHC) 。

### <a name="how-are-role-instance-metrics-changing"></a>角色實例計量如何變更？
角色實例計量中沒有任何變更。 

### <a name="how-are-web--worker-roles-changing"></a>Web & 背景工作角色如何變更？
Web 角色和背景工作角色的設計、架構或元件沒有任何變更。 

### <a name="how-are-role-instances-changing"></a>角色實例如何變更？
角色實例的設計、架構或元件沒有任何變更。 

### <a name="how-will-guest-os-updates-change"></a>來賓 os 更新會如何變更？
 推出方法沒有任何變更。 雲端服務 (傳統) 和雲端服務 (延伸支援) 將會取得相同的更新。
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>雲端服務 (延伸支援) 支援已停止、已解除配置的狀態嗎？

雲端服務 (延伸支援) 部署僅支援在 Azure 入口網站中顯示為「已停止」的已停止配置狀態。 已停止-不支援已解除配置狀態。 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>雲端服務 (延伸支援) 部署支援在叢集、可用性區域和區域之間進行調整嗎？
雲端服務 (延伸支援) 部署無法跨多個叢集、可用性區域和區域調整。 

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>雲端服務 (傳統) 與雲端服務之間是否有任何定價差異 (延伸支援) ？
雲端服務 (延伸支援) 使用 Azure Key Vault 和基本 (ARM) 公用 IP 位址。需要憑證的客戶必須使用 Azure Key Vault 進行憑證管理 ([深入瞭解](https://azure.microsoft.com/pricing/details/key-vault/) Azure Key Vault 定價。 ) 雲端服務的每個公用 IP 位址 (延伸支援) 會另外收費， ([深入瞭解](https://azure.microsoft.com/pricing/details/ip-addresses/) 公用 IP 位址定價。 )  
## <a name="resources"></a>資源 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>連結至雲端服務的哪些資源 (延伸支援) 部署必須存留在相同的資源群組中？
負載平衡器、網路安全性群組和路由表必須存留在相同的區域和資源群組中。 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>連結至雲端服務的哪些資源 (延伸支援) 部署必須存留在相同區域中？
Key Vault、虛擬網路、公用 IP 位址、網路安全性群組和路由表必須存留在相同的區域中。

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>連結至雲端服務的哪些資源 (延伸支援) 部署必須存留在相同的虛擬網路中？
公用 IP 位址、負載平衡器、網路安全性群組和路由表必須存留在相同的虛擬網路中。 

## <a name="deployment-files"></a>部署檔案 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>如何使用範本來部署或管理我的部署？
您可以使用 REST、PowerShell 和 CLI 以參數形式傳遞範本和參數檔案。 也可以使用 Azure 入口網站上傳。  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>我現在需要維護四個檔案嗎？  (範本、參數、通過、.cscfg) 
範本和參數檔案只用于部署自動化。 如同雲端服務 (傳統) ，您可以先手動建立相依的資源，然後再使用 PowerShell、CLI 命令或透過入口網站（具有現有的經過配置） .cscfg，以手動方式建立相依)  (的資源。

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>我的應用程式在雲端服務上的程式碼變更如何 (延伸支援) 
在 .cspkg 中封裝的應用程式程式碼不需要任何變更。 您現有的應用程式會繼續如之前一樣運作。 


## <a name="migration"></a>遷移

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>雲端服務是否 (延伸支援) 因配置失敗而減少失敗？
否，雲端服務 (延伸支援) 部署系結至雲端服務 (傳統) 等叢集。 因此，如果叢集已滿，配置失敗將繼續存在。 

### <a name="when-do-i-need-to-migrate"></a>何時需要遷移？ 
估計所需的時間和複雜度的遷移取決於某個範圍的變數。 規劃是瞭解工作範圍、封鎖程式和遷移複雜度的最有效步驟。

## <a name="networking"></a>網路功能

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>為什麼無法建立沒有虛擬網路的部署？
虛擬網路是 Azure Resource Manager 上任何部署的必要資源。 雲端服務 (延伸支援) 部署必須存留于虛擬網路內。 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>為什麼我現在看到許多網路資源？ 
在 Azure Resource Manager 中，雲端服務的元件 (延伸支援) 部署會公開為資源，以取得更佳的可見度和改進的控制。 在雲端服務中使用相同類型的資源 (傳統) 不過它們只是隱藏的。 這類資源的其中一個範例是公用 Load Balancer，現在是由平臺自動建立的明確「唯讀」資源

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>適用于雲端服務 (延伸支援) 的子網會有哪些限制？
包含雲端服務 (延伸支援) 部署的子網無法與其他計算產品（例如虛擬機器、虛擬機器擴展集、Service Fabric 等）的部署共用。

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>雲端服務支援哪些 IP 配置方法 (延伸支援) ？
雲端服務 (延伸支援) 支援動態 & 靜態 IP 配置方法。 靜態 IP 位址在 .cscfg 檔案中是以保留 Ip 的形式參考。

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>為什麼我會收到 IP 位址的費用？
客戶需支付雲端服務上 IP 位址使用量的費用 (延伸支援) ，就如同使用者是針對與虛擬機器相關聯的 IP 位址計費一樣。 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>我可以將 DNS 名稱與雲端服務搭配使用 (延伸支援) 嗎？ 
是。 雲端服務 (延伸支援) 也可以提供 DNS 名稱。 使用 Azure Resource Manager 時，DNS 標籤是指派給雲端服務之公用 IP 位址的選擇性屬性。 以 Azure Resource Manager 為基礎的部署的 DNS 名稱格式為 `<userlabel>.<region>.cloudapp.azure.com`

## <a name="certificates--key-vault"></a>憑證 & Key Vault

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>為什麼我需要在雲端服務上管理我的憑證 (延伸支援) ？
雲端服務 (延伸支援) 已採用與其他計算供應專案相同的程式，其中憑證位於客戶管理的金鑰保存庫內。 這可讓客戶完全掌控其秘密 & 憑證。 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>我可以針對所有區域中的所有部署使用一個 Key Vault 嗎？
否。 Key Vault 是區域資源，且客戶在每個區域都需要一個 Key Vault。 不過，一個 Key Vault 可用於給定區域內的所有部署。

## <a name="next-steps"></a>後續步驟
若要開始使用雲端服務 (延伸支援) ，請參閱 [使用 PowerShell 部署雲端服務 (延伸支援) ](deploy-powershell.md)