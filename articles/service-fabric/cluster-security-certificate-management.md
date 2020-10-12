---
title: Service Fabric 叢集中的憑證管理
description: 瞭解如何在 Service Fabric 叢集中管理以 x.509 憑證保護的憑證。
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: aba681157d71f94914462b8d9fc13b90d4d6b153
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653659"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Service Fabric 叢集中的憑證管理

本文說明用來保護 Azure Service Fabric 叢集中通訊的憑證管理層面，並補充 [Service Fabric 叢集安全性](service-fabric-cluster-security.md) 的簡介，以及 [x.509 在 Service Fabric 中以憑證為基礎的驗證](cluster-security-certificates.md)的說明。 我們假設讀者已經熟悉基本安全性概念，以及 Service Fabric 公開來設定叢集安全性的控制項。  

本主題所涵蓋的各方面：

* 「憑證管理」到底是什麼？
* 與憑證管理相關的角色和實體
* 憑證的旅程
* 深入探討範例
* 疑難排解和常見問題

但首先，這篇文章會嘗試將其理論與實際操作範例配對，而這些範例需要、更明確的服務、技術等等。 由於相當大的物件部分是 Microsoft 內部，我們會參考特定于 Microsoft Azure 的服務、技術和產品。 您可以隨意在批註區段中詢問，以瞭解或指引，其中 Microsoft 特定的詳細資料不適用於您的案例。

## <a name="defining-certificate-management"></a>定義憑證管理
如我們在 [隨附文章](cluster-security-certificates.md)中所見，憑證是一種密碼編譯物件，基本上系結非對稱金鑰組與描述其所代表之實體的屬性。 不過，它也是一個 ' 易腐壞 ' 物件，因為它的存留期是有限的，而且容易遭受入侵-意外洩漏或成功的惡意探索可能會導致憑證無法從安全性的觀點來使用。 這表示需要變更憑證，不論是定期或為了回應安全性事件。 管理 (的另一個層面是，它本身的) 是憑證私密金鑰的保護，或是保護採購和布建憑證之實體身分識別的秘密。 我們會參考用來取得憑證的流程和程式，以及安全地 (並安全地) 將它們傳輸到需要作為「憑證管理」的位置。 某些管理作業（例如註冊、原則設定和授權控制）已超出本文的範圍。 另外還有其他專案（例如布建、更新、重新加密或撤銷）只會與 Service Fabric 相關，不過，我們會在此解決這些問題，因為瞭解這些作業有助於適當保護一個叢集的安全。 

其目標是要盡可能將憑證管理自動化，以確保叢集的可用性不會中斷，並提供安全性保證（假設該程式是無使用者觸控的）。 目前在 Azure Service Fabric 叢集中可達成此目標;本文的其餘部分將會先解構憑證管理，之後會著重于啟用 autorollover。

具體而言，範圍中的主題包括：
  - 在管理憑證的內容中，與擁有者和平臺之間的歸屬分隔相關的假設
  - 從發行到取用的憑證長期管線
  - 憑證輪替-原因、作法和時機
  - 可能會發生錯誤？

保護/管理功能變數名稱、註冊憑證或設定授權控制以強制執行憑證發行等方面，不在本文的討論範圍內。 請參閱您最愛的公開金鑰基礎結構 (PKI) 服務的登錄授權單位 (RA) 。 Microsoft 內部客戶：請與 Azure 安全性聯繫。

