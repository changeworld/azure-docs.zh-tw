---
title: Microsoft Azure 上設計安全應用程式
description: 本文討論了在 Web 應用程式專案的需求和設計階段需要考慮的最佳做法。
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
ms.openlocfilehash: c9e3cfa689f2e528f4d20e796017ae9d91c29fe2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461713"
---
# <a name="design-secure-applications-on-azure"></a>在 Azure 上設計安全應用程式
在本文中,我們將介紹在為雲設計應用程式時要考慮的安全活動和控制措施。 培訓資源以及 Microsoft[安全開發生命週期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的要求和設計階段需要考慮的安全問題和概念。 目標是説明您定義可用於設計更安全應用程式的活動和 Azure 服務。

本文將介紹以下 SDL 階段:

- 訓練
- 需求
- 設計

## <a name="training"></a>訓練
在開始開發雲應用程式之前,請花時間瞭解 Azure 上的安全性和隱私。 通過執行此步驟,可以減少應用程式中可利用漏洞的數量和嚴重性。 您將更準備對不斷變化的威脅形勢做出適當的反應。

在訓練階段使用以下資源來熟悉開發人員可用的 Azure 服務以及 Azure 上的安全最佳實務:

  - [Azure 開發人員指南](https://azure.microsoft.com/campaigns/developer-guide/)示範如何開始使用Azure。 本指南顯示可以使用哪些服務來運行應用程式、儲存數據、整合智慧、構建IoT應用以及以更高效、更安全的方式部署解決方案。

  - [Azure 開發人員入門指南](../../guides/developer/azure-developer-guide.md)為希望開始使用 Azure 平臺以滿足其開發需求的開發人員提供了重要資訊。

  - [SDK 和工具](https://docs.microsoft.com/azure/index?pivot=sdkstools)描述了 Azure 上可用的工具。

  - [Azure DevOps 服務](https://docs.microsoft.com/azure/devops/)提供開發協作工具。 這些工具包括高性能管道、免費 Git 儲存庫、可配置看板以及廣泛的自動化和基於雲端的負載測試。
    [DevOps 資源中心](https://docs.microsoft.com/azure/devops/learn/)整合了我們的資源,用於學習 DevOps 實踐、Git 版本控制、敏捷方法、我們如何在 Microsoft 使用 DevOps 以及如何評估自己的 DevOps 進度。

  - [在推送到生產之前要考慮的 5 個安全項](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca)將介紹如何幫助保護 Azure 上的 Web 應用程式,並保護您的應用免受最常見和危險的 Web 應用程式攻擊。

  - [Azure 的安全 DevOps 工具套件](https://azsk.azurewebsites.net/index.html)是腳本、工具、擴展和自動化的集合,可滿足使用廣泛自動化的 DevOps 團隊的全面 Azure 訂閱和資源安全需求。 Azure 的安全 DevOps 工具組可以向您展示如何將安全性順利整合到本機 DevOps 工作流中。 該工具套件解決了安全驗證測試 (SVT) 等工具,這些工具可説明開發人員編寫安全代碼,並在編碼和早期開發階段測試其雲端應用程式的安全配置。

  - [Azure 解決方案的安全最佳實作](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions)提供了一系列安全最佳實務,用於使用 Azure 設計、部署和管理雲端解決方案。

## <a name="requirements"></a>需求
需求定義階段是定義應用程式是什麼以及應用程式發佈後將執行哪些操作的關鍵步驟。 需求階段也是考慮將構建到應用程式中的安全控制的時間。 在此階段,您還將開始在整個 SDL 中執行的步驟,以確保釋放和部署安全應用程式。

### <a name="consider-security-and-privacy-issues"></a>考慮安全和隱私問題
此階段是考慮基本安全和隱私問題的最佳時機。 在項目開始時定義可接受的安全和隱私級別有助於團隊:

- 瞭解與安全問題相關的風險。
- 在開發過程中識別和修復安全錯誤。
- 在整個專案中應用既定的安全和隱私級別。

編寫應用程式的要求時,請務必考慮有助於保護應用程序和數據安全的安全控制。

### <a name="ask-security-questions"></a>提出安全問題
提出安全問題,如:

  - 我的應用程式是否包含敏感數據?

  - 我的應用程式是否收集或存儲需要我遵守行業標準和合規計劃的數據,如[聯邦金融機構審查委員會 (FFIEC)](../blueprints/ffiec-analytics-overview.md)或[支付卡行業數據安全標準 (PCI DSS)?](../blueprints/pcidss-analytics-overview.md)

  - 我的應用程式是否收集或包含敏感個人或客戶數據,這些數據可以單獨使用或與其他資訊一起使用,以識別、聯繫或定位一個人?

  - 我的應用程式是否收集或包含可用於訪問個人醫療、教育、財務或就業資訊的數據? 識別數據在需求階段的敏感性可説明您對數據進行分類,並確定將用於應用程式的數據保護方法。

  - 我的數據存儲在何處以及如何存儲? 考慮如何監視應用程式用於任何意外更改(如回應時間變慢)的存儲服務。 您是否能夠影響日誌記錄以收集更詳細的數據並深入分析問題?

  - 我的申請是向公眾開放(在互聯網上)還是僅在內部? 如果您的應用程式可供公眾使用,您如何保護可能收集的數據被錯誤使用? 如果應用程式僅在內部可用,請考慮組織中誰應有權訪問該應用程式以及他們應該具有訪問許可權的時間。

  - 在開始設計應用程式之前,您是否瞭解您的身份模型? 您如何確定使用者是誰,他們稱他們是誰,以及使用者有權做什麼?

  - 我的應用程式是否執行敏感或重要的任務(如轉帳、開門或送葯)?
    考慮如何驗證執行敏感任務的使用者是否有權執行該任務,以及如何驗證此人是否屬於他們所說的人。 授權 (AuthZ) 是授予經過身份驗證的安全主體執行某些操作的許可權的行為。 身份驗證 (AuthN) 是向一方質疑合法憑據的行為。

  - 我的應用程式是否執行任何危險的軟體活動,例如允許使用者上傳或下載檔或其他數據? 如果應用程式確實執行了危險的活動,請考慮應用程式將如何保護使用者不處理惡意檔或數據。

### <a name="review-owasp-top-10"></a>檢視 OWASP 前 10 名
考慮審查[<span class="underline">OWASP 十大應用程式安全風險</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)。
OWASP 前 10 名解決了 Web 應用程式面臨的嚴重安全風險。
瞭解這些安全風險可以説明您做出要求和設計決策,將應用程式中的這些風險降至最低。

考慮安全控制以防止違規很重要。
但是,您還希望假定會發生[違規](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops)。 假設違規有助於提前回答一些有關安全性的重要問題,因此不必在緊急情況下回答:

  - 如何檢測攻擊?

  - 如果發生攻擊或違規,我該怎麼辦?

  - 如何從攻擊(如數據洩漏或篡改)中恢復?

## <a name="design"></a>設計

設計階段對於為設計和功能規範建立最佳實踐至關重要。 對於執行風險分析,幫助緩解整個專案的安全和隱私問題,這一點也至關重要。

當您有安全要求並使用安全設計概念時,可以避免或盡量減少出現安全漏洞的機會。 安全缺陷是應用程式設計中的一種疏忽,可能允許使用者在應用程式發佈後執行惡意或意外操作。

在設計階段,還要考慮如何在層中應用安全性;一級的防守不一定足夠。 如果攻擊者通過 Web 應用程式防火牆 (WAF),會發生什麼情況? 您希望有另一個安全控制來防禦該攻擊。

考慮到這一點,我們討論了以下安全設計概念以及您在設計安全應用程式時應解決的安全控制:

- 使用安全編碼庫和軟體框架。
- 掃描易受攻擊的元件。
- 在應用程式設計期間使用威脅建模。
- 減少攻擊面。
- 採用身份策略作為主要安全邊界。
- 要求對重要事務重新認證。
- 使用金鑰管理解決方案保護金鑰、憑據和其他機密。
- 保護敏感數據。
- 實施故障安全措施。
- 利用錯誤和異常處理。
- 使用日誌記錄和警報。

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>使用安全編碼庫與軟體框架

對於開發,請使用安全編碼庫和具有嵌入式安全性的軟體框架。 開發人員可以使用現有的、經過驗證的功能(加密、輸入衛生、輸出編碼、密鑰或連接字串,以及任何被視為安全控制的功能),而不是從頭開始開發安全控制。 這有助於防止與安全相關的設計和實現缺陷。

確保您使用的是框架的最新版本以及框架中提供的所有安全功能。 Microsoft 為所有開發人員提供了一[套全面的開發工具](https://azure.microsoft.com/product-categories/developer-tools/),用於任何平臺或語言,以交付雲端應用程式。 您可以通過從各種[SDK](https://azure.microsoft.com/downloads/)中選擇使用您選擇的語言編寫代碼。
您可以利用具有進階除錯功能和內建 Azure 支援的功能齊全的整合式開發環境 (II) 和編輯器。

Microsoft 提供了各種[語言、框架和工具,](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all)可用於在 Azure 上開發應用程式。 例如[,Azure 為 .NET 和 .NET 核心開發人員](https://docs.microsoft.com/dotnet/azure/)。 對於我們提供的每種語言和框架,您將找到快速入門、教程和 API 引用,以説明您快速入門。

Azure 提供了可用於託管網站和 Web 應用程式的各種服務。 這些服務允許您使用您最喜愛的語言進行開發,無論是 .NET、.NET Core、Java、Ruby、Node.js、PHP 還是 Python。
[Azure 應用服務 Web 應用](../../app-service/overview.md)(Web 應用)是這些服務之一。

Web 應用將 Microsoft Azure 的強大功能添加到應用程式中。 它包括安全性、負載平衡、自動縮放和自動化管理。 您還可以利用 Web 應用中的 DevOps 功能,例如包管理、暫存環境、自定義域、SSL/TLS 證書以及 Azure DevOps、GitHub、Docker Hub 和其他源的持續部署。

Azure 提供了可用於託管網站和 Web 應用程式的其他服務。 大部分的情況下，Web Apps 是最佳選擇。 對於微服務體系結構,請考慮[Azure 服務結構](https://azure.microsoft.com/documentation/services/service-fabric)。
如果您需要能更加充分地掌控程式碼執行所在的 VM，則請考慮使用 [Azure 虛擬機器](https://azure.microsoft.com/documentation/services/virtual-machines/)。
有關如何在這些 Azure 服務之間進行選擇的詳細資訊,請參閱 Azure[應用服務、虛擬機器、服務結構和雲端服務的比較](/azure/architecture/guide/technology-choices/compute-decision-tree)。

### <a name="apply-updates-to-components"></a>元件應用更新

為了防止漏洞,應持續清點用戶端和伺服器端元件(例如,框架和庫)及其更新依賴項。 新的漏洞和更新的軟體版本會不斷發佈。 確保您有一個持續的計劃來監視、會審,並將更新或配置更改應用於您使用的庫和元件。

有關工具建議,請參閱有關[使用具有已知漏洞的元件](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities)[的打開 Web 應用程式安全專案 (OWASP)](https://www.owasp.org/index.php/Main_Page)頁面。 您還可以訂閱與您使用的元件相關的安全漏洞的電子郵件警報。

### <a name="use-threat-modeling-during-application-design"></a>在應用程式設計期間使用威脅模型化

威脅建模是識別業務和應用程式的潛在安全威脅,然後確保適當的緩解措施到位的過程。 SDL規定,團隊應在設計階段進行威脅建模,當解決潛在問題相對容易且經濟高效時。 在設計階段使用威脅建模可以大大降低開發總成本。

為了幫助促進威脅建模過程,我們設計了[SDL 威脅建模工具](threat-modeling-tool.md),並考慮到了非安全專家。 此工具通過提供有關如何創建和分析威脅模型的明確指導,使所有開發人員都更容易進行威脅建模。

跨所有信任邊界對應用程式設計和枚舉[STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)威脅(欺騙、篡改、拒絕、資訊披露、拒絕服務和特權提升)進行建模已證明是及早捕獲設計錯誤的有效方法。 下表列出了 STRIDE 威脅,並提供了一些使用 Azure 提供的功能的示例緩解措施。 這些風險降低措施並非能在各個情況下運作。

| Threat | 安全性屬性 | 潛在的 Azure 平台緩解 |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 詐騙               | 驗證        | [需要 HTTPS 連線](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio)。 |
| 竄改              | 完整性             | 驗證 SSL/TLS 證書。 使用 SSL/TLS 的應用程式必須完全驗證其連接到的實體的 X.509 憑證。 使用 Azure 金鑰保存的憑證[來管理 x509 憑證](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates)。 |
| 否認性            | 不可否認性       | 啟用 Azure [監視和診斷](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)。|
| 資訊洩漏 | 保密       | 在靜止和[傳輸](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)[中](../fundamentals/encryption-atrest.md)加密敏感數據。 |
| 阻斷服務      | 可用性          | 監視性能指標,以尋找潛在的拒絕服務條件。 實作 IP 連線篩選。 [Azure DDoS 保護](../../virtual-network/ddos-protection-overview.md#next-steps)與應用程式設計最佳實務相結合,可防禦 DDoS 攻擊。|
| 提高權限 | 授權         | 使用 Azure<span class="underline"> </span>的目錄[權限管理](../../active-directory/privileged-identity-management/pim-configure.md)。|

### <a name="reduce-your-attack-surface"></a>減少攻擊面

攻擊面是可能發生潛在漏洞的總和。 在本文中,我們重點介紹了應用程序的攻擊面。
重點是保護應用程式免受攻擊。 最小化攻擊面的一種簡單和快速的方法是從應用程式中刪除未使用的資源和代碼。 應用程式越小,攻擊面越小。 例如, 刪除:

- 尚未發佈的功能的代碼。
- 調試支持代碼。
- 未使用的網路介面和協定或已棄用的網路介面和協定。
- 虛擬機和未使用的其他資源。

定期清理資源並確保刪除未使用的代碼是確保惡意參與者攻擊的機會減少的好方法。

減少攻擊面的更詳細和深入的方法是完成攻擊面分析。 攻擊面分析可説明您映射系統需要檢查和測試安全漏洞的部分。

攻擊表面分析的目的是瞭解應用程式中的風險區域,以便開發人員和安全專家瞭解應用程式的哪些部分可以攻擊。 然後,您可以找到最小化此潛力的方法,跟蹤攻擊面的更改時間以及方式,以及從風險角度意味著什麼。

攻擊面分析可説明您識別:

- 需要查看和測試安全漏洞的函數和部分。
- 需要縱深防禦的高風險代碼區域(需要防禦的系統部分)。
- 當您更改攻擊面並且需要刷新威脅評估時。

減少攻擊者利用潛在弱點或漏洞的機會需要您徹底分析應用程式的整體攻擊面。 它還包括禁用或限制對系統服務的訪問、應用最小特權原則以及盡可能使用分層防禦。

我們討論在 SDL 的驗證階段[進行攻擊表面審查](secure-develop.md#conduct-attack-surface-review)。

> [!NOTE]
> **威脅建模和攻擊面分析之間的區別是什麼?**
威脅建模是識別應用程式的潛在安全威脅並確保針對威脅的適當緩解措施的過程。 攻擊面分析可識別可攻擊的高風險代碼區域。 它涉及找到保護應用程式高風險區域的方法,並在部署應用程式之前查看和測試這些代碼區域。

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>採用以身分識別作為主要安全性周邊的原則

在設計雲應用程式時,將安全周邊焦點從以網路為中心的方法擴展到以身份為中心的方法非常重要。 從歷史上看,主要的本地安全邊界是組織的網路。 大多數本地安全設計都使用網路作為主要安全支點。 對於雲端應用程式,將標識視為主要安全邊界,可以更好地為您提供服務。

開發以身份為中心的方法來開發 Web 應用程式可執行的事項:

- 為用戶強制實施多重身份驗證。
- 使用增強式驗證與授權平台。
- 應用最低特權原則。
- 實現即時訪問。

#### <a name="enforce-multi-factor-authentication-for-users"></a>為使用者強制實施多重身份驗證

使用雙因素驗證。 雙重身份驗證是當前身份驗證和授權的標準,因為它避免了使用者名和密碼類型的身份驗證固有的安全漏洞。 對 Azure 管理介面(Azure 入口/遠端 PowerShell)和針對客戶的服務的存取應設計和設定為使用[Azure 多重身份驗證](../../active-directory/authentication/concept-mfa-howitworks.md)。

#### <a name="use-strong-authentication-and-authorization-platforms"></a>使用強大的身份驗證與授權平臺

使用平台提供的驗證與授權機制，而不要使用自訂程式碼。 這是因為開發自定義身份驗證代碼容易出錯。 商業代碼(例如,來自 Microsoft)經常經過廣泛審查,以進行安全性檢查。 [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 是用於管理身分識別和存取的 Azure 解決方案。 這些 Azure AD 工具和服務有助於安全開發:

- [Azure AD 識別平臺(面向開發人員的 Azure AD)](../../active-directory/develop/about-microsoft-identity-platform.md)是一種雲端識別服務,開發人員用於建構安全登錄使用者的應用。 Azure AD 可幫助建構單租戶、業務線 (LOB) 應用的開發人員以及希望開發多租戶應用的開發人員。 除了基本登錄之外,使用 Azure AD 構建的應用還可以調用在 Azure AD 平臺上建構的 Microsoft API 和自定義 API。 Azure AD 識別平台支援產業標準協定,如OAuth 2.0和OpenID連接。

- [Azure 活動目錄 B2C(Azure AD B2C)](../../active-directory-b2c/index.yml)是一種識別管理服務,可用於自訂和控制客戶在使用應用程式時註冊、登錄和管理其配置檔的方式。 這包括為 iOS、Android 和 .NET 等開發的應用程式。 Azure AD B2C 支援這些操作,同時保護客戶標識。

#### <a name="apply-the-principle-of-least-privilege"></a>應用最低特權原則

[最低特權](https://en.wikipedia.org/wiki/Principle_of_least_privilege)的概念意味著為使用者提供精確的訪問和控制水準,他們需要做他們的工作,僅此而已。

軟體開發人員是否需要域管理員許可權? 行政助理是否需要訪問其個人計算機上的管理控制? 評估對軟體的訪問也沒有什麼不同。 如果使用[基於角色的訪問控制 (RBAC)](../../role-based-access-control/overview.md)為使用者提供不同的應用程式中的能力和許可權,則不會授予每個人對一切的訪問許可權。 通過限制對每個角色所需內容的訪問,可以限制發生安全問題的風險。

確保應用程式在存取模式中強制進行[最少的權限](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications)。

> [!NOTE]
>  最低特權規則需要適用於軟體和創建軟體的使用者。 如果軟體開發人員獲得過多訪問許可權,則可能會給 IT 安全帶來巨大的風險。 如果開發人員有惡意或獲得過多訪問許可權,後果可能非常嚴重。 我們建議在整個開發生命週期中將最低特權規則應用於開發人員。

#### <a name="implement-just-in-time-access"></a>實即時存取

實現*即時*(JIT) 訪問以進一步降低特權的曝光時間。 使用[Azure AD 特權識別管理](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity):

- 向使用者授予他們只需要 JIT 的許可權。
- 指派縮短持續時間的角色，而且有信心會自動撤銷權限。

### <a name="require-re-authentication-for-important-transactions"></a>要求對重要事務重新認證

[跨網站請求偽造](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1)(也稱為*XSRF*或*CSRF)* 是針對 Web 託管應用的攻擊,其中惡意 Web 應用影響客戶端瀏覽器與信任該流覽器的 Web 應用之間的交互。 跨網站請求偽造攻擊是可能的,因為 Web 瀏覽器會在每次請求發送到網站時自動發送某些類型的身份驗證令牌。
這種利用形式也稱為*一鍵式攻擊*或*會話使用*,因為攻擊利用了使用者以前經過身份驗證的會話。

抵禦此類攻擊的最佳方法是向使用者詢問只有在每次重要交易之前才能提供的東西,例如購買、帳戶停用或密碼更改。 您可以要求使用者重新輸入其密碼、完成密碼或提交只有使用者才會擁有的秘密權杖。 最常見的方法是機密權杖。

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>使用金鑰管理解決方案保護金鑰、認證和其他機密

遺失金鑰和認證是相當常見的問題。 唯一比遺失金鑰和認證更糟的情況，就是讓未經授權者能夠存取這些機密資料。 攻擊者可以利用自動化和手動技術來查找存儲在 GitHub 等代碼存儲庫中的密鑰和機密。 不要在這些公共代碼存儲庫或任何其他伺服器上放置密鑰和機密。

始終將密鑰、證書、機密和連接字串放在密鑰管理解決方案中。 您可以使用集中式解決方案,其中密鑰和機密存儲在硬體安全模組(HSM) 中。 Azure 在雲中提供具有[Azure 密鑰保管庫](../../key-vault/general/overview.md)的 HSM。

密鑰保管庫是一個秘密*存儲*:它是用於存儲應用程式機密的集中雲服務。 密鑰保管庫通過將應用程式機密保存在單個中心位置並提供安全訪問、許可權控制和訪問日誌記錄來保護您的機密數據安全。

機密儲存在單獨的*保存庫中*。 每個保管庫都有自己的配置和安全策略來控制訪問。 您可以通過 REST API 或透過適用於大多數程式設計語言的用戶端 SDK 訪問數據。

> [!IMPORTANT]
> Azure 密鑰保管庫旨在儲存伺服器應用程式的配置機密。 它不用於存儲屬於應用用戶的數據。 這會反映在其效能特性、API 和成本模型中。
>
> 使用者數據應儲存在其他位置,例如,在具有透明資料加密 (TDE) 的 Azure SQL 資料庫實例中或使用 Azure 儲存服務加密的儲存帳戶中。 應用程式用於存取這些資料儲存的機密可以保存在 Azure 密鑰保管庫中。

### <a name="protect-sensitive-data"></a>保護敏感資料

保護數據是安全策略的重要組成部分。
對數據進行分類並確定數據保護需求有助於在設計應用時牢記數據安全性。 按敏感性和業務影響對存儲的數據進行分類(分類)可幫助開發人員確定與數據相關的風險。

在設計數據格式時,將所有適用的數據標記為敏感。 確保應用程式將適用的數據視為敏感數據。 這些實務可以幫助您保護您的敏感資料:

- 使用加密。
- 避免對密鑰和密碼等機密進行硬編碼。
- 確保訪問控制和審核到位。

#### <a name="use-encryption"></a>使用加密

保護數據應該是安全策略的重要組成部分。
如果數據存儲在資料庫中,或者數據在位置之間來回移動,請使用[靜態數據](../fundamentals/encryption-atrest.md)加密(在資料庫中)和[傳輸中的數據](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)加密(在往返用戶、資料庫、API 或服務終結點的途中)。 我們建議您始終使用 SSL/TLS 協定來交換數據。 確保使用最新版本的 TLS 進行加密(目前,這是版本 1.2)。

#### <a name="avoid-hard-coding"></a>避免硬編碼

有些東西不應該在你的軟體中硬編碼。 一些範例包括主機名或IP位址、URL、電子郵件地址、使用者名、密碼、儲存帳戶金鑰和其他加密金鑰。 請考慮圍繞代碼中可以硬編碼或不能硬編碼的內容(包括在代碼的註釋部分)實現要求。

在代碼中放置註釋時,請確保不保存任何敏感資訊。 這包括您的電子郵件地址、密碼、連接字串、僅組織中的人知道的應用程式資訊,以及可能讓攻擊者在攻擊應用程式或組織時具有優勢的任何其他資訊。

基本上,假設開發專案中的所有內容在部署時都是公共知識。 避免在專案中包含任何類型的敏感數據。

早些時候,我們討論了[Azure 金鑰保管庫](../../key-vault/general/overview.md)。 您可以使用金鑰保管庫儲存金鑰和密碼等機密,而不是對其進行硬編碼。 將密鑰保管庫與 Azure 資源的託管標識結合使用時,Azure Web 應用可以輕鬆安全地訪問機密配置值,而無需在原始程式碼管理或配置中儲存任何機密。 要瞭解更多資訊,請參閱[使用 Azure 金鑰保存庫管理伺服器套用的機密](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/)。

### <a name="implement-fail-safe-measures"></a>實施故障安全措施

應用程式必須以一致的方式處理執行過程中發生的[錯誤](https://docs.microsoft.com/dotnet/standard/exceptions/)。 應用程式應捕獲所有錯誤,並不安全或關閉失敗。

您還應確保使用足夠的使用者上下文記錄錯誤,以識別可疑或惡意活動。 日誌應保留足夠的時間,以便延遲進行取證分析。 日誌的格式應易於由日誌管理解決方案使用。 確保觸發與安全相關的錯誤的警報。 日誌記錄和監視不足使攻擊者可以進一步攻擊系統並保持持久性。

### <a name="take-advantage-of-error-and-exception-handling"></a>利用錯誤與不正常處理

實現正確的錯誤和[異常處理](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions)是防禦性編碼的重要組成部分。 錯誤和異常處理對於使系統可靠和安全至關重要。 錯誤處理中的錯誤可能導致不同類型的安全漏洞,例如向攻擊者洩露資訊並幫助攻擊者瞭解有關您的平臺和設計的更多資訊。

請確定：

- 以集中方式處理異常,以避免程式碼中重複[的嘗試/捕獲區塊](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions)。

- 所有意外行為都在應用程式內處理。

- 向用戶顯示的消息不會洩露關鍵數據,但確實提供了足夠的資訊來解釋問題。

- 記錄異常,並提供足夠的資訊供取證或事件回應團隊進行調查。

[Azure 邏輯應用](../../logic-apps/logic-apps-overview.md)為處理由從屬系統引起的[錯誤和異常](../../logic-apps/logic-apps-exception-handling.md)提供了一流的體驗。 您可以使用邏輯應用創建工作流,以自動執行跨企業和組織集成應用、數據、系統和服務的任務和流程。

### <a name="use-logging-and-alerting"></a>使用紀錄記錄與警示

[記錄](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)您的安全問題以進行安全調查,並觸發有關問題的警報,以確保人們及時了解問題。 在所有元件上啟用稽核和記錄。 審核日誌應捕獲使用者上下文並標識所有重要事件。

檢查使用者是否未記錄使用者提交到您的網站的任何敏感數據。 敏感性資料的範例包括︰

- 使用者認證
- 社會安全號碼或其他識別資訊
- 信用卡號碼或其他財務資訊
- 健康狀態資訊
- 可用於解密加密資訊的私密金鑰或其他資料
- 可用來更有效地攻擊應用程式的系統或應用程式資訊

確保應用程式監視使用者管理事件,如成功和失敗的使用者登錄、密碼重置、密碼更改、帳戶鎖定和用戶註冊。 記錄這些事件可幫助您檢測和回應潛在的可疑行為。 它還允許您收集操作數據,例如訪問應用程式的人員。

## <a name="next-steps"></a>後續步驟
在以下文章中,我們建議安全控制和活動,以説明您開發和部署安全應用程式。

- [開發安全的應用程式](secure-develop.md)
- [部署安全應用程式](secure-deploy.md)
