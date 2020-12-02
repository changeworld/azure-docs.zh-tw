---
title: 在 Microsoft Azure 上設計安全的應用程式
description: 本文討論在 web 應用程式專案的需求和設計階段期間應考慮的最佳作法。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 743412b7602e5781911cdf190e41a5ee15bfddd4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487672"
---
# <a name="design-secure-applications-on-azure"></a>在 Azure 上設計安全的應用程式
在本文中，我們會提供在您設計雲端應用程式時要考慮的安全性活動和控制項。 訓練資源，以及在 Microsoft [安全性開發生命週期 ](/previous-versions/windows/desktop/cc307891(v=msdn.10)) 的需求和設計階段時要考慮的安全性問題和概念 (SDL) 的涵蓋範圍。 目標是協助您定義活動和 Azure 服務，您可以使用這些服務來設計更安全的應用程式。

本文章涵蓋下列 SDL 階段：

- 訓練
- 規格需求
- 設計

## <a name="training"></a>訓練
開始開發您的雲端應用程式之前，請花一點時間瞭解 Azure 上的安全性和隱私權。 藉由採取這個步驟，您可以減少應用程式中可利用的弱點數和嚴重性。 您可以更妥善地回應不斷變化的威脅環境。

在訓練階段使用下列資源，以熟悉可供開發人員使用的 Azure 服務，以及 Azure 上的安全性最佳作法：

  - [Azure 的開發人員指南](https://azure.microsoft.com/campaigns/developer-guide/) 會說明如何開始使用 azure。 本指南將說明您可以使用哪些服務來執行應用程式、儲存資料、併入智慧、建立 IoT 應用程式，並以更有效率且安全的方式部署您的解決方案。

  - [適用于 azure 開發人員的快速入門手冊](../../guides/developer/azure-developer-guide.md) 可為想要開始使用 Azure 平臺的開發人員提供基本資訊，以滿足其開發需求。

  - [Sdk 和工具](../../index.yml?pivot=sdkstools) 說明可在 Azure 上使用的工具。

  - [Azure DevOps Services](/azure/devops/) 提供開發共同作業工具。 這些工具組括高效能管線、免費的 Git 存放庫、可設定的看板面板，以及廣泛的自動化和雲端負載測試。
    [DevOps 資源中心](/azure/devops/learn/)結合了我們的資源，以取得學習 DevOps 實務、Git 版本控制、敏捷式方法、如何與 Microsoft 的 DevOps 搭配運作，以及如何評估您自己的 DevOps 進展。

  - 在[推送到生產環境之前，要考慮的前5個安全性專案](/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca)會示範如何協助保護您在 Azure 上的 web 應用程式，並保護您的應用程式免于最常見且危險的 web 應用程式攻擊。

  - [適用于 Azure 的安全 DevOps 套件](https://azsk.azurewebsites.net/index.html) 是腳本、工具、延伸模組和自動化的集合，可已經考慮到廣泛的 Azure 訂用帳戶，以及使用大量自動化之 DevOps 團隊的資源安全性需求。 適用于 Azure 的安全 DevOps 套件可為您示範如何順暢地將安全性整合到您的原生 DevOps 工作流程。 此套件可解決 (SVTs) 的安全性驗證測試之類的工具，可協助開發人員撰寫安全的程式碼，並在程式碼和早期開發階段測試其雲端應用程式的安全設定。

  - [Azure 解決方案的安全性最佳做法](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions) 會提供一系列安全性最佳作法，以在您使用 Azure 設計、部署及管理雲端解決方案時使用。

## <a name="requirements"></a>規格需求
需求定義階段是定義應用程式的重要步驟，以及發行時的運作方式。 需求階段也是思考您將在應用程式中建立之安全性控制項的時間。 在這個階段中，您也會開始整個 SDL 所採取的步驟，以確保您發行和部署安全的應用程式。

### <a name="consider-security-and-privacy-issues"></a>考慮安全性和隱私權問題
這個階段是考慮基本安全性和隱私權問題的最佳時機。 在專案開始時定義可接受的安全性和隱私權層級可協助小組：

- 瞭解與安全性問題相關的風險。
- 識別並修正開發期間的安全性錯誤。
- 將已建立的安全性和隱私權等級套用至整個專案。

當您為應用程式撰寫需求時，請務必考慮可協助保護應用程式和資料安全的安全性控制措施。

### <a name="ask-security-questions"></a>提出安全性問題
詢問安全性問題，例如：

  - 我的應用程式是否包含機密資料？

  - 我的應用程式是否收集或儲存的資料，需要我遵守業界標準和合規性計畫，例如 [聯邦金融機構檢查委員會 (FFIEC) ](/previous-versions/azure/security/blueprints/ffiec-analytics-overview) 或 [支付卡產業資料安全性標準 (PCI DSS) ](/previous-versions/azure/security/blueprints/pcidss-analytics-overview)？

  - 我的應用程式是否會收集或包含機密的個人或客戶資料，以供自己或其他資訊使用，以識別、聯絡或尋找單一人員？

  - 我的應用程式會收集或包含可用來存取個人醫療、教育、財務或雇用資訊的資料嗎？ 在需求階段中識別您的資料敏感度，可協助您將資料分類，並找出您將用於應用程式的資料保護方法。

  - 我的資料儲存在何處？ 請考慮您要如何監視應用程式用於任何非預期變更的儲存體服務 (例如) 的回應時間變慢。 您是否能夠影響記錄以收集更詳細的資料，並深入分析問題？

  - 我的應用程式是否可供網際網路上的公用 () 或內部使用？ 如果您的應用程式可供公開使用，您要如何保護可能收集的資料不正確的使用方式？ 如果您的應用程式僅供內部使用，請考慮您組織中的誰應該有應用程式的存取權，以及他們應該有多久的存取權。

  - 開始設計您的應用程式之前，您是否瞭解您的身分識別模型？ 您要如何判斷使用者的身分，以及使用者的授權方式為何？

  - 我的應用程式是否執行敏感性或重要工作 (例如轉移錢、解除鎖定大門或傳遞醫學) ？
    請考慮您將如何驗證執行敏感性工作的使用者是否已獲授權執行此工作，以及您將如何驗證該人員的身分。 授權 (AuthZ) 是授與已驗證的安全性主體許可權以進行某些動作的動作。 驗證 (驗證) 是針對合法認證的合作物件帶來挑戰的動作。

  - 我的應用程式是否執行任何具風險的軟體活動，像是允許使用者上傳或下載檔案或其他資料？ 如果您的應用程式確實執行具風險的活動，請考慮您的應用程式將如何保護使用者免于處理惡意檔案或資料。

### <a name="review-owasp-top-10"></a>複習 OWASP 前10大
請考慮審核 [<span class="underline">OWASP 前10個應用程式安全性風險</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)。
OWASP Top 10 解決了 web 應用程式的重大安全性風險。
瞭解這些安全性風險可協助您進行需求和設計決策，以將應用程式中的風險降至最低。

考慮安全性控制項來防止缺口是很重要的。
不過，您也會想要 [假設發生缺口](/azure/devops/learn/devops-at-microsoft/security-in-devops) 。 假設缺口有助於事先回答一些有關安全性的重要問題，所以不需要在緊急狀況下回答：

  - 我要如何偵測到攻擊？

  - 如果有攻擊或缺口，該怎麼辦？

  - 我要如何從攻擊中復原，例如資料洩漏或篡改？

## <a name="design"></a>設計

設計階段對於建立設計和功能規格的最佳作法是很重要的。 對於執行風險分析，有助於減輕整個專案的安全性和隱私權問題，這也是很重要的。

當您準備好安全性需求並使用安全的設計概念時，您可以避免或減少安全性瑕疵的機會。 安全性瑕疵是應用程式設計的監督，可能會讓使用者在您的應用程式發行之後，執行惡意或非預期的動作。

在設計階段，也請考慮如何在圖層中套用安全性;一種防禦層級不一定足夠。 如果攻擊者)  (WAF 的 web 應用程式防火牆，會發生什麼事？ 您想要有另一個安全性控制措施來防禦該攻擊。

考慮到這一點，我們將討論下列安全的設計概念，以及設計安全應用程式時應該解決的安全性控制項：

- 使用安全程式碼庫和軟體架構。
- 掃描易受攻擊的元件。
- 在應用程式設計期間使用威脅模型化。
- 減少受攻擊面。
- 採用身分識別原則作為主要安全性周邊。
- 需要重新驗證重要交易。
- 使用金鑰管理解決方案來保護金鑰、認證和其他秘密。
- 保護敏感性資料。
- 執行失敗安全的量值。
- 利用錯誤和例外狀況處理。
- 使用記錄和警示。

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>使用安全程式碼庫和軟體架構

針對開發，請使用具有內嵌安全性的安全程式碼庫和軟體架構。 開發人員可以使用現有、經過證實的功能 (加密、輸入清理、輸出編碼、金鑰或連接字串，以及其他任何會被視為安全性控制的其他功能) 而不是從頭開發安全性控制。 這有助於防範安全性相關的設計和實行瑕疵。

請確定您使用的是最新版本的架構，以及架構中可用的所有安全性功能。 Microsoft 為所有開發人員提供一 [組完整的開發工具](https://azure.microsoft.com/product-categories/developer-tools/) ，可處理任何平臺或語言，以提供雲端應用程式。 您可以選擇各種不同的 [sdk](https://azure.microsoft.com/downloads/)，以您選擇的語言撰寫程式碼。
您可以利用功能齊全的整合式開發環境 (Ide) 以及具有先進的偵錯工具和內建 Azure 支援的編輯器。

Microsoft 提供各種不同的 [語言、架構和工具](../../index.yml?panel=sdkstools-all&pivot=sdkstools) ，可讓您用來在 Azure 上開發應用程式。 例如， [適用于 .net 和 .Net Core 開發人員的 Azure](/dotnet/azure/)。 針對每個我們提供的語言和架構，您可以找到快速入門、教學課程和 API 參考，以協助您快速開始使用。

Azure 提供各種服務，可供您用來裝載網站和 web 應用程式。 這些服務可讓您以最愛的語言進行開發，無論是 .NET、.NET Core、JAVA、Ruby、Node.js、PHP 或 Python。
[Azure App Service Web Apps](../../app-service/overview.md) (Web Apps) 是其中一項服務。

Web Apps 將 Microsoft Azure 的強大功能新增至您的應用程式。 它包含安全性、負載平衡、自動調整和自動化管理。 您也可以利用 Web Apps 中的 DevOps 功能，例如封裝管理、預備環境、自訂網域、SSL/TLS 憑證，以及從 Azure DevOps、GitHub、Docker Hub 及其他來源進行持續部署。

Azure 提供您可用來裝載網站和 web 應用程式的其他服務。 大部分的情況下，Web Apps 是最佳選擇。 針對微服務架構，請考慮使用 [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)。
如果您需要能更加充分地掌控程式碼執行所在的 VM，則請考慮使用 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)。
如需如何在這些 Azure 服務之間進行選擇的詳細資訊，請參閱 [Azure App Service、虛擬機器、Service Fabric 和雲端服務的比較](/azure/architecture/guide/technology-choices/compute-decision-tree)。

### <a name="apply-updates-to-components"></a>將更新套用至元件

為了避免弱點，您應該持續清查您的用戶端和伺服器端元件 (例如，架構和程式庫) ，以及其更新的相依性。 新的弱點和更新的軟體版本會持續發行。 確定您有進行中的計畫，可針對您所使用的程式庫和元件，監視、分級及套用更新或設定變更。

請參閱 [開放式 Web 應用程式安全性專案 (OWASP) ](https://www.owasp.org/index.php/Main_Page) 頁面，瞭解如何 [使用具有已知弱點的元件](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities) 來取得工具建議。 您也可以訂閱電子郵件警示，以瞭解與您使用的元件相關的安全性弱點。

### <a name="use-threat-modeling-during-application-design"></a>在應用程式設計期間使用威脅模型化

威脅分析模型是找出企業和應用程式潛在安全性威脅的程式，然後確保已備妥適當的緩和措施。 SDL 指定小組應在設計階段進行威脅模型化，解決潛在問題相當簡單且符合成本效益。 在設計階段使用威脅模型，可以大幅降低開發的總成本。

為了協助進行威脅分析模型的程式，我們為 [SDL Threat Modeling Tool](threat-modeling-tool.md) 設計了非安全性專家。 這項工具會提供有關如何建立及分析威脅模型的清楚指引，讓所有開發人員更容易進行威脅分析模型。

在所有信任界限內建立應用程式設計模型和列舉 [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) 威脅（詐騙、篡改、否認、資訊洩漏、阻絕服務以及提高許可權）的模型，都證明了在早期捕捉設計錯誤的有效方式。 下表列出 STRIDE 威脅，並提供使用 Azure 所提供功能的一些範例緩和措施。 這些風險降低措施並非能在各個情況下運作。

| 威脅 | 安全性屬性 | 潛在的 Azure 平臺緩和 |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 詐騙               | 驗證        | [需要 HTTPS 連接](/aspnet/core/security/enforcing-ssl?tabs=visual-studio&view=aspnetcore-2.1)。 |
| 竄改              | 完整性             | 驗證 SSL/TLS 憑證。 使用 SSL/TLS 的應用程式必須完整驗證其所連接之實體的 x.509 憑證。 使用 Azure Key Vault 憑證來 [管理您的 x509 憑證](../../key-vault/general/about-keys-secrets-certificates.md)。 |
| 否認性            | 不可否認性       | 啟用 Azure [監視和診斷](/azure/architecture/best-practices/monitoring)。|
| 洩露資訊 | 機密性       | 加密待用和[傳輸中](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)[的敏感](../fundamentals/encryption-atrest.md)資料。 |
| 拒絕服務      | 可用性          | 監視潛在拒絕服務狀況的效能計量。 實作 IP 連線篩選。 結合應用程式設計最佳做法的[Azure DDoS 保護](../../ddos-protection/ddos-protection-overview.md#next-steps)可提供對抗 DDoS 攻擊的防禦。|
| 提高權限 | 授權         | 使用 Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)。|

### <a name="reduce-your-attack-surface"></a>減少受攻擊面

攻擊面是可能發生弱點的總總和。 在本文中，我們將焦點放在應用程式的受攻擊面。
重點是保護應用程式免于遭受攻擊。 將您的攻擊面降至最低的簡單快速方法，就是從您的應用程式中移除未使用的資源和程式碼。 您的應用程式越小，您的攻擊面就越小。 例如，移除：

- 尚未發行的功能程式碼。
- 支援程式碼的偵錯工具。
- 未使用或已淘汰的網路介面和通訊協定。
- 您未使用的虛擬機器和其他資源。

定期清除資源，並確保您移除未使用的程式碼，是確保惡意執行者遭受攻擊的絕佳方法。

若要減少受攻擊面，更詳細且深入的方法是完成攻擊面分析。 受攻擊面分析可協助您對應系統的元件，這些元件需要經過檢查並測試是否有安全性弱點。

攻擊面分析的目的是要瞭解應用程式中的風險區域，讓開發人員和安全性專家瞭解應用程式的哪些部分會受到攻擊。 然後，您可以找到將這種可能性降至最低的方式、追蹤受攻擊面的時間和方式，以及這對風險觀點的意義。

攻擊面分析可協助您識別：

- 您需要檢查並測試安全性弱點的函式和部分。
- 需要深層防禦保護的程式碼高風險範圍， (您需要防護) 的系統元件。
- 當您改變受攻擊面，而且需要重新整理威脅評量時。

若要降低攻擊者入侵潛在弱點或弱點的機會，您需要徹底分析應用程式的整體攻擊面。 它也包含停用或限制系統服務的存取權、套用最低許可權的原則，以及盡可能採用多層式防禦。

我們會討論如何在 SDL 驗證階段進行 [攻擊面審核](secure-develop.md#conduct-attack-surface-review) 。

> [!NOTE]
> **威脅分析模型和攻擊面分析之間有何差異？**
威脅分析模型是指找出應用程式的潛在安全性威脅，並確保對威脅的適當緩和措施已就緒的流程。 攻擊面分析會識別已開放攻擊的高風險程式碼區域。 它牽涉到在部署應用程式之前，找出如何保護應用程式的高風險區域，以及檢查和測試這些程式碼區域的方法。

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>採用以身分識別作為主要安全性周邊的原則

當您設計雲端應用程式時，請務必將您的安全性周邊焦點從以網路為中心的方法擴充到以身分識別為中心的方法。 在過去，主要內部部署安全性周邊是組織的網路。 大部分的內部部署安全性設計會使用網路作為主要的安全性樞紐分析表。 針對雲端應用程式，您可以將身分識別視為主要安全性周邊，以提供更好的服務。

開發以身分識別為中心的方法來開發 web 應用程式時，您可以做的事：

- 為使用者強制執行多重要素驗證。
- 使用增強式驗證與授權平台。
- 套用最低許可權原則。
- 實行即時存取。

#### <a name="enforce-multi-factor-authentication-for-users"></a>為使用者強制執行多重要素驗證

使用雙因素驗證。 雙因素驗證是目前的驗證和授權標準，因為它可避免使用者名稱和密碼類型驗證中固有的安全性弱點。 Azure 管理介面 (Azure 入口網站/遠端 PowerShell) 和客戶面向服務的存取權，應設計並設定為使用 [Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)。

#### <a name="use-strong-authentication-and-authorization-platforms"></a>使用增強式驗證與授權平臺

使用平台提供的驗證與授權機制，而不要使用自訂程式碼。 這是因為開發自訂驗證程式代碼可能很容易發生錯誤。 例如，來自 Microsoft) 的商業程式碼 (，通常會經過廣泛的安全性檢查。 [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 是用於管理身分識別和存取的 Azure 解決方案。 這些 Azure AD 的工具和服務可協助進行安全的開發：

- [Microsoft 身分識別平臺](../../active-directory/develop/index.yml) 是一組元件，可供開發人員用來建立安全地登入使用者的應用程式。 平臺可協助開發人員建立單一租使用者的企業營運 (LOB) 應用程式和開發人員想要開發多租使用者應用程式。 除了基本登入，使用 Microsoft 身分識別平臺所建立的應用程式可以呼叫 Microsoft Api 和自訂 Api。 Microsoft 身分識別平臺支援業界標準的通訊協定，例如 OAuth 2.0 和 OpenID Connect。

- [Azure Active Directory B2C (Azure AD B2C) ](../../active-directory-b2c/index.yml) 是身分識別管理服務，可用來自訂和控制客戶在使用您的應用程式時，如何註冊、登入及管理其設定檔。 這包括針對 iOS、Android 和 .NET 開發的應用程式，以及其他應用程式。 Azure AD B2C 會啟用這些動作，同時保護客戶身分識別。

#### <a name="apply-the-principle-of-least-privilege"></a>套用最低許可權原則

[最小許可權](https://en.wikipedia.org/wiki/Principle_of_least_privilege)的概念表示讓使用者進行工作所需的精確存取和控制層級，而不需要執行任何動作。

軟體發展人員需要網域系統管理員許可權嗎？ 系統管理助理需要存取其個人電腦上的系統管理控制項嗎？ 評估軟體的存取權並無不同。 如果您使用 [azure 角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md) 在您的應用程式中提供使用者不同的功能和授權，您就不會讓每個人都能存取所有專案。 藉由限制每個角色所需的存取權，您可以限制發生安全性問題的風險。

確定您的應用程式在其存取模式中強制執行 [最低許可權](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications) 。

> [!NOTE]
> 最低許可權規則必須套用至軟體和建立軟體的人員。 如果軟體發展人員獲得太多存取權，可能會對 IT 安全性造成巨大的風險。 如果開發人員有惡意意圖或獲得太多存取權，後果可能會很嚴重。 建議您在整個開發生命週期中，將最低許可權規則套用至開發人員。

#### <a name="implement-just-in-time-access"></a>實行即時存取

執行 *及時* (JIT) 存取，以進一步降低許可權的曝光時間。 使用 [Azure AD Privileged Identity Management](../../active-directory/roles/security-planning.md#stage-3-take-control-of-admin-activity) ：

- 僅提供使用者需要 JIT 的許可權。
- 指派縮短持續時間的角色，而且有信心會自動撤銷權限。

### <a name="require-re-authentication-for-important-transactions"></a>需要重新驗證重要交易

[跨網站要求偽造](/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (也稱為 *XSRF* 或 *CSRF*) 是針對 web 裝載的應用程式進行攻擊，其中惡意的 web 應用程式會影響用戶端瀏覽器與信任該瀏覽器的 web 應用程式之間的互動。 跨網站要求偽造攻擊的可能原因是，網頁瀏覽器會自動傳送某些類型的驗證權杖，並對網站提出每個要求。
這種形式的惡意探索也稱為單鍵 *攻擊* 或 *會話騎* ，因為攻擊會利用使用者先前經過驗證的會話。

防禦這類攻擊的最佳方式，是詢問使用者只有使用者可以在每一項重要交易之前提供的內容，例如購買、帳戶停用或密碼變更。 您可能會要求使用者重新輸入其密碼、完成 captcha，或提交只有使用者所擁有的秘密權杖。 最常見的方法是秘密權杖。

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>使用金鑰管理解決方案來保護金鑰、認證和其他秘密

遺失金鑰和認證是相當常見的問題。 唯一比遺失金鑰和認證更糟的情況，就是讓未經授權者能夠存取這些機密資料。 攻擊者可以利用自動化和手動技術來尋找儲存在程式碼存放庫（例如 GitHub）中的金鑰和秘密。 請勿將金鑰和密碼放在這些公用程式代碼存放庫或任何其他伺服器上。

一律將您的金鑰、憑證、秘密和連接字串放在金鑰管理解決方案中。 您可以使用集中式解決方案，將金鑰和秘密儲存在硬體安全模組 (Hsm) 。 Azure 可讓您在雲端中使用 [Azure Key Vault](../../key-vault/general/overview.md)的 HSM。

Key Vault 是 *秘密存放區*：這是用來儲存應用程式秘密的集中式雲端服務。 Key Vault 藉由將應用程式秘密保存在單一中央位置，並提供安全存取、許可權控制和存取記錄，讓您的機密資料保持安全。

秘密會儲存在個別的保存 *庫* 中。 每個保存庫都有自己的設定和安全性原則來控制存取權。 您可以透過 REST API 或透過適用于大部分程式設計語言的用戶端 SDK 來取得資料。

> [!IMPORTANT]
> Azure Key Vault 是專為儲存伺服器應用程式的設定秘密所設計。 它不適合用來儲存屬於應用程式使用者的資料。 這會反映在其效能特性、API 和成本模型中。
>
> 使用者資料應該儲存在其他地方，例如在 Azure SQL Database 實例中，透明資料加密 (TDE) 或使用 Azure 儲存體服務加密的儲存體帳戶。 您的應用程式用來存取這些資料存放區的秘密可以保存在 Azure Key Vault 中。

### <a name="protect-sensitive-data"></a>保護敏感性資料

保護資料是安全性策略中不可或缺的一部分。
將您的資料分類，並識別您的資料保護需求，協助您在設計應用程式時考慮資料安全性。 分類 (將) 儲存的資料分類為機密和業務衝擊，可協助開發人員判斷與資料相關聯的風險。

當您設計資料格式時，請將所有適用的資料標示為機密。 確定應用程式會將適用的資料視為機密。 這些做法可協助您保護敏感性資料：

- 使用加密。
- 避免硬式編碼的秘密，例如金鑰和密碼。
- 確定已備妥存取控制和審核功能。

#### <a name="use-encryption"></a>使用加密

保護資料應該是安全性策略中不可或缺的一部分。
如果您的資料儲存在資料庫中，或在位置之間來回移動，請在資料庫中使用待用 [資料](../fundamentals/encryption-atrest.md) 加密 (，同時在資料庫中) 和加密 [傳輸中的資料](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit) (在使用者、資料庫、API 或服務端點) 之間進行加密。 建議您一律使用 SSL/TLS 通訊協定來交換資料。 確定您使用最新版的 TLS 進行加密 (目前的版本為 1.2) 。

#### <a name="avoid-hard-coding"></a>避免硬式編碼

在您的軟體中，絕對不應將某些專案硬式編碼。 其中一些範例包括主機名稱或 IP 位址、Url、電子郵件地址、使用者名稱、密碼、儲存體帳戶金鑰和其他密碼編譯金鑰。 請考慮採用您的程式碼中可以或無法進行硬式編碼的需求，包括程式碼的批註區段。

當您在程式碼中加上批註時，請確定您不會儲存任何敏感性資訊。 這包括您的電子郵件地址、密碼、連接字串、您的應用程式的相關資訊，這些資訊只會由您組織中的某人知道，而其他任何可能會讓攻擊者在攻擊您的應用程式或組織方面有優勢。

基本上，假設開發專案中的所有專案都是在部署時的公開知識。 避免在專案中包含任何種類的機密資料。

稍早，我們討論了 [Azure Key Vault](../../key-vault/general/overview.md)。 您可以使用 Key Vault 來儲存金鑰和密碼等秘密，而不是對其進行硬式編碼。 當您使用 Key Vault 搭配適用于 Azure 資源的受控識別時，您的 Azure web 應用程式可以輕鬆且安全地存取秘密設定值，而不需要將任何秘密儲存在您的原始檔控制或設定中。 若要深入瞭解，請參閱 [使用 Azure Key Vault 管理您的伺服器應用程式中的秘密](/learn/modules/manage-secrets-with-azure-key-vault/)。

### <a name="implement-fail-safe-measures"></a>執行失敗安全措施

您的應用程式必須能夠以一致的方式處理執行期間所發生的 [錯誤](/dotnet/standard/exceptions/) 。 應用程式應該攔截所有錯誤，並可能會導致安全或關閉。

您也應該確保記錄的錯誤具有足夠的使用者內容，以識別可疑或惡意活動。 記錄應該保留足夠的時間，以允許延遲的取證分析。 記錄檔的格式應可供記錄管理解決方案輕鬆使用。 請確定觸發安全性相關錯誤的警示。 記錄和監視不足，可讓攻擊者進一步攻擊系統並維護持續性。

### <a name="take-advantage-of-error-and-exception-handling"></a>利用錯誤和例外狀況處理

執行正確的錯誤和 [例外狀況處理](/dotnet/standard/exceptions/best-practices-for-exceptions) 是防禦程式碼的重要部分。 錯誤和例外狀況處理是讓系統可靠且安全的關鍵。 錯誤處理中的錯誤可能會導致不同類型的安全性弱點，例如將資訊洩漏給攻擊者，以及協助攻擊者深入瞭解您的平臺與設計。

請確定：

- 您可以集中處理例外狀況，以避免程式碼中有重複的 [try/catch 區塊](/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions) 。

- 所有非預期的行為都是在應用程式內處理。

- 向使用者顯示的訊息不會流失重要的資料，但會提供足夠的資訊來說明問題。

- 系統會記錄例外狀況，並提供足夠的資訊讓辯論或事件回應小組調查。

[Azure Logic Apps](../../logic-apps/logic-apps-overview.md) 提供頂級體驗來處理相依系統所造成的 [錯誤和例外](../../logic-apps/logic-apps-exception-handling.md) 狀況。 您可以使用 Logic Apps 來建立工作流程，以自動化跨企業和組織整合應用程式、資料、系統和服務的工作和流程。

### <a name="use-logging-and-alerting"></a>使用記錄和警示

[記錄](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1) 安全性問題的安全性問題，並觸發有關問題的警示，以確保人們及時知道問題。 在所有元件上啟用稽核和記錄。 Audit 記錄檔應該會捕獲使用者內容，並識別所有重要的事件。

確認您未記錄使用者提交至網站的任何機密資料。 敏感性資料的範例包括︰

- 使用者認證
- 社會安全碼或其他識別資訊
- 信用卡號碼或其他財務資訊
- 健康狀態資訊
- 可以用來將加密資訊解密的私密金鑰或其他資料
- 可用來更有效地攻擊應用程式的系統或應用程式資訊

確定應用程式會監視使用者管理事件，例如成功和失敗的使用者登入、密碼重設、密碼變更、帳戶鎖定和使用者註冊。 記錄這些事件可協助您偵測及回應潛在的可疑行為。 它也可讓您收集作業資料，例如誰正在存取應用程式。

## <a name="next-steps"></a>後續步驟
在下列文章中，我們建議可協助您開發及部署安全應用程式的安全性控制與活動。

- [開發安全的應用程式](secure-develop.md)
- [部署安全的應用程式](secure-deploy.md)