## <a name="roles-and-entities-involved-in-certificate-management"></a>與憑證管理相關的角色和實體
Service Fabric 叢集中的安全性方法是「叢集擁有者宣告它時，Service Fabric 執行時間強制執行」的情況。 這表示，參與叢集運作的身分識別幾乎沒有任何憑證、金鑰或其他認證來自服務本身;它們都是由叢集擁有者所宣告。 此外，叢集擁有者也會負責將憑證布建到叢集中、視需要予以更新，以及隨時確保憑證的安全性。 更具體來說，叢集擁有者必須確保：
  - 在叢集資訊清單的 NodeType 區段中所宣告的憑證，可以在該類型的每個節點上找到（根據[展示規則](cluster-security-certificates.md#presentation-rules)）
  - 上述宣告的憑證會安裝為內含其對應的私密金鑰
  - 在展示規則中宣告的憑證應通過 [驗證規則](cluster-security-certificates.md#validation-rules) 

Service Fabric，其部分會假設下列各項的職責：
  - 尋找/尋找符合叢集定義中宣告的憑證  
  - 以「需求」為基礎，將對應私密金鑰的存取權授與 Service Fabric 控制的實體
  - 根據已建立的安全性最佳作法和叢集定義嚴格驗證憑證
  - 在憑證即將到期時引發警示，或執行憑證驗證的基本步驟失敗
  - 驗證 (的程度) ，主機的基礎設定符合叢集定義的憑證相關層面 

這是因為「憑證管理」負擔 (為「作用中」的作業) 完全落在叢集擁有者上。 在下列各節中，我們將深入探討每個管理作業，並提供可用的機制及其對叢集的影響。

## <a name="the-journey-of-a-certificate"></a>憑證的旅程
讓我們快速重新流覽憑證的進展，從發行到 Service Fabric 叢集內容中的耗用量：

  1. 網域擁有者會向 PKI 的 RA 註冊您要與後續憑證相關聯的網域或主體;接著，這些憑證會構成所述網域或主體的擁有權證明
  2. 網域擁有者也會在 RA 中指定授權要求者的身分識別，這是有權向指定的網域或主體要求註冊憑證的實體。在 Microsoft Azure 中，預設身分識別提供者是 Azure Active Directory，而授權的要求者則是由其對應的 AAD 身分識別 (或透過安全性群組指定) 
  3. 經過授權的要求者接著會透過秘密管理服務註冊到憑證;在 Microsoft Azure 中，選擇的 SMS 是 Azure Key Vault (AKV) ，可安全地儲存和允許依授權的實體抓取秘密/憑證。 AKV 也會根據相關聯的憑證原則中的設定，更新/重新設定憑證的金鑰。  (AKV 使用 AAD 作為身分識別提供者。 ) 
  4. 經授權的取得者（我們將稱為「布建代理程式」）會從保存庫抓取憑證（包含其私密金鑰），並將其安裝在裝載叢集的電腦上。
  5. Service Fabric 服務 (在每個節點上提高許可權，) 將憑證的存取權授與允許的 Service Fabric 實體;這些是由本機群組指定，並在 ServiceFabricAdministrators 與 ServiceFabricAllowedUsers 之間分割
  6. Service Fabric 執行時間存取並使用憑證來建立同盟，或向授權的用戶端驗證輸入要求
  7. 布建代理程式會監視保存庫憑證，並在偵測到更新時觸發布建流程;接著，叢集擁有者會視需要更新叢集定義，以指出要變換憑證的意圖。
  8. 布建代理程式或叢集擁有者也負責清除/刪除未使用的憑證
  
基於我們的目的，上述序列中的前兩個步驟大多不相關;唯一的連接是憑證的主體一般名稱是在叢集定義中宣告的 DNS 名稱。

這些步驟如下所示：請注意依指紋和一般名稱分別宣告的憑證之間的差異。

*圖 1。* 發佈和布建憑證指紋所宣告的流程。
![布建憑證指紋所宣告的憑證][Image1]

*圖2。* 發佈和布建主體一般名稱所宣告的憑證流程。
![提供主體一般名稱所宣告的憑證][Image2]

### <a name="certificate-enrollment"></a> 憑證註冊
本主題將在 Key Vault [檔](../key-vault/certificates/create-certificate.md)中詳細說明。我們將在此加入概要，以提供持續性和更輕鬆的參考。 繼續使用 Azure 作為內容，並使用 Azure Key Vault 作為秘密管理服務，授權的憑證要求者必須至少具備保存庫擁有者的憑證管理許可權，並由保存庫擁有者授與。然後，要求者會註冊到憑證中，如下所示：
    - 在 Azure Key Vault (AKV) 中建立憑證原則，以指定憑證的網域/主旨、所需的簽發者、金鑰類型和長度、預定的金鑰使用方式等等;如需詳細資訊，請參閱 [Azure Key Vault 中的憑證](../key-vault/certificates/certificate-scenarios.md) 。 
    - 使用上述指定的原則，在相同的保存庫中建立憑證;接著，這會產生金鑰組作為保存庫物件、以私密金鑰簽署的憑證簽署要求，然後轉送至指定的簽發者進行簽署
    - 一旦簽發者 (憑證授權單位單位) 使用已簽署的憑證來回複，結果就會合並到保存庫，而憑證可用於下列作業：
      - 在 {vaultUri}/certificates/{name}：包含公開金鑰和中繼資料的憑證
      - 在 {vaultUri}/keys/{name}：憑證的私密金鑰（可用於密碼編譯作業 (包裝/解除包裝、簽署/驗證) 
      - 在 {vaultUri}/secrets/{name}：包含其私密金鑰的憑證，可供下載為未受保護的 pfx 或 pem 檔案  
    回想一下，保存庫憑證實際上是一條時間的憑證實例，共用原則。 將會根據原則的存留期和更新屬性來建立憑證版本。 強烈建議保存庫憑證不共用主體或網域/DNS 名稱;叢集可能會造成干擾，從不同的保存庫憑證（具有相同的主體）布建憑證實例，但本質上有不同的其他屬性，例如簽發者、金鑰使用方法等。

此時，保存庫中有憑證存在，可供取用。 前往：

### <a name="certificate-provisioning"></a>憑證布建
我們提到「布建代理程式」，這是從保存庫抓取憑證（包含其私密金鑰），並將它安裝到叢集的每個主機上的實體。  (回想 Service Fabric 不會布建憑證。在我們的內容中 ) ，叢集將會裝載于 Azure Vm 和/或虛擬機器擴展集的集合。 在 Azure 中，您可以使用下列機制來完成將保存庫中的憑證布建至 VM/VMSS 的作業-假設是先前已在保存庫擁有者授與保存庫的「取得」許可權： 
  - 臨機操作：運算子會將保存庫 (中的憑證抓取為 pfx/PKCS #12 或 pem) ，並將其安裝在每個節點上
  - 在部署期間，作為虛擬機器擴展集的「秘密」：「計算服務」會代表操作員使用其第一個合作物件身分識別來抓取憑證，並將憑證安裝在虛擬機器擴展集的每個節點上， ([例如](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)) ;請注意，這只允許布建已建立版本的秘密
  - 使用 [KEY VAULT VM 擴充](../virtual-machines/extensions/key-vault-windows.md)功能;這可讓您使用不限版本的宣告來布建憑證，定期重新整理觀察到的憑證。 在此情況下，VM/VMSS 預期會有 [受控識別](../virtual-machines/security-policy.md#managed-identities-for-azure-resources)，也就是已授與保存庫 (的身分識別，) 包含所觀察到的憑證。

不建議使用臨機操作機制的原因有很多，範圍從安全性到可用性，也不會進一步討論。如需詳細資訊，請參閱 [虛擬機器擴展集中的憑證](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates)。

VMSS-/Compute-based 布建提供安全性和可用性優點，但也提供限制。 它需要設計-將憑證宣告為版本設定的秘密，使其僅適用于使用指紋所宣告的憑證保護的叢集。 相反地，Key Vault VM 擴充功能型布建一律會安裝每個觀察到之憑證的最新版本，使其僅適用于使用主體一般名稱所宣告的憑證保護的叢集。 若要強調，請不要使用自動重新整理布建機制 (例如 KVVM) 延伸模組（由實例宣告的憑證 (也就是藉由指紋) -遺失可用性的風險很可觀。

其他布建機制可能存在;上述的 Azure Service Fabric 叢集目前已接受上述動作。

### <a name="certificate-consumption-and-monitoring"></a>憑證耗用量和監視
如先前所述，Service Fabric 執行時間負責尋找和使用在叢集定義中宣告的憑證。 以 [憑證為基礎之驗證](cluster-security-certificates.md) 的文章會詳細說明 Service Fabric 如何分別執行簡報和驗證規則，並不會在此討論。 我們將探討存取權和許可權授與，以及監視。

回想一下，Service Fabric 中的憑證用於許多用途，從同盟層級的相互驗證到管理端點的 TLS 驗證;這需要有各種元件或系統服務才能存取憑證的私密金鑰。 Service Fabric 執行時間會定期掃描憑證存放區，以尋找每個已知呈現規則的相符專案;針對每個相符的憑證，會找到對應的私密金鑰，而且其任意存取控制清單會更新以包含許可權，通常是讀取和執行-授與需要它們的個別身分識別。  (此程式非正式地稱為「執行 acl」。 ) 進程會在1分鐘的時間執行，而且也會涵蓋「應用程式」憑證，例如用來加密設定或端點憑證的憑證。 執行 acl 遵循簡報規則，因此請務必記住，憑證指紋所宣告的憑證，而在沒有後續叢集設定更新的情況下 autorefreshed 的憑證將無法存取。    

### <a name="certificate-rotation"></a>憑證輪替
請注意： IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) 正式將 [更新](https://tools.ietf.org/html/rfc3647#section-4.4.6) 定義為憑證的發行，此憑證的屬性與所取代的憑證相同：簽發者、主體的公開金鑰和資訊，以及 [重新加密](https://tools.ietf.org/html/rfc3647#section-4.4.7) 為使用新的金鑰組來發行憑證，而不會限制簽發者是否可能變更。 由於差異可能很重要 (請考慮主體一般名稱與簽發者釘選) 所宣告的憑證案例，我們將選擇「旋轉」中性詞彙來涵蓋這兩種案例。  (請記住，在非正式使用的情況下，「續約」指的是重新加密。 )  

我們稍早曾看過，Azure Key Vault 支援自動憑證輪替：「關聯」憑證原則會定義在保存庫中輪替憑證時的時間點（無論到期日之前的天數或總存留期的百分比）。 布建代理程式必須在此時間點之後，以及之前的憑證到期之前叫用，以將此新憑證散發至叢集的所有節點。 Service Fabric 將會在憑證的到期日 (的到期日，以及叢集內目前正在) 使用中的時間，提供比預先決定間隔更快的時間，協助您提出健康情況警告。 自動布建代理程式 (亦即，設定為觀察保存庫憑證的 KeyVault VM 延伸模組) 會定期輪詢保存庫、偵測旋轉，以及取得並安裝新的憑證。 透過 VM/VMSS 「秘密」功能布建完成時，將需要授權的操作員以對應至新憑證的已建立版本 KeyVault URI 來更新 VM/VMSS。

無論是哪一種情況，輪替的憑證現在都會布建到所有的節點，而我們已描述 Service Fabric 用來偵測旋轉的機制;讓我們檢查下一步發生的情況：假設套用至主體一般名稱所宣告之叢集憑證的旋轉 (所有適用于撰寫時間的時間，以及 Service Fabric 執行階段版本 7.1.409) ：
  - 針對內的新連線，以及叢集中的新連線，Service Fabric 執行時間會尋找並選取具有最新到期日的相符憑證 (憑證的 ' NotAfter ' 屬性，通常縮寫為 ' na ' ) 
  - 現有的連接將保持運作狀態/允許自然到期或終止;系統會通知內部處理常式有新的相符項存在

這會轉譯成下列重要觀察：
  - 如果更新憑證的到期日早于目前使用的憑證，則可能會忽略該更新憑證。
  - 叢集的可用性或裝載應用程式的可用性優先于指示詞來旋轉憑證;叢集最後會融合在新憑證上，但不會有時間保證。 說明如下：
  - 針對已輪替憑證完全取代其前身的觀察者，它可能不會立即察覺;確保叢集憑證 () 重新開機主機電腦的唯一方法。 請注意，重新開機 Service Fabric 節點並不足夠，因為在叢集中形成租用連線的核心模式元件不會受到影響。 另請注意，重新開機 VM/VMSS 可能會導致可用性暫時遺失。  (應用程式憑證，只需要重新開機個別的應用程式實例就夠了。 ) 
  - 引進不符合驗證規則的重建憑證可以有效地中斷叢集。 最常見的範例是未預期的簽發者：叢集憑證是由主體一般名稱與簽發者釘選所宣告，但輪替的憑證是由新的/未宣告的簽發者所發出。     

### <a name="certificate-cleanup"></a>憑證清除
目前，Azure 不會提供明確移除憑證的條款。 這通常是一項非一般的工作，用來判斷特定時間是否正在使用指定的憑證。 這對應用程式憑證比叢集憑證更為困難。 Service Fabric 本身，而不是布建代理程式，則不會在任何情況下刪除使用者所宣告的憑證。 針對標準布建機制：
  - 宣告為 VM/VMSS 秘密的憑證將會布建，只要 VM/VMSS 定義中參考它們，就可以從保存庫中移除， (刪除保存庫秘密/憑證將會使後續的 VM/VMSS 部署失敗;同樣地，在保存庫中停用秘密版本也會使 VM/VMSS 部署失敗，其會參考該秘密版本) 
  - 透過 KeyVault VM 延伸模組布建的舊版憑證不一定會出現在 VM/VMSS 節點上。 代理程式只會抓取並安裝目前的版本，而不會移除任何憑證。 重新安裝節點的映射 (通常每個月會發生) 會將憑證存放區重設為 OS 映射的內容，因此會隱含移除先前的版本。 不過，請考慮相應增加虛擬機器擴展集，只會產生目前已安裝的觀察憑證版本;請勿假設節點的同質性與已安裝的憑證有關。   

## <a name="simplifying-management---an-autorollover-example"></a>簡化管理-autorollover 範例
我們已描述機制、限制、所述的複雜規則和定義，並進行了中斷的 dire 預測。 可能的話，您可能會看到如何設定自動憑證管理來避免這些問題。 在 PaaSv2 虛擬機器擴展集上執行的 Azure Service Fabric 叢集內容中，我們會使用 Azure Key Vault 來管理秘密，並利用受控識別，如下所示：
  - 憑證的驗證已從指紋釘選變更為主體 + 簽發者釘選：指定簽發者的任何憑證都同樣受到信任
    - 憑證會在信任的存放區中註冊並取得 (Key Vault) ，並由代理程式重新整理-在此案例中，KeyVault VM 延伸模組
    - 布建憑證的方式，會隨著 ComputeRP) 部署和使用無版本的 KeyVault Uri 而從部署時間和版本 (進行變更
    - KeyVault 的存取權是透過使用者指派的受控識別來授與。在部署期間，會建立 UA 身分識別，並將其指派給虛擬機器擴展集
    - 部署之後， (KV VM 擴充功能的代理程式) 會輪詢並重新整理虛擬機器擴展集的每個節點上觀察到的憑證;由於 SF 會自動挑選最遠的有效憑證，因此憑證輪替會完全自動化。

此序列可完整編寫腳本/自動化，並可讓使用者無需觸控的初始部署為憑證 autorollover 設定的叢集。 詳細步驟如下所述。 我們會混合使用 PowerShell Cmdlet 和 json 範本的片段。 所有與 Azure 互動的支援方法都可達成相同的功能。

[!NOTE] 此範例假設保存庫中已有憑證存在;註冊和更新 KeyVault 管理的憑證需要必要的手動步驟，如本文稍早所述。 針對生產環境，請使用 KeyVault 管理的憑證-Microsoft 內部 PKI 專屬的範例腳本，如下所示。
憑證 autorollover 僅適用于 CA 發行的憑證;使用自我簽署的憑證（包括在 Azure 入口網站中部署 Service Fabric 叢集時所產生的憑證）無意義，但在本機/開發人員裝載的部署中，您可以藉由將簽發者指紋宣告為與分葉憑證相同的方式來進行。

### <a name="starting-point"></a>起點
為了簡潔起見，我們會假設下列啟動狀態：
  - Service Fabric 叢集存在，並使用指紋宣告的 CA 發行憑證來保護。
  - 憑證會儲存在保存庫中，並布建為虛擬機器擴展集秘密
  - 相同的憑證將用來將叢集轉換為一般以名稱為基礎的憑證宣告，因此可依主旨和簽發者進行驗證。如果不是這種情況，請取得適用于此用途的 CA 發行憑證，並依憑證指紋將其新增至叢集定義， [如下所述](service-fabric-cluster-security-update-certs-azure.md)

以下是來自對應到這類狀態之範本的 json 摘要-請注意，這會省略許多必要的設定，而且只會說明與憑證相關的層面：
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

以上基本上指出具有指紋 ```json [parameters('primaryClusterCertificateTP')] ``` 且在 KEYVAULT URI 中找到的憑證 ```json [parameters('clusterCertificateUrlValue')] ``` ，是以指紋的形式宣告為叢集的唯一憑證。 接下來，我們將設定所需的其他資源，以確保憑證的 autorollover。

### <a name="setting-up-prerequisite-resources"></a>設定必要條件資源
如先前所述，Microsoft 計算資源提供者服務會從保存庫取出作為虛擬機器擴展集秘密布建的憑證，並使用其第一方身分識別和代表部署操作員。 針對將會變更的 autorollover，我們會切換為使用受控識別，指派給虛擬機器擴展集，並將其授與保存庫的秘密。

所有後續的摘要都應該部署 concomitantly，它們會個別列出以供播放播放分析和說明。

首先定義使用者指派的身分識別 (預設值會包含為範例) -請參閱 [官方檔](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) 以取得最新資訊：
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

然後將此身分識別存取權授與保存庫秘密-請參閱 [官方檔](/rest/api/keyvault/vaults/updateaccesspolicy) 以取得最新資訊：
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
  - 宣告虛擬機器擴展集與建立受控識別的相依性，以及授與存取保存庫的結果
  - 宣告 KeyVault VM 延伸模組，要求它在啟動 ([正式檔](../virtual-machines/extensions/key-vault-windows.md) 時，必須抓取觀察到的憑證) 
  - 更新 Service Fabric VM 延伸模組的定義以相依于 KVVM 擴充功能，並將叢集憑證轉換為一般名稱 (我們在單一步驟中進行這些變更，因為它們落在相同資源的範圍內。 ) 

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
請注意，雖然上面未明確列出，但我們已從虛擬機器擴展集的 [OsProfile] 區段中移除保存庫憑證 URL。
最後一個步驟是更新叢集定義，將憑證宣告從指紋變更為一般名稱-這裡我們也會釘選簽發者指紋：

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

至此，您可以在單一部署中執行上面所述的更新;在此情況下，Service Fabric 資源提供者服務會以數個步驟分割叢集升級，如將叢集 [憑證從指紋轉換為一般名稱](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations)的區段中所述。

### <a name="analysis-and-observations"></a>分析和觀察
本節將全面說明說明上述的步驟，並強調重要層面。

#### <a name="certificate-provisioning-explained"></a>提供憑證布建，說明
KVVM 延伸模組是布建代理程式，會依預定的頻率持續執行。 在無法取出觀察到的憑證時，它會繼續進行，並在下一個週期之前進入休眠狀態。 SFVM 延伸模組（作為叢集啟動載入代理程式）將需要宣告的憑證，叢集才能形成。 也就是說，這也表示 SFVM 延伸模組只能在成功將叢集憑證 (s) （由子句表示）， ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` 以及 KeyVaultVM 延伸模組的設定的情況下執行 ```json "requireInitialSync": true``` 。 這表示在部署或重新) 啟動後，第一次執行 (上的 KVVM 延伸模組，必須等到所有憑證都成功下載之後，才能在其觀察到的憑證之間進行迴圈。 將此參數設定為 false，與無法取出叢集憑證 () 會導致叢集部署失敗。 相反地，要求具有不正確/無效之觀察憑證清單的初始同步處理會導致 KVVM 延伸模組失敗，因此無法再部署叢集。  

#### <a name="certificate-linking-explained"></a>憑證連結，說明
您可能已注意到 KVVM 延伸模組的 ' linkOnRenewal ' 旗標，以及它設定為 false 的事實。 我們將深入探討此旗標所控制的行為，以及其對叢集運作的影響。 請注意，這是 Windows 特定的行為。

根據其 [定義](../virtual-machines/extensions/key-vault-windows.md#extension-schema)：
```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

用來建立 TLS 連線的憑證通常是透過 S 通道安全性支援提供者 [取得](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) ，也就是用戶端不會直接存取憑證本身的私密金鑰。 S-通道支援重新導向 () 憑證延伸的形式連結認證 ([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)) ：如果設定了此屬性，其值代表「更新」憑證的指紋，因此 S 通道會改為嘗試載入連結的憑證。 事實上，它會跨越這個連結的 (，希望以非迴圈的方式) 清單，直到最後有「最終」憑證為止（一個沒有續約標記）。 這項功能在明智地使用時，對於因實例) 的憑證 (過期所造成的可用性，是很好的緩和措施。 在其他情況下，這可能是因為難以診斷和緩和的中斷原因。 S 通道會無條件地在其更新屬性上執行憑證的遍歷，而不是由用戶端參與驗證所產生之憑證的主體、簽發者或任何其他特定屬性。 實際上，產生的憑證沒有相關聯的私密金鑰，或金鑰尚未碰到至其潛在取用者。 
 
如果已啟用連結，KeyVault VM 延伸模組會在從保存庫中取得觀察到的憑證時，嘗試尋找相符的現有憑證，以便透過更新延伸模組屬性來連結它們。 比對只會根據主體的別名 (SAN) ，以獨佔方式)  (，並以以下查詢示的方式運作。
假設有兩個現有的憑證，如下所示： A： CN = "Alice 的配件"、SAN = {"alice.universalexports.com"}、續約 = ' ' B： CN = "Bob 的位"、SAN = {"bob.universalexports.com"、"bob.universalexports.net"}、更新 = ' '
 
假設憑證 C 是由 KVVM ext： CN = "Mallory's malware"、SAN = {"alice.universalexports.com"、"bob.universalexports.com"、"mallory.universalexports.com"} 取出
 
A 的 SAN 清單會完全包含在 C 中，因此 A. 續約 = c. 指紋;B 的 SAN 清單與 C 的共通交集，但並未完全包含在其中，所以 b. 續約保持空白。
 
嘗試在憑證 A 上叫用 AcquireCredentialsHandle (S-通道) 在此狀態下，實際上最後會將 C 傳送給遠端方。 在 Service Fabric 的情況下，叢集的 [傳輸子系統](service-fabric-architecture.md#transport-subsystem) 會使用 S 通道進行相互驗證，因此上面所述的行為會直接影響叢集的基本通訊。 繼續上述範例，並假設 A 是叢集憑證，接下來會發生什麼事：
  - 如果 C 的私密金鑰未 ACLd 到網狀架構執行的帳戶，我們將會看到取得私密金鑰 (SEC_E_UNKNOWN_CREDENTIALS 或類似的失敗) 
  - 如果 C 的私密金鑰可供存取，則會看到其他節點傳回的授權失敗 (CertificateNotMatched、未經授權等 )  
 
在任一種情況下，傳輸都會失敗，而且叢集可能會停止運作。徵兆會有所不同。 為了讓事情更糟，連結的順序取決於續約的 KVVM 延伸模組清單、保存庫中的續約排程，或甚至會改變抓取順序之暫時性錯誤的順序。

若要減輕這類事件，建議您：
  - 請勿混用不同保存庫憑證的 San;每個保存庫憑證都應該提供不同的用途，且其主旨和 SAN 應該會反映明確的情況
  - 將主體一般名稱包含在 SAN 清單中 (例如，「CN =」 <subject common name> )   
  - 如果不確定，請針對以 KVVM 擴充功能布建的憑證停用更新連結 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>為何要使用使用者指派的受控識別？ 使用它有何影響？
由於上述的 json 程式碼片段所述，需要特定的作業/更新順序，以保證轉換成功，以及維護叢集的可用性。 具體而言，虛擬機器擴展集資源會宣告並使用其身分識別，從使用者的觀點) 更新取得單一 (中的秘密。 啟動叢集) 的 Service Fabric VM 擴充功能 (取決於是否已完成 KeyVault VM 延伸模組，這取決於是否成功抓取觀察到的憑證。 KVVM 擴充功能會使用虛擬機器擴展集的身分識別來存取保存庫，這表示在部署虛擬機器擴展集之前，必須先更新保存庫上的存取原則。 

若要處置受控識別的建立，或將它指派給另一個資源，部署操作員除了管理範本中參考的其他資源所需的角色之外，還必須在訂用帳戶或資源群組中 (ManagedIdentityOperator) 所需的角色。 

從安全性的觀點來看，您應該記得虛擬機器 (擴展集) 被視為其 Azure 身分識別的安全性界限。 這表示，裝載在 VM 上的任何應用程式都可以取得存取權杖，以取得代表 VM 受控識別存取權杖的存取權杖，從未經驗證的 IMDS 端點取得。 如果您將 VM 視為共用或多租使用者環境，則您可能不會指出這個用來抓取叢集憑證的方法。 不過，這是唯一適用于憑證 autorollover 的布建機制。

## <a name="troubleshooting-and-frequently-asked-questions"></a>疑難排解和常見問題

*問*：如何以程式設計方式註冊 KeyVault 管理的憑證？
*答*：從 KeyVault 檔中找出簽發者的名稱，然後將它取代為下列腳本。  
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

*問*：當未宣告/未指定的簽發者發行憑證時，會發生什麼事？ 我可以在哪裡取得指定 PKI 之有效簽發者的完整清單？
*答*：如果憑證宣告指定簽發者指紋，且憑證的直接簽發者未包含在固定的簽發者清單中，則會將憑證視為無效，不論其根目錄是否受用戶端信任。 因此，請務必確定簽發者的清單是最新的，也就是最新的。 引進新的簽發者是罕見的事件，在開始發行憑證之前，應該先廣泛地進行公開。 

在一般情況下，PKI 會根據 IETF [RFC 7382](https://tools.ietf.org/html/rfc7382)發佈和維護認證實務聲明。 在其他資訊中，它將包含所有有效的簽發者。 以程式設計方式抓取此清單可能與 PKI 不同。   

若為 Microsoft 內部 Pki，請參閱用來取得授權簽發者之端點/Sdk 的內部檔;叢集擁有者必須負責定期探查此清單，並確保其叢集定義包含 *所有* 預期的簽發者。

*問*：是否支援多個 pki？ 
*答*：是;您可能不會在叢集資訊清單中宣告具有相同值的多個 CN 專案，但是可以列出與相同 CN 對應之多個 Pki 的簽發者。 這不是建議的作法，而且憑證透明作法可能會導致這類憑證無法被發出。 不過，若要從某個 PKI 遷移到另一個 PKI，這是可接受的機制。

*問*：如果目前的叢集憑證不是由 CA 簽發，或沒有預定的主體，該怎麼辦？ 
*答*：取得具有預期主體的憑證，並依指紋將其新增至叢集的定義做為次要。 一旦成功完成升級，就會起始另一個叢集設定升級，將憑證宣告轉換成一般名稱。 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
