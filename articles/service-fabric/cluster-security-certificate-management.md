---
title: Service Fabric 叢集中的憑證管理
description: 瞭解如何在使用 x.509 憑證保護的 Service Fabric 叢集中管理憑證。
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: ecdeb5c9e30c176e2f3525f8efeb861d9210b202
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196240"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Service Fabric 叢集中的憑證管理

本文說明用來保護 Azure Service Fabric 叢集通訊的憑證管理層面，並補充[Service Fabric 叢集安全性](service-fabric-cluster-security.md)的簡介，以及[Service Fabric 中以 x.509 憑證為基礎的驗證](cluster-security-certificates.md)說明。 我們假設讀者熟悉基本的安全性概念，以及 Service Fabric 公開來設定叢集安全性的控制項。  

此標題底下涵蓋的層面：

* 「憑證管理」到底是什麼？
* 涉及憑證管理的角色和實體
* 憑證的旅程
* 深入探討範例
* 疑難排解和常見問題

但首先是免責聲明：本文會嘗試將理論端與實際操作範例配對，這需要詳細的服務、技術等等。 因為物件的相當大部分是 Microsoft 內部，所以我們會參考 Microsoft Azure 特定的服務、技術和產品。 歡迎您在 [批註] 區段中詢問說明或指引，其中 Microsoft 特定的詳細資料不適用於您的案例。

## <a name="defining-certificate-management"></a>定義憑證管理
如我們在[隨附文章](cluster-security-certificates.md)中所見，憑證是一種密碼編譯物件，基本上是以描述它所代表之實體的屬性來系結非對稱金鑰組。 不過，它也是「壞」物件，因為它的存留期是有限的，而且容易遭受危害-意外洩漏或成功的入侵可能會使憑證無法從安全性的觀點來呈現。 這表示需要變更憑證，不論是定期或為了回應安全性事件。 管理的另一個層面（而且本身就是整個主題）就是憑證私密金鑰的保護，或是保護與採購和布建憑證相關之實體身分識別的秘密。 我們指的是用來取得憑證，以及安全（和安全地）將它們傳輸至其「憑證管理」所需位置的程式和程式。 某些管理作業（例如註冊、原則設定和授權控制）已超出本文的範圍。 還有其他專案（例如布建、更新、重新加密或撤銷）只會與 Service Fabric 相關，不過，我們會在這裡討論一些程度，因為瞭解這些作業有助於適當保護一個叢集。 

其目標是盡可能將憑證管理自動化，以確保叢集的可用性不會中斷，並提供安全性保證，因為此程式是使用者自由觸控的。 目前在 Azure Service Fabric 叢集中可達成此目標;本文的其餘部分將會先解構憑證管理，而稍後會將焦點放在啟用 autorollover。

具體而言，範圍中的主題包括：
  - 在管理憑證的內容中，與擁有者和平臺之間的歸屬分隔相關的假設
  - 從發行到耗用量之憑證的長管線
  - 憑證輪替-原因、做法和時機
  - 可能有什麼錯誤？

保護/管理功能變數名稱、註冊憑證，或設定授權控制以強制執行憑證的方面，都不在本文的討論範圍內。 請參閱您最愛的公開金鑰基礎結構（PKI）服務的註冊授權單位（RA）。 Microsoft-內部取用者：請與 Azure 安全性聯繫。

