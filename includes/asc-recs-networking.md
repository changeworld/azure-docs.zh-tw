---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 01/12/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: eb4cceeb5b151197d6f52b45a0ea2a1913cefe55
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133025"
---
此類別中有 **15** 項建議。

|建議 |描述 |嚴重性 |
|---|---|---|
|應限制存取具防火牆與虛擬網路設定的儲存體帳戶 |檢閱儲存體帳戶防火牆設定中的網路存取設定。 建議您改為設定網路規則，只有來自允許網路的應用程式才能存取儲存體帳戶。 若要允許來自特定網際網路或內部部署用戶端的連線，可將存取權授與來自特定 Azure 虛擬網路的流量，或授與公用網際網路 IP 位址範圍。<br />(相關原則：[儲存體帳戶應限制網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f34c877ad-507e-4c82-993e-3452a6e0ad3c)) |低度 |
|應在內部對應虛擬機器中套用自適性網路強化建議 |Azure 資訊安全中心分析了下列虛擬機器的網際網路流量通訊模式，並判斷與其建立關聯的 NSG 中現有規則過於寬鬆，導致潛在的受攻擊面增加。 這可能是因為連接埠/通訊協定元組上缺少流量，或是「資訊安全中心」威脅情報來源將特定 IP 標示為惡意。<br />(無相關原則) |中 |
|應在網際網路對應的虛擬機器上套用自適性網路強化建議 |Azure 資訊安全中心分析了下列虛擬機器的網際網路流量通訊模式，並判斷與其建立關聯的 NSG 中現有規則過於寬鬆，導致潛在的受攻擊面增加。<br>當此 IP 位址未定期與此資源通訊時，就會發生這種情況。 或者，也有可能是資訊安全中心的威脅情報來源已將 IP 位址標示為惡意。 <a href="https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening">深入了解</a><br />(相關原則：[應在網際網路對應的虛擬機器上套用自適性網路強化建議](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f08e6af2d-db70-460a-bfe9-d5bd474ba9d6)) |高 |
|所有網路連接埠均應限制在與虛擬機器建立關聯的網路安全性群組 |Azure 資訊安全中心發現您某些網路安全性群組的輸入規則過於寬鬆。 輸入規則不應允許來自「任何」或「網際網路」範圍的存取。 這可能會讓攻擊者鎖定您的資源。<br />(相關原則：[所有網路連接埠均應限制在與虛擬機器建立關聯的網路安全性群組](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f9daedab3-fb2d-461e-b861-71790eead4f6)) |高 |
|應啟用 Azure DDoS 保護標準 |資訊安全中心發現虛擬網路具有未受到 DDoS 保護服務所保護的應用程式閘道資源。 這些資源包含公用 IP。 降低網路大流量攻擊和通訊協定攻擊的風險。<br />(相關原則：[應啟用 Azure DDoS 保護標準](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa7aca53f-2ed4-4466-a25e-0b45ade68efd)) |中 |
|應使用網路安全性群組保護網際網路對應的虛擬機器 |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的 VM 遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕網路流量從相同子網路內部或外部的其他執行個體流入您的 VM。<br>請注意，為了盡可能保護您的機器，您必須限制 VM 對網際網路的存取，並在子網路上啟用 NSG。<br>具有「高」嚴重性的 VM 都是面向網際網路的 VM。<br />(相關原則：[應使用網路安全性群組保護網際網路對應的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c)) |高 |
|應停用虛擬機器上的 IP 轉送 |Azure 資訊安全中心發現您的部分虛擬機器上已啟用 IP 轉送。 在虛擬機器的 NIC 上啟用 IP 轉送可讓機器接收傳送到其他目的地的流量。 IP 轉送是極少需要的功能 (例如，當將 VM 作為網路虛擬設備使用時)，因此，這應經過網路安全性小組檢閱。<br />(相關原則：[應停用虛擬機器上的 IP 轉送](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fbd352bd5-2853-4985-bf0d-73806b4a5744)) |中 |
|應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠 |Azure 資訊安全中心發現您網路安全性群組中用於管理連接埠的一些輸入規則過於寬鬆。 請啟用 Just-In-Time 控制，以保護您的 VM 免於遭受網際網路型的暴力密碼破解攻擊。 <a href="https://docs.microsoft.com/azure/security-center/security-center-just-in-time">深入了解。</a><br />(相關原則：[應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fb0f33259-77d7-4c9e-aac6-3aabcfae693c)) |高 |
|應關閉虛擬機器上的管理連接埠 |開放的遠端管理連接埠會使您的 VM 暴露在網際網路攻擊的高風險之下。 這些攻擊會嘗試對認證發動暴力密碼破解攻擊，來取得機器的系統管理員存取權。<br />(相關原則：[應關閉虛擬機器上的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f22730e10-96f6-4aac-ad84-9383d35b5917)) |中 |
|應在 Linux 虛擬機器上安裝網路流量資料收集代理程式 |資訊安全中心會使用 Microsoft Dependency Agent 從您的 Azure 虛擬機器收集網路流量資料，以啟用進階網路保護功能，例如網路地圖上的流量視覺效果、網路強化建議與特定的網路威脅。<br />(相關原則：[應在 Linux 虛擬機器上安裝網路流量資料收集代理程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f04c4380f-3fae-46e8-96c9-30193528f602)) |中 |
|應在 Windows 虛擬機器上安裝網路流量資料收集代理程式 |資訊安全中心會使用 Microsoft Dependency Agent 從您的 Azure 虛擬機器收集網路流量資料，以啟用進階網路保護功能，例如網路地圖上的流量視覺效果、網路強化建議與特定的網路威脅。<br />(相關原則：[應在 Windows 虛擬機器上安裝網路流量資料收集代理程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2f2ee1de-44aa-4762-b6bd-0893fc3f306d)) |中 |
|應使用網路安全性群組保護非網際網路對應的虛擬機器 |使用網路安全性群組 (NSG) 限制非網際網路面向虛擬機器的存取，藉此遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕網路流量從其他執行個體 (無論是否位在相同子網路中) 流入您的 VM。<br>請注意，為了盡可能保護您的機器，您必須限制 VM 對網際網路的存取，並在子網路上啟用 NSG。<br />(相關原則：[應使用網路安全性群組保護非網際網路面向的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fbb91dfba-c30d-4263-9add-9c2384e659a6)) |低度 |
|應啟用儲存體帳戶的安全傳輸 |安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持。<br />(相關原則：[應啟用儲存體帳戶的安全傳輸](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f404c3081-a854-4457-ae30-26a93ef643f9)) |高 |
|子網路應該與網路安全性群組建立關聯 |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的子網路遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路的網路流量。 當 NSG 與子網路相關聯時，ACL 規則會套用至該子網路中的所有 VM 執行個體和整合服務，但不會套用至子網路內的內部流量。 若要防止相同子網路中的資源受到其他子網路的威脅，請直接在資源上啟用 NSG。<br />(相關原則：[子網路應該與網路安全性群組建立關聯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fe71308d3-144b-4262-b144-efdc3cc90517)) |低度 |
|虛擬網路應該受到 Azure 防火牆的保護 |某些虛擬網路未受到防火牆保護。 使用 Azure 防火牆來限制對虛擬網路的存取並防止潛在的威脅。 若要深入了解 Azure 防火牆， <br> 請按一下<a href="https://azure.microsoft.com/pricing/details/azure-firewall">這裡</a><br />(相關原則：[所有網際網路流量都應透過您部署的 Azure 防火牆路由](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ffc5e4038-4584-4632-8c85-c0448d374b2c)) |低度 |
|||
