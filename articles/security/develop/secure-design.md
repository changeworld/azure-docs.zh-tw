---
title: 在 Microsoft Azure 上設計安全的應用程式
description: 本文討論在 web 應用程式專案的需求和設計階段中，應考慮的最佳做法。
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
ms.openlocfilehash: d0cffbd1fa09abef9853e0ef853696c3c8ed353c
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246804"
---
# <a name="design-secure-applications-on-azure"></a>在 Azure 上設計安全的應用程式
在本文中，我們會提供您在設計雲端應用程式時應考慮的安全性活動和控制項。 在 Microsoft 安全性開發週期的需求和設計階段中，訓練資源以及要考慮的安全性問題和概念[ (SDL) ](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)涵蓋在內。 其目標是要協助您定義活動和 Azure 服務，您可以用來設計更安全的應用程式。

本文章涵蓋下列 SDL 階段：

- 訓練
- 需求
- 設計

## <a name="training"></a>訓練
在您開始開發雲端應用程式之前，請花一點時間瞭解 Azure 上的安全性和隱私權。 藉由採取此步驟，您可以減少應用程式中可利用的弱點數目和嚴重性。 您將更準備好適當地回應不斷變動的威脅環境。

在訓練階段使用下列資源，讓您自己熟悉可供開發人員使用的 Azure 服務，以及 Azure 上的安全性最佳作法：

  - [Azure 開發人員指南](https://azure.microsoft.com/campaigns/developer-guide/)說明如何開始使用 azure。 本指南說明您可以用來執行應用程式、儲存資料、整合智慧、建立 IoT 應用程式，以及以更有效率且更安全的方式部署解決方案的服務。

  - [Azure 開發人員的快速入門手冊針對](../../guides/developer/azure-developer-guide.md)想要開始使用 Azure 平臺以滿足其開發需求的開發人員，提供基本資訊。

  - [Sdk 和工具](https://docs.microsoft.com/azure/index?pivot=sdkstools)說明 Azure 上可用的工具。

  - [Azure DevOps Services](https://docs.microsoft.com/azure/devops/)提供開發共同作業工具。 這些工具組括高效能管線、免費的 Git 存放庫、可設定的看板面板，以及廣泛的自動化和雲端負載測試。
    [DevOps 資源中心](https://docs.microsoft.com/azure/devops/learn/)結合了我們的資源，以取得學習 DevOps 實務、Git 版本控制、agile 方法、我們如何在 Microsoft 中使用 DevOps，以及如何評估自己的 DevOps 進展。

  - 在[推送到生產環境之前，要考慮的前5個安全性專案](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca)會示範如何協助保護您在 Azure 上的 web 應用程式，並保護您的應用程式免于受到最常見且危險的 web 應用程式攻擊。

  - [適用于 azure 的安全 DevOps 套件](https://azsk.azurewebsites.net/index.html)是腳本、工具、延伸模組和自動化的集合，可已經考慮使用廣泛自動化之 DevOps 團隊的完整 Azure 訂用帳戶和資源安全性需求。 適用于 Azure 的安全 DevOps 套件可以向您示範如何將安全性順暢地整合到您的原生 DevOps 工作流程。 此套件處理 (SVTs) 的安全性驗證測試之類的工具，可協助開發人員撰寫安全的程式碼，並在編碼和早期開發階段測試其雲端應用程式的安全設定。

  - [Azure 解決方案的安全性最佳作法](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions)提供了一組安全性最佳作法，可讓您在使用 Azure 設計、部署和管理雲端解決方案時使用。

## <a name="requirements"></a>需求
需求定義階段是定義應用程式的重要步驟，以及發行時將會執行的動作。 需求階段也是考慮您將在應用程式中建立之安全性控制項的一段時間。 在此階段中，您也會開始在 SDL 中採取的步驟，以確保您可以發行和部署安全的應用程式。

### <a name="consider-security-and-privacy-issues"></a>考慮安全性和隱私權問題
這個階段是考慮基礎安全性和隱私權問題的最佳時機。 在專案開始時定義可接受的安全性和隱私權層級有助於小組：

- 瞭解與安全性問題相關的風險。
- 識別並修正開發期間的安全性錯誤。
- 在整個專案中套用已建立的安全性和隱私權層級。

當您撰寫應用程式的需求時，請務必考慮可協助保護應用程式和資料安全的安全性控制項。

### <a name="ask-security-questions"></a>詢問安全性問題
詢問安全性問題，例如：

  - 我的應用程式是否包含敏感性資料？

  - 我的應用程式是否會收集或儲存要求我遵守業界標準和合規性計畫的資料，例如[《聯邦金融機構檢查委員會 (FFIEC) ](../blueprints/ffiec-analytics-overview.md)或「[支付卡產業資料安全標準」 (PCI DSS) ](../blueprints/pcidss-analytics-overview.md)？

  - 我的應用程式會收集或包含機密個人或客戶資料，不論是自己或其他資訊，都可以用來識別、聯絡或尋找單一人員嗎？

  - 我的應用程式會收集或包含可用來存取個人醫療、教育、財務或雇用資訊的資料嗎？ 識別您的資料在需求階段的敏感性可協助您將資料分類，並識別您將用於應用程式的資料保護方法。

  - 我的資料在哪裡和如何儲存？ 請考慮您將如何監視應用程式用於任何非預期變更的儲存體服務 (例如回應時間較慢) 。 您將能夠影響記錄，以收集更詳細的資料並深入分析問題嗎？

  - 我的應用程式是否可供網際網路上的公用 () 或內部使用？ 如果您的應用程式可供公開使用，您要如何保護可能收集成不正確的資料？ 如果您的應用程式僅供內部使用，請考慮您組織中的哪些人應該具有應用程式的存取權，以及他們應該有多久的存取權。

  - 在開始設計您的應用程式之前，您是否瞭解您的身分識別模型？ 您要如何判斷使用者的身分為何，以及使用者有權執行哪些動作？

  - 我的應用程式會執行敏感或重要的工作， (例如轉移 money、解除鎖定門，或傳遞醫學) ？
    請考慮如何驗證執行敏感性工作的使用者已獲授權執行此工作，以及如何驗證該人員是他們所說的。 授權 (AuthZ) 是授與已驗證的安全性主體許可權來執行某個動作的作業。 驗證 (驗證) 是合法認證合作物件的動作。

  - 我的應用程式是否會執行任何具風險的軟體活動，像是允許使用者上傳或下載檔案或其他資料？ 如果您的應用程式確實執行有風險的活動，請考慮您的應用程式如何保護使用者不受惡意檔案或資料的處理。

### <a name="review-owasp-top-10"></a>複習 OWASP 前10名
請考慮查看[<span class="underline">OWASP 前10名應用程式安全性風險</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)。
OWASP 前10個可解決 web 應用程式的重大安全性風險。
認知這些安全性風險可協助您做出需求和設計決策，將應用程式中的這些風險降到最低。

考慮安全性控制以防止缺口是很重要的。
不過，您也會想要假設會發生[缺口](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops)。 假設缺口有助於事先回答一些關於安全性的重要問題，所以不需要在緊急情況下回答：

  - 我要如何偵測到攻擊？

  - 如果發生攻擊或缺口，我該怎麼做？

  - 我要如何從攻擊中復原，例如資料洩漏或遭到篡改？

## <a name="design"></a>設計

設計階段對於建立設計和功能規格的最佳作法非常重要。 執行風險分析也很重要，這有助於減輕整個專案的安全性和隱私權問題。

當您有安全性需求並使用安全的設計概念時，您可以避免或減少安全性缺陷的機會。 安全性缺陷是應用程式設計的監督，可能會讓使用者在您的應用程式發行之後執行惡意或非預期的動作。

在設計階段，也要考慮如何在層級套用安全性;一個防禦層級並不一定足夠。 如果攻擊者 (WAF) 的 web 應用程式防火牆之後，會發生什麼事？ 您想要有另一個安全性控制措施來防禦該攻擊。

記住這一點之後，我們將討論下列安全設計概念，以及您在設計安全應用程式時應解決的安全性控制項：

- 使用安全的程式碼撰寫程式庫和軟體架構。
- 掃描易受攻擊的元件。
- 在應用程式設計期間使用威脅模型。
- 減少受攻擊面。
- 採用身分識別原則做為主要安全性周邊。
- 需要重新驗證重要交易。
- 使用金鑰管理解決方案來保護金鑰、認證和其他秘密。
- 保護敏感性資料。
- 執行失敗安全措施。
- 利用錯誤和例外狀況處理。
- 使用記錄和警示。

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>使用安全的程式碼撰寫程式庫和軟體架構

若要進行開發，請使用安全的程式碼撰寫程式庫，以及具有內嵌安全性的軟體架構。 開發人員可以使用現有、經過證實的功能 (加密、輸入清理、輸出編碼、金鑰或連接字串，以及任何其他會被視為安全性控制) ，而不是從頭開發安全性控制項。 這有助於防範安全性相關的設計和實施瑕疵。

請確定您使用的是 framework 的最新版本，以及架構中可用的所有安全性功能。 Microsoft 為所有開發人員提供了一[組完整的開發工具](https://azure.microsoft.com/product-categories/developer-tools/)，以處理任何平臺或語言，以提供雲端應用程式。 您可以從各種[sdk](https://azure.microsoft.com/downloads/)中選擇，以您選擇的語言撰寫程式碼。
您可以利用功能完整的整合式開發環境， (Ide) 和具備先進的偵錯工具功能的編輯器，以及內建 Azure 支援。

Microsoft 提供各種不同的[語言、架構和工具](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all)，可讓您用來在 Azure 上開發應用程式。 例如，[適用于 .net 和 .Net Core 開發人員的 Azure](https://docs.microsoft.com/dotnet/azure/)。 針對我們所提供的每個語言和架構，您可以找到快速入門、教學課程和 API 參考，以協助您快速入門。

Azure 提供您可用來裝載網站和 web 應用程式的各種服務。 這些服務可讓您以您慣用的語言進行開發，無論是 .NET、.NET Core、JAVA、Ruby、Node.js、PHP 或 Python。
[Azure App Service Web Apps](../../app-service/overview.md) (Web Apps) 是其中一項服務。

Web Apps 將 Microsoft Azure 的威力新增至您的應用程式。 其中包括安全性、負載平衡、自動調整和自動化管理。 您也可以利用 Web Apps 中的 DevOps 功能，例如套件管理、預備環境、自訂網域、SSL/TLS 憑證，以及從 Azure DevOps、GitHub、Docker Hub 和其他來源進行持續部署。

Azure 提供您可用來裝載網站和 web 應用程式的其他服務。 大部分的情況下，Web Apps 是最佳選擇。 針對微服務架構，請考慮使用[Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)。
如果您需要能更加充分地掌控程式碼執行所在的 VM，則請考慮使用 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)。
如需如何在這些 Azure 服務之間進行選擇的詳細資訊，請參閱[Azure App Service、虛擬機器、Service Fabric 和雲端服務的比較](/azure/architecture/guide/technology-choices/compute-decision-tree)。

### <a name="apply-updates-to-components"></a>將更新套用至元件

為避免弱點，您應該持續清查用戶端和伺服器端元件 (例如，) 的架構和程式庫，以及更新的相依性。 新的弱點和更新的軟體版本會持續發行。 請確定您有進行中的計畫來監視、分級，並將更新或設定變更套用至您所使用的程式庫和元件。

請參閱[Open Web Application Security Project (OWASP) ](https://www.owasp.org/index.php/Main_Page)頁面上有關[使用具有已知弱點的元件](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities)來取得工具建議。 您也可以訂閱電子郵件警示，以取得與您所使用之元件相關的安全性弱點。

### <a name="use-threat-modeling-during-application-design"></a>在應用程式設計期間使用威脅模型化

威脅模型化是識別您企業和應用程式潛在安全性威脅的程式，然後確保適當的緩和措施已就緒。 SDL 指定小組應該在設計階段進行威脅模型化，而解決潛在問題相當簡單且符合成本效益。 在設計階段使用威脅模型，可以大幅降低開發的總成本。

為了協助進行威脅模型化程式，我們設計了[SDL Threat Modeling Tool](threat-modeling-tool.md) ，並在非安全性專家的情況下加以考慮。 這項工具藉由提供如何建立和分析威脅模型的清楚指引，讓所有開發人員更容易進行威脅模型化。

將應用程式設計模型化，並列舉[STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)威脅（詐騙、篡改、否認、資訊洩漏、阻絕服務及提高許可權）：在所有信任界限中，已證明有效的方法，可以及早攔截設計錯誤。 下表列出 STRIDE 威脅，並提供一些使用 Azure 所提供功能的範例緩和措施。 這些風險降低措施並非能在各個情況下運作。

| 威脅 | 安全性屬性 | 潛在的 Azure 平臺緩和措施 |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 詐騙               | 驗證        | [需要 HTTPS 連接](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio)。 |
| 竄改              | 完整性             | 驗證 SSL/TLS 憑證。 使用 SSL/TLS 的應用程式必須完整驗證其所連接實體的 x.509 憑證。 使用 Azure Key Vault 憑證來[管理 x509 憑證](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates)。 |
| 否認性            | 不可否認性       | 啟用 Azure [監視和診斷](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)。|
| 洩露資訊 | 機密性       | 加密待用和[傳輸中](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)[的敏感](../fundamentals/encryption-atrest.md)資料。 |
| 拒絕服務      | 可用性          | 監視潛在拒絕服務狀況的效能計量。 實作 IP 連線篩選。 [Azure DDoS 保護](../../virtual-network/ddos-protection-overview.md#next-steps)與應用程式設計最佳作法結合，可提供對抗 DDoS 攻擊的防線。|
| 提高權限 | 授權         | 使用 Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)。|

### <a name="reduce-your-attack-surface"></a>減少受攻擊面

受攻擊面是可能發生弱點的總總和。 在本文中，我們將焦點放在應用程式的受攻擊面。
重點在於保護應用程式免于遭受攻擊。 簡單快速地將您的受攻擊面降到最低，就是從您的應用程式中移除未使用的資源和程式碼。 您的應用程式越小，您的攻擊面就越小。 例如，移除：

- 尚未發行之功能的程式碼。
- 支援程式碼的偵錯工具。
- 未使用或已被取代的網路介面和通訊協定。
- 您未使用的虛擬機器和其他資源。

定期清除資源，並確保移除未使用的程式碼，是確保惡意執行者遭受攻擊的機會較少的絕佳方式。

若要減少受攻擊面，更詳細且深入的方法是完成攻擊面分析。 攻擊面分析可協助您對應系統的各個部分，這些元件需要經過檢查並測試是否有安全性弱點。

攻擊面分析的目的是要瞭解應用程式中的風險區域，讓開發人員和安全性專家知道應用程式的哪些部分已開放攻擊。 然後，您可以找到將這種可能性降到最低、追蹤攻擊面和如何變更的方式，以及這對於風險觀點的意義。

攻擊面分析可協助您識別：

- 系統的函式和部分，您必須檢查並測試是否有安全性弱點。
- 需要深層防禦保護的程式碼的高風險部分， (系統的部分，您必須加以防護才能) 。
- 當您改變受攻擊面，而且需要重新整理威脅評估時。

降低攻擊者入侵潛在弱點或弱點的機會，需要您徹底分析應用程式的整體攻擊面。 它也包括停用或限制系統服務的存取、套用最低許可權的原則，以及盡可能採用多層防禦。

我們會討論在 SDL 的驗證階段進行[攻擊面審查](secure-develop.md#conduct-attack-surface-review)。

> [!NOTE]
> **威脅模型和攻擊面分析有何不同？**
威脅模型化是識別應用程式潛在安全性威脅的過程，並確保對威脅的適當緩和措施已就緒。 攻擊面分析會識別已開放攻擊的高風險程式碼區域。 其中包括尋找方法來保護應用程式的高風險區域，以及在部署應用程式之前，檢查和測試這些部分的程式碼。

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>採用以身分識別作為主要安全性周邊的原則

當您設計雲端應用程式時，請務必將您的安全性周邊焦點從以網路為中心的方法擴充到以身分識別為中心的方法。 在過去，主要內部部署安全性周邊是組織的網路。 大部分的內部部署安全性設計都會使用網路做為主要的安全性樞紐分析表。 針對雲端應用程式，您可以將身分識別視為主要安全性周邊，以獲得更好的服務。

開發以身分識別為中心的方法來開發 web 應用程式時，您可以做的事：

- 對使用者強制執行多重要素驗證。
- 使用增強式驗證與授權平台。
- 套用最低許可權的原則。
- 執行即時存取。

#### <a name="enforce-multi-factor-authentication-for-users"></a>對使用者強制執行多重要素驗證

使用雙因素驗證。 雙因素驗證是目前的驗證和授權標準，因為它可避免使用者名稱和密碼類型的驗證中固有的安全性弱點。 存取 Azure 管理介面 (Azure 入口網站/遠端 PowerShell) 以及客戶面向服務，應設計並設定為使用[Azure 多重要素驗證](../../active-directory/authentication/concept-mfa-howitworks.md)。

#### <a name="use-strong-authentication-and-authorization-platforms"></a>使用增強式驗證與授權平臺

使用平台提供的驗證與授權機制，而不要使用自訂程式碼。 這是因為開發自訂驗證程式代碼可能會很容易發生錯誤。 例如，從 Microsoft) 的商業程式碼 (經常會針對安全性進行廣泛的審查。 [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 是用於管理身分識別和存取的 Azure 解決方案。 這些 Azure AD 工具和服務可協助進行安全的開發：

- [Microsoft 身分識別平臺](/azure/active-directory/develop/)是一組元件，可供開發人員用來建立安全地登入使用者的應用程式。 此平臺可協助建立單一租使用者、企業營運 (LOB) 應用程式的開發人員，以及想要開發多租使用者應用程式的開發人員。 除了基本登入以外，使用 Microsoft 身分識別平臺所建立的應用程式也可以呼叫 Microsoft Api 和自訂 Api。 Microsoft 身分識別平臺支援業界標準通訊協定，例如 OAuth 2.0 和 OpenID Connect。

- [Azure Active Directory B2C (Azure AD B2C) ](../../active-directory-b2c/index.yml)是一項身分識別管理服務，可用來自訂和控制客戶在使用您的應用程式時，如何註冊、登入及管理其設定檔。 這包括針對 iOS、Android 和 .NET 開發的應用程式，以及其他專案。 Azure AD B2C 會啟用這些動作，同時保護客戶身分識別。

#### <a name="apply-the-principle-of-least-privilege"></a>套用最低許可權原則

[最低許可權](https://en.wikipedia.org/wiki/Principle_of_least_privilege)的概念意味著讓使用者能夠進行其作業所需的精確存取和控制層級，而不需要執行其他動作。

軟體發展人員需要網域系統管理員許可權嗎？ 系統管理助理需要存取其個人電腦上的管理控制項嗎？ 評估軟體的存取權並不相同。 如果您使用[角色型存取控制 (RBAC) ](../../role-based-access-control/overview.md)在您的應用程式中為使用者提供不同的功能和授權，您就不會讓所有人都能存取所有專案。 藉由限制每個角色所需的存取權，您可以限制發生安全性問題的風險。

請確定您的應用程式在其存取模式中強制執行[最低許可權](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications)。

> [!NOTE]
> 最低許可權的規則必須套用至軟體和建立軟體的人員。 如果軟體發展人員被賦予太多存取權，可能會對 IT 安全性造成巨大的風險。 如果開發人員有惡意的意圖或有太多存取權，可能會產生嚴重的後果。 我們建議您在整個開發週期中，將最低許可權規則套用至開發人員。

#### <a name="implement-just-in-time-access"></a>執行即時存取

執行*即時* (JIT) 存取，以進一步降低許可權的曝光時間。 使用[Azure AD Privileged Identity Management](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-take-control-of-admin-activity) ：

- 授與使用者只需要 JIT 的許可權。
- 指派縮短持續時間的角色，而且有信心會自動撤銷權限。

### <a name="require-re-authentication-for-important-transactions"></a>需要重新驗證重要交易

[跨網站要求偽造](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1) (也稱為*XSRF*或*CSRF*) 是對 web 裝載應用程式的攻擊，其中惡意的 web 應用程式會影響用戶端瀏覽器與信任該瀏覽器之 web 應用程式之間的互動。 由於網頁瀏覽器會在每次要求網站時自動傳送某些類型的驗證權杖，因此可能會發生跨網站偽造要求攻擊。
這種形式的攻擊也稱為單鍵*攻擊*或*會話騎*，因為攻擊會利用使用者先前已驗證的會話。

防禦這類攻擊的最佳方式，就是詢問使用者只有使用者可以在每個重要交易之前提供的專案，例如購買、帳戶停用或密碼變更。 您可能會要求使用者重新輸入密碼、完成 captcha，或提交只有使用者會擁有的秘密權杖。 最常見的方法是秘密權杖。

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>使用金鑰管理解決方案來保護金鑰、認證和其他秘密

遺失金鑰和認證是相當常見的問題。 唯一比遺失金鑰和認證更糟的情況，就是讓未經授權者能夠存取這些機密資料。 攻擊者可以利用自動化和手動技術，來尋找儲存在程式碼存放庫（例如 GitHub）中的金鑰和秘密。 請勿將金鑰和密碼放在這些公用程式代碼存放庫或任何其他伺服器上。

請一律將您的金鑰、憑證、秘密和連接字串放在金鑰管理解決方案中。 您可以使用集中式解決方案，將金鑰和密碼儲存在硬體安全性模組中， (Hsm) 。 Azure 可讓您透過[Azure Key Vault](../../key-vault/general/overview.md)在雲端中提供 HSM。

Key Vault 是「*秘密存放區*」：這是用來儲存應用程式秘密的集中式雲端服務。 Key Vault 藉由將應用程式秘密保存在單一集中位置，並提供安全存取、許可權控制和存取記錄，來保護機密資料的安全。

秘密會儲存在個別的保存*庫*中。 每個保存庫都有自己的設定和安全性原則，以控制存取權。 您可以透過 REST API 或透過適用于大部分程式設計語言的用戶端 SDK，取得您的資料。

> [!IMPORTANT]
> Azure Key Vault 是設計用來儲存伺服器應用程式的設定秘密。 它不適合用來儲存屬於應用程式使用者的資料。 這會反映在其效能特性、API 和成本模型中。
>
> 使用者資料應該儲存在其他地方，例如在具有透明資料加密 () TDE 的 Azure SQL Database 實例中，或在使用 Azure 儲存體服務加密的儲存體帳戶中。 您的應用程式用來存取這些資料存放區的秘密可以保存在 Azure Key Vault 中。

### <a name="protect-sensitive-data"></a>保護敏感性資料

保護資料是安全性策略中不可或缺的一部分。
將您的資料分類並識別您的資料保護需求，可協助您在設計應用程式時考慮資料安全性。 分類 (依敏感度和業務影響來分類) 儲存的資料，可協助開發人員判斷與資料相關聯的風險。

當您設計資料格式時，請將所有適用的資料標記為機密。 請確定應用程式會將適用的資料視為機密。 這些作法可協助您保護您的敏感性資料：

- 使用加密。
- 避免硬式編碼密碼，例如金鑰和密碼。
- 確定已備妥存取控制和審核。

#### <a name="use-encryption"></a>使用加密

保護資料應該是安全性策略中不可或缺的一部分。
如果您的資料儲存在資料庫中，或在位置間來回移動，請在資料庫中使用待用[資料](../fundamentals/encryption-atrest.md)的加密 (，同時) 和使用者、資料庫、API 或服務端點) [傳送 (傳輸中的資料](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)加密。 我們建議您一律使用 SSL/TLS 通訊協定來交換資料。 請確定您使用最新版本的 TLS 進行加密 (目前的版本為 1.2) 。

#### <a name="avoid-hard-coding"></a>避免硬式編碼

在您的軟體中，有些東西絕對不會硬式編碼。 其中一些範例包括主機名稱或 IP 位址、Url、電子郵件地址、使用者名稱、密碼、儲存體帳戶金鑰和其他密碼編譯金鑰。 請考慮在您的程式碼中，針對可以或無法硬式編碼的內容（包括程式碼的批註區段）來執行需求。

當您在程式碼中放入批註時，請確定您不會儲存任何敏感性資訊。 這包括您的電子郵件地址、密碼、連接字串，以及您的應用程式的相關資訊，這只是貴組織中的人員所知，而其他任何可能提供攻擊者在攻擊您的應用程式或組織方面的優勢。

基本上，假設開發專案中的所有內容在部署時都是公開知識。 避免在專案中包含任何種類的機密資料。

稍早，我們已討論[Azure Key Vault](../../key-vault/general/overview.md)。 您可以使用 Key Vault 來儲存金鑰和密碼等秘密，而不是硬式編碼。 當您使用 Key Vault 搭配 Azure 資源的受控識別時，您的 Azure web 應用程式可以輕鬆且安全地存取秘密設定值，而不需要將任何秘密儲存在您的原始檔控制或設定中。 若要深入瞭解，請參閱[使用 Azure Key Vault 管理伺服器應用程式中的秘密](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/)。

### <a name="implement-fail-safe-measures"></a>執行失敗安全措施

您的應用程式必須能夠以一致的方式處理執行期間所發生的[錯誤](https://docs.microsoft.com/dotnet/standard/exceptions/)。 應用程式應該會攔截所有錯誤，而且可能會導致無法安全或已關閉。

您也應該確保記錄錯誤的使用者內容足以識別可疑或惡意的活動。 記錄應保留足夠的時間，以允許延遲的法庭分析。 記錄的格式應該是可供記錄管理解決方案輕鬆使用的格式。 請確定已觸發與安全性相關之錯誤的警示。 記錄和監視不足，可讓攻擊者進一步攻擊系統並維持持續性。

### <a name="take-advantage-of-error-and-exception-handling"></a>利用錯誤和例外狀況處理

執行正確的錯誤和[例外狀況處理](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions)是防禦性程式碼中很重要的一環。 錯誤和例外狀況處理是讓系統可靠且安全的關鍵。 錯誤處理的錯誤可能會導致不同種類的安全性弱點，例如將資訊洩漏給攻擊者，並協助攻擊者深入瞭解您的平臺和設計。

請確定：

- 您會以集中方式處理例外狀況，以避免程式碼中有重複的[try/catch 區塊](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions)。

- 所有未預期的行為都會在應用程式內處理。

- 向使用者顯示的訊息不會洩漏重要資料，但會提供足夠的資訊來說明問題。

- 系統會記錄例外狀況，並提供足夠的資訊供取證或事件回應小組進行調查。

[Azure Logic Apps](../../logic-apps/logic-apps-overview.md)提供第一級的經驗來處理相依系統所造成的[錯誤和例外](../../logic-apps/logic-apps-exception-handling.md)狀況。 您可以使用 Logic Apps 來建立工作流程，以自動化將應用程式、資料、系統和服務整合到企業和組織的作業和流程。

### <a name="use-logging-and-alerting"></a>使用記錄和警示

[記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)安全性問題以進行安全性調查，並觸發問題的相關警示，以確保人們能夠及時瞭解問題。 在所有元件上啟用稽核和記錄。 Audit 記錄應該會捕獲使用者內容，並識別所有重要事件。

檢查您是否不會記錄使用者提交至網站的任何敏感性資料。 敏感性資料的範例包括︰

- 使用者認證
- 社會安全號碼或其他識別資訊
- 信用卡號碼或其他財務資訊
- 健康狀態資訊
- 可用來解密加密資訊的私用金鑰或其他資料
- 可用來更有效地攻擊應用程式的系統或應用程式資訊

請確定應用程式會監視使用者管理事件，例如成功和失敗的使用者登入、密碼重設、密碼變更、帳戶鎖定和使用者註冊。 記錄這些事件可協助您偵測並回應潛在的可疑行為。 它也可讓您收集作業資料，例如誰正在存取應用程式。

## <a name="next-steps"></a>後續步驟
在下列文章中，我們建議可協助您開發及部署安全應用程式的安全性控制和活動。

- [開發安全的應用程式](secure-develop.md)
- [部署安全的應用程式](secure-deploy.md)