## <a name="roles-and-entities-involved-in-certificate-management"></a>涉及憑證管理的角色和實體
Service Fabric 叢集中的安全性方法是「叢集擁有者宣告它，Service Fabric 執行時間強制執行」的情況。 這表示，幾乎沒有任何參與叢集功能之身分識別的憑證、金鑰或其他認證會來自服務本身;它們全都由叢集擁有者宣告。 此外，叢集擁有者也會負責將憑證布建到叢集、視需要更新它們，以及確保憑證的安全性。 更明確地說，叢集擁有者必須確定：
  - 在叢集資訊清單的 NodeType 區段中宣告的憑證，可以根據[呈現規則](cluster-security-certificates.md#presentation-rules)在該類型的每個節點上找到
  - 上述宣告的憑證會安裝內含其對應的私密金鑰
  - 在展示規則中宣告的憑證應通過[驗證規則](cluster-security-certificates.md#validation-rules) 

Service Fabric，針對其部分，會假設下列各項的責任：
  - 尋找/尋找符合叢集定義中宣告的憑證  
  - 以「需求」為基礎，將對應私密金鑰的存取權授與 Service Fabric 控制的實體
  - 根據已建立的安全性最佳做法和叢集定義嚴格驗證憑證
  - 在憑證即將到期時引發警示，或執行憑證驗證的基本步驟失敗
  - 驗證（對某種程度）主機的基礎設定符合叢集定義的憑證相關層面 

這是因為憑證管理的負擔（作用中的作業）只會落在叢集擁有者上。 在下列各節中，我們將深入探討每個管理作業，並提供可用的機制及其對叢集的影響。

## <a name="the-journey-of-a-certificate"></a>憑證的旅程
讓我們快速重新審視憑證的進展，使其從發行到 Service Fabric 叢集內容中的耗用量：

  1. 網域擁有者會向 PKI 的 RA 註冊網域或主旨，而他們想要與後續的憑證產生關聯;接著，憑證會構成所稱網域或主旨的擁有權證明
  2. 網域擁有者也會在 RA 中指定授權要求者的身分識別，這些實體有權向指定的網域或主旨申請憑證的註冊;在 Microsoft Azure 中，預設身分識別提供者為 Azure Active Directory，而授權的要求者則是由其對應的 AAD 身分識別（或透過安全性群組）所指定
  3. 授權的要求者接著會透過密碼管理服務向憑證註冊;在 Microsoft Azure 中，選擇的 SMS 是 Azure Key Vault （AKV），它會安全地儲存並允許依授權的實體來抓取秘密/憑證。 AKV 也會依據相關聯的憑證原則中的設定，更新/重新建立憑證的金鑰。 （AKV 使用 AAD 做為身分識別提供者）。
  4. 授權的取得器-我們將稱之為「布建代理程式」-從保存庫抓取憑證（包含其私密金鑰），並將它安裝在裝載叢集的電腦上。
  5. Service Fabric 服務（在每個節點上提高許可權執行）會將憑證的存取權授與給允許的 Service Fabric 實體;這些是由本機群組指定，並在 ServiceFabricAdministrators 與 ServiceFabricAllowedUsers 之間分割
  6. Service Fabric 執行時間會存取並使用憑證來建立同盟，或向已授權用戶端的輸入要求進行驗證
  7. 布建代理程式會監視保存庫憑證，並在偵測到更新時觸發布建流程;接著，叢集擁有者會視需要更新叢集定義，以指示要變換憑證的意圖。
  8. 布建代理程式或叢集擁有者也會負責清除/刪除未使用的憑證
  
基於我們的目的，上述順序中的前兩個步驟主要不相關;唯一的連接是憑證的主體一般名稱是在叢集定義中宣告的 DNS 名稱。

這些步驟如下所示：請注意，分別在指紋和一般名稱宣告的憑證之間提供差異。

*圖1。* 指紋所宣告之憑證的發行和布建流程。
![布建指紋所宣告的憑證][Image1]

*圖2。* 主體一般名稱所宣告之憑證的發行和布建流程。
![布建由主體一般名稱宣告的憑證][Image2]

### <a name="certificate-enrollment"></a>憑證註冊
本主題將在 Key Vault[檔](../key-vault/create-certificate.md)中詳細說明;我們在此包含概要，以提供持續性和更輕鬆的參考。 繼續使用 Azure 做為內容，並使用 Azure Key Vault 做為秘密管理服務，授權的憑證要求者必須至少擁有保存庫擁有者的憑證管理許可權（由保存庫擁有者授與）;然後，要求者會註冊為憑證，如下所示：
    - 在 Azure Key Vault （AKV）中建立憑證原則，這會指定憑證的網域/主旨、所需的簽發者、金鑰類型和長度、預定的金鑰使用方式等等;如需詳細資訊，請參閱[Azure Key Vault 中的憑證](../key-vault/certificate-scenarios.md)。 
    - 在相同的保存庫中，使用上述指定的原則建立憑證;接著，這會產生金鑰組做為保存庫物件、使用私密金鑰簽署的憑證簽署要求，然後轉送到指定的簽發者以進行簽署
    - 當簽發者（憑證授權單位單位）以簽署的憑證回復後，結果會合並到保存庫中，而憑證可供下列作業使用：
      - 在 {vaultUri}/certificates/{name} 之下：憑證，包括公開金鑰和中繼資料
      - 在 {vaultUri}/keys/{name}：憑證的私密金鑰，可用於密碼編譯作業（換行/解除包裝、簽署/驗證）
      - 在 {vaultUri}/secrets/{name}：包含其私密金鑰的憑證（可供下載為未受保護的 pfx 或 pem 檔案），回想一下保存庫憑證（事實上是一種依時間排列的憑證實例）共用原則。 將會根據原則的存留期和更新屬性來建立憑證版本。 強烈建議保存庫憑證不共用主體或網域/DNS 名稱;叢集可能會造成干擾，以從不同的保存庫憑證布建憑證實例，但使用相同的主旨，但有不同的其他屬性，例如簽發者、金鑰使用方式等。

此時，保存庫中有憑證存在，可供使用。 向前到：

### <a name="certificate-provisioning"></a>憑證提供
我們提到「布建代理程式」，這是從保存庫抓取憑證的實體（包含其私密金鑰），並將它安裝在叢集的每個主機上。 （回想一下，Service Fabric 不會布建憑證）。在我們的內容中，叢集會裝載在 Azure Vm 和/或虛擬機器擴展集的集合上。 在 Azure 中，您可以使用下列機制來達成從保存庫到 VM/VMSS 的憑證：如上所述，布建代理程式先前已由保存庫擁有者授與保存庫的「取得」許可權。。 
  - 臨機操作：操作員從保存庫（pfx/PKCS #12 或 pem）抓取憑證，並將它安裝在每個節點上
  - 在部署期間作為虛擬機器擴展集的「秘密」：計算服務會使用其第一個合作物件身分識別來代表操作員、從範本部署啟用的保存庫執行憑證，並將它安裝在虛擬機器擴展集的每個節點上（[如下所示](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)）;請注意，這只會提供已設定版本的秘密
  - 使用[Key Vault 的 VM 擴充](../virtual-machines/extensions/key-vault-windows.md)功能;這可讓您使用不限版本的宣告來布建憑證，定期重新整理觀察到的憑證。 在此情況下，VM/VMSS 預期會有[受控識別](../virtual-machines/windows/security-policy.md#managed-identities-for-azure-resources)，此身分識別已被授與包含所觀察憑證之保存庫的存取權。

基於多種原因，不建議使用臨機操作機制，範圍從安全性到可用性，而且不會進一步討論。如需詳細資訊，請參閱[虛擬機器擴展集中的憑證](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)。

VMSS/Compute-based 布建提供安全性和可用性優勢，但也會提供限制。 它需要-藉由設計-將憑證宣告為已建立版本的秘密，這僅適用于使用指紋宣告的憑證保護的叢集。 相反地，Key Vault VM 擴充功能型布建一律會安裝每個觀察到的憑證的最新版本，因此僅適用于使用主體一般名稱所宣告的憑證保護的叢集。 若要強調，請勿針對實例所宣告的憑證（也就是依指紋）使用 autorefresh 布建機制（例如 KVVM 擴充功能）-遺失可用性的風險相當可觀。

其他布建機制可能存在;Azure Service Fabric 叢集目前接受上述的。

### <a name="certificate-consumption-and-monitoring"></a>憑證耗用量和監視
如先前所述，Service Fabric 執行時間會負責尋找和使用在叢集定義中宣告的憑證。 以[憑證為基礎的驗證](cluster-security-certificates.md)文章會詳細說明 Service Fabric 如何分別執行展示和驗證規則，而不會在這裡看到。 我們將探討存取權和許可權授與，以及監視。

回想一下，Service Fabric 中的憑證是用於許多用途，從同盟層級的相互驗證到管理端點的 TLS 驗證;這需要各種元件或系統服務，才能存取憑證的私密金鑰。 Service Fabric 執行時間會定期掃描憑證存放區，尋找每個已知簡報規則的相符專案;針對每個相符的憑證，會找到對應的私密金鑰，並更新其任意存取控制清單，以包含許可權-通常是讀取並執行授與需要它們的個別身分識別。 （這個程式非正式地稱為「執行 acl」）。此程式會以1分鐘的步調執行，同時也涵蓋「應用程式」憑證，例如用來加密設定或作為端點憑證的憑證。 執行 acl 會遵循展示規則，因此請務必記住，指紋所宣告的憑證以及不會進行叢集設定更新的 autorefreshed，都將無法存取。    

### <a name="certificate-rotation"></a>憑證輪替
請注意： IETF [RFC 3647](https://tools.ietf.org/html/rfc3647)正式定義[更新](https://tools.ietf.org/html/rfc3647#section-4.4.6)為憑證的發行，其屬性與所要取代的憑證相同-發行者、主體的公開金鑰和資訊會保留，並以新的金鑰組來[重新加密](https://tools.ietf.org/html/rfc3647#section-4.4.7)為憑證的發行，而不會限制簽發者是否可能變更。 基於差異可能很重要（請考慮以主旨一般名稱宣告的憑證與簽發者釘選的案例），我們會選擇使用中性詞彙「輪替」來涵蓋這兩種案例。 （請記住，在非正式使用的情況下，「更新」則是指重新加密）。 

我們先前已介紹 Azure Key Vault 支援自動憑證輪替： [關聯] 憑證原則會定義在保存庫中輪替憑證時的時間點（不論是在到期前的天數還是總存留期的百分比）。 布建代理程式必須在此時間點之後，以及在先前的憑證到期前叫用，才能將此新憑證散發至叢集的所有節點。 Service Fabric 會在憑證的到期日（以及目前在叢集中使用中），在比預先決定的間隔發生時引發健康情況警告。 自動布建代理程式（也就是 KeyVault VM 擴充功能），設定為觀察保存庫憑證時，會定期輪詢保存庫、偵測旋轉，並取出並安裝新的憑證。 透過 VM/VMSS ' 秘密 ' 功能進行布建，將需要已授權的操作員使用已建立版本的 KeyVault URI 來更新 VM/VMSS，並對應至新的憑證。

不論是哪一種情況，輪替的憑證現在都會布建到所有的節點，而且我們已說明 Service Fabric 用來偵測旋轉的機制;讓我們檢查下一步發生的情況，假設已套用至主體一般名稱所宣告的叢集憑證（所有適用于本文撰寫時間，以及 Service Fabric 執行階段版本7.1.409）：
  - 對於內的新連線，以及叢集中的，Service Fabric 執行時間會尋找並選取具有最遠到期日的相符憑證（憑證的 ' NotAfter ' 屬性，通常縮寫為 ' na '）
  - 現有的連接將保持運作/允許自然過期或終止;系統會通知內部處理常式有新的相符項存在

這會轉譯成下列重要觀察：
  - 如果更新憑證的到期日比目前使用的憑證更早，則可能會略過。
  - 叢集的可用性或裝載的應用程式會優先于指示詞來輪替憑證;叢集最後會匯總新憑證，但不會有時間保證。 說明如下：
  - 該觀察者可能不會立即察覺到輪替的憑證完全取代其前身;確保（用於叢集憑證）重新開機主機電腦的唯一方式。 請注意，重新開機 Service Fabric 節點並不夠，因為在叢集中形成租用連線的核心模式元件不會受到影響。 另請注意，重新開機 VM/VMSS 可能會導致暫時失去可用性。 （針對應用程式憑證，只需要重新開機個別的應用程式實例即可。）
  - 引進不符合驗證規則的重新加密憑證，可以有效地中斷叢集。 最常見的範例是非預期的簽發者：叢集憑證是由主體一般名稱與簽發者釘選來宣告，但輪替的憑證是由新的/未宣告的簽發者所發出。     

### <a name="certificate-cleanup"></a>憑證清除
目前，Azure 中沒有提供明確移除憑證的規定。 判斷指定的憑證是否在指定的時間使用，通常是非一般的工作。 這對應用程式憑證比叢集憑證更容易。 Service Fabric 本身，而不是布建代理程式，在任何情況下都不會刪除使用者所宣告的憑證。 針對標準布建機制：
  - 宣告為 VM/VMSS 秘密的憑證將會布建，只要它們在 VM/VMSS 定義中被參考，而且可以從保存庫中加以抓取（刪除保存庫秘密/憑證將會使後續的 VM/VMSS 部署失敗; 同樣地，停用保存庫中的秘密版本也會使 VM/VMSS 部署失敗，其參考該秘密版本）
  - 透過 KeyVault VM 擴充功能布建的舊版憑證不一定會出現在 VM/VMSS 節點上。 代理程式只會抓取並安裝目前的版本，而且不會移除任何憑證。 重新安裝節點的映射（通常會在每個月發生），會將憑證存放區重設為 OS 映射的內容，因此會隱含移除先前的版本。 不過，請考慮將虛擬機器擴展集相應增加，只會產生目前已安裝的已觀察憑證版本;不要假設已安裝憑證的節點同質性。   

## <a name="simplifying-management---an-autorollover-example"></a>簡化管理-autorollover 範例
我們已描述機制、限制、概述複雜的規則和定義，並做出目錄的中斷預測。 可能的話，也有時間示範如何設定自動憑證管理，以避免所有這些問題。 我們會在 PaaSv2 虛擬機器擴展集上執行的 Azure Service Fabric 叢集內容中，使用 Azure Key Vault 進行秘密管理並利用受控識別，如下所示：
  - 憑證的驗證已從指紋-釘選變更為主旨 + 簽發者釘選：指定簽發者的任何憑證具有指定的主體，同樣受到信任
    - 憑證會向受信任的存放區註冊並取得（Key Vault），並由代理程式重新整理（在此案例中為 KeyVault VM 擴充功能）
    - 憑證的布建會從部署階段和以版本為基礎的（由 ComputeRP 完成）變更為部署後，並使用無版本的 KeyVault Uri
    - KeyVault 的存取權是透過使用者指派的受控識別來授與;在部署期間，會建立 UA 身分識別並將其指派給虛擬機器擴展集
    - 部署之後，代理程式（KV VM 擴充功能）會輪詢並重新整理虛擬機器擴展集的每個節點上觀察到的憑證;憑證輪替因此完全自動化，因為 SF 會自動取得最大的有效憑證

此順序完全可編寫腳本/自動化，並允許無使用者觸控的初始部署（針對憑證 autorollover 設定）。 以下提供詳細的步驟。 我們會混合使用 PowerShell Cmdlet 和 json 範本的片段。 與 Azure 互動的所有支援方法都能達成相同的功能。

[!NOTE] 這個範例假設保存庫中已經有憑證。註冊和更新 KeyVault 管理的憑證需要必要的手動步驟，如本文稍早所述。 針對生產環境，請使用 KeyVault 管理的憑證-下列為 Microsoft 內部 PKI 特定的範例腳本。
憑證 autorollover 只對 CA 發行的憑證有意義;使用自我簽署憑證（包括在 Azure 入口網站中部署 Service Fabric 叢集時所產生的憑證）無意義，但在本機/開發人員裝載的部署中，您也可以將簽發者指紋宣告為與分葉憑證相同。

### <a name="starting-point"></a>起點
為求簡潔，我們會假設下列起始狀態：
  - Service Fabric 叢集存在，並使用指紋所宣告的 CA 發行憑證來保護。
  - 憑證會儲存在保存庫中，並布建為虛擬機器擴展集秘密
  - 相同的憑證將用來將叢集轉換成以名稱為基礎的一般憑證宣告，因此可由主旨和簽發者進行驗證。如果不是這種情況，請取得適用于此用途的 CA 發行憑證，並依指紋將其新增至叢集定義，如[這裡](service-fabric-cluster-security-update-certs-azure.md)所述

以下是對應到這類狀態之範本的 json 摘錄-請注意，這會省略許多必要的設定，而且只會說明憑證相關的層面：
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

上述內容基本上指出具有指紋```json [parameters('primaryClusterCertificateTP')] ```且在 KeyVault URI ```json [parameters('clusterCertificateUrlValue')] ```找到的憑證會宣告為叢集的唯一憑證（依指紋）。 接下來，我們將設定所需的額外資源，以確保憑證的 autorollover。

### <a name="setting-up-prerequisite-resources"></a>設定必要條件資源
如先前所述，Microsoft 計算資源提供者服務會從保存庫取出布建為虛擬機器擴展集秘密的憑證，並使用其第一方身分識別和代表部署操作員來取得。 針對 autorollover，這會變更-我們會切換為使用受控識別、指派給虛擬機器擴展集，並將許可權授與保存庫的密碼。

所有後續的摘錄都應該 concomitantly 部署-它們會個別列出供播放的分析和說明之用。

首先定義使用者指派的身分識別（包含預設值做為範例）-請參閱[官方檔](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity)以取得最新資訊：
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

然後將此身分識別存取權授與保存庫秘密-如需最新資訊，請參閱[官方檔](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)：
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

在下一個步驟中，我們將：
  - 將使用者指派的身分識別指派給虛擬機器擴展集
  - 宣告虛擬機器擴展集在建立受控識別時的相依性，以及授與它對保存庫之存取權的結果
  - 宣告 KeyVault VM 擴充功能，要求它在啟動時捕獲觀察到的憑證（[官方檔](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows)）
  - 更新 Service Fabric VM 擴充功能的定義，使其相依于 KVVM 延伸模組，並將叢集憑證轉換為一般名稱（我們會在單一步驟中進行這些變更，因為它們落在相同資源的範圍內）。

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
請注意，雖然上述並未明確列出，但我們已從虛擬機器擴展集的 ' OsProfile ' 區段移除保存庫憑證 URL。
最後一個步驟是更新叢集定義，將憑證宣告從 [指紋] 變更為 [一般名稱]-這裡我們也會釘選簽發者指紋：

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

此時，您可以執行上述在單一部署中所述的更新;在此情況下，Service Fabric 資源提供者服務會以數個步驟分割叢集升級，如將叢集[憑證從指紋轉換成一般名稱](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations)一節中所述。

### <a name="analysis-and-observations"></a>分析和觀察
本節是說明上述步驟的全部深入資訊，並著重于重要層面。

#### <a name="certificate-provisioning-explained"></a>憑證布建，說明
作為布建代理程式的 KVVM 擴充功能會以預先決定的頻率持續執行。 在無法抓取觀察到的憑證時，它會繼續下一行，然後休眠直到下一個週期。 作為叢集啟動載入代理程式的 SFVM 擴充功能，將需要宣告的憑證，叢集才能形成。 這就表示 SFVM 延伸模組只能在成功抓取叢集憑證之後執行，以```json "provisionAfterExtensions" : [ "KVVMExtension" ]"```子句在此表示，並以 KeyVaultVM 延伸模組的```json "requireInitialSync": true```設定為依據。 這表示在第一次執行時（在部署或重新開機之後），必須迴圈執行其觀察到的憑證，直到成功下載全部為止。 將此參數設定為 false，並結合無法抓取叢集憑證的功能，將會導致叢集部署失敗。 相反地，如果不正確/不正確觀察憑證清單需要初始同步處理，會導致 KVVM 延伸模組失敗，因此無法部署叢集。  

#### <a name="certificate-linking-explained"></a>憑證連結，說明
您可能已注意到 KVVM 延伸模組的 ' linkOnRenewal ' 旗標，以及它設定為 false 的事實。 我們將深入探討此旗標所控制的行為，以及其對叢集運作的影響。 請注意，這是 Windows 特有的行為。

根據其[定義](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows#extension-schema)：
```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

用來建立 TLS 連線的憑證通常是透過 S 通道安全性支援提供者[取得](https://docs.microsoft.com/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea)，也就是說，用戶端不會直接存取憑證本身的私密金鑰。 S 通道以憑證延伸（[CERT_RENEWAL_PROP_ID](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)）的形式支援認證的重新導向（連結）：如果已設定此屬性，其值代表 ' 續訂 ' 憑證的指紋，因此 S 通道會改為嘗試載入連結憑證。 事實上，它會跨越這個連結（而且希望是非迴圈）清單，直到最後有「最終」憑證為止–一個不含更新標記。 這項功能在謹慎使用時，對於過期的憑證（例如）所造成的可用性損失而言，是很好的緩和措施。 在其他情況下，這可能是因為難以診斷和緩和之中斷的原因。 S-通道會無條件地在其更新屬性上執行憑證的遍歷，而不論主旨、簽發者或任何其他特定屬性，以用戶端參與驗證所產生的憑證。 實際上，產生的憑證沒有相關聯的私密金鑰，或者金鑰尚未碰到給其潛在的取用者。 
 
如果已啟用連結，KeyVault VM 擴充功能會在從保存庫中抓取觀察到的憑證時，嘗試尋找相符的現有憑證，以便透過 [更新延伸模組] 屬性來連結它們。 比對是以主體別名（SAN）為基礎，並以下列方式運作，如下所示的一例。
假設有兩個現有的憑證，如下所示： A： CN = "Alice 的配件"，SAN = {"alice.universalexports.com"}，續訂 = ' ' B： CN = "Bob 的位"，SAN = {"bob.universalexports.com"，"bob.universalexports.net"}，更新 = ' '
 
假設憑證 C 是由 KVVM ext： CN = "Mallory's malware"，SAN = {"alice.universalexports.com"，"bob.universalexports.com"，"mallory.universalexports.com"} 抓取
 
A 的 SAN 清單完全包含在 C 的中，因此也就是更新 = c. 指紋;B 的 SAN 清單具有與 C 的共同交集，但未完全包含在其中，因此 B. 更新會保持空白。
 
嘗試在憑證 A 上叫用此狀態的 AcquireCredentialsHandle （S 通道），實際上最後會將 C 傳送給遠端方。 在 Service Fabric 的情況下，叢集的[傳輸子系統](service-fabric-architecture.md#transport-subsystem)會使用 S 通道進行相互驗證，因此上述行為會直接影響叢集的基本通訊。 繼續上述範例，並假設是叢集憑證，接下來會發生什麼情況：
  - 如果 C 的私密金鑰未 ACLd 到網狀架構執行時所用的帳戶，我們會看到取得私密金鑰的失敗（SEC_E_UNKNOWN_CREDENTIALS 或類似）
  - 如果 C 的私密金鑰可供存取，則會看到其他節點（CertificateNotMatched、未授權等）傳回的授權失敗。 
 
不論是哪一種情況，傳輸都會失敗，而且叢集可能會關閉;徵兆會有所不同。 為了讓事情更糟，連結的順序取決於續約的 KVVM 延伸模組清單、保存庫中的更新排程，或甚至是會改變抓取順序的暫時性錯誤。

若要減輕這類事件的風險，我們建議：
  - 請勿混用不同保存庫憑證的 San;每個保存庫憑證都應該提供不同的用途，而且其主體和 SAN 應該會以明確的方式反映該情況
  - 在 SAN 清單中包含主體一般名稱（例如，以字面說，"CN<subject common name>="）  
  - 如果不確定，請在更新時停用 KVVM 延伸模組所布建憑證的連結 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>為何要使用使用者指派的受控識別？ 使用它的含意有哪些？
如上述 json 程式碼片段所述，需要進行作業/更新的特定排序，以保證轉換成功，以及維護叢集的可用性。 具體而言，虛擬機器擴展集資源會宣告並使用其身分識別，以在單一（從使用者的觀點來看）更新中取得秘密。 Service Fabric 的 VM 延伸模組（啟動叢集）取決於 KeyVault VM 擴充功能的完成，這取決於是否成功抓取已觀察到的憑證。 KVVM 擴充功能會使用虛擬機器擴展集的身分識別來存取保存庫，這表示在部署虛擬機器擴展集之前，保存庫上的存取原則必須已經更新。 

若要處置受控識別的建立，或將它指派給另一個資源，除了管理範本中參考的其他資源所需的角色以外，部署操作員在訂用帳戶或資源群組中必須擁有必要的角色（ManagedIdentityOperator）。 

從安全性的觀點來看，虛擬機器（擴展集）會視為其 Azure 身分識別的安全性界限。 這表示在 VM 上裝載的任何應用程式，都可以取得代表 VM 受控身分識別存取權杖的存取權杖，從未經驗證的 IMDS 端點取得。 如果您將 VM 視為共用或多租使用者環境，則可能不會指出這種可抓取叢集憑證的方法。 不過，它是唯一適用于憑證 autorollover 的布建機制。

## <a name="troubleshooting-and-frequently-asked-questions"></a>疑難排解和常見問題

*問*：如何以程式設計方式註冊 KeyVault 管理的憑證？
*答*：從 KeyVault 檔中找出簽發者的名稱，然後在下面的腳本中取代它。  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*問*：當憑證由未宣告/未指定的簽發者發行時，會發生什麼事？ 我可以在哪裡取得特定 PKI 之有效簽發者的詳盡清單？
*答*：如果憑證宣告指定了簽發者指紋，而憑證的直接簽發者並未包含在已釘選的 issuer 清單中，則會將憑證視為無效-無論用戶端是否信任其根。 因此，請務必確保簽發者清單是最新的/最新的。 引進新的簽發者是很罕見的事件，而且應該在開始發行憑證之前廣泛地加以公開。 

一般來說，PKI 會根據 IETF [RFC 7382](https://tools.ietf.org/html/rfc7382)發佈和維護憑證實務聲明。 除了其他資訊之外，它還會包含所有作用中的簽發者。 以程式設計方式抓取此清單可能與 PKI 不同。   

如需 Microsoft 內部 Pki，請參閱用來抓取授權簽發者之端點/Sdk 的內部檔。叢集擁有者必須負責定期探查此清單，並確保其叢集定義包含*所有*預期的簽發者。

*問*：是否支援多個 pki？ 
*答*：是;您不可以在叢集資訊清單中宣告多個具有相同值的 CN 專案，但可以列出多個與相同 CN 對應之 Pki 的簽發者。 這不是建議的作法，而且憑證透明度實務可能會導致無法發出這類憑證。 不過，做為從某個 PKI 遷移到另一個 PKI 的方法，這是可接受的機制。

*問*：如果目前的叢集憑證不是由 CA 發行，或沒有預定的主旨，該怎麼辦？ 
*答*：取得具有預定主旨的憑證，並依指紋將其新增至叢集的定義做為次要。 升級成功完成後，起始另一個叢集設定升級，以將憑證宣告轉換為一般名稱。 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png

