---
title: 在 Microsoft Azure 上開發安全應用程式
description: 本文討論了在 Web 應用程式專案的實現和驗證階段需要考慮的最佳做法。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 9d98660230e0ab9f4edcd9a7af8a3797106dd17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255651"
---
# <a name="develop-secure-applications-on-azure"></a>在 Azure 上開發安全的應用程式
在本文中，我們將介紹開發雲應用程式時需要考慮的安全活動和控制措施。 介紹了在 Microsoft[安全開發生命週期 （SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)實施和驗證階段需要考慮的安全問題和概念。 目標是説明您定義可用於開發更安全應用程式的活動和 Azure 服務。

本文將介紹以下 SDL 階段：

- 實作
- 驗證

## <a name="implementation"></a>實作
實施階段的重點是建立早期預防的最佳做法，並從代碼中發現和刪除安全問題。
假設您的應用程式將以您不希望使用的方式使用。 這有助於您防止意外或故意誤用您的應用程式。

### <a name="perform-code-reviews"></a>執行代碼評審

在簽入代碼之前，請執行[代碼評審](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs)以提高總體代碼品質並降低創建 Bug 的風險。 您可以使用[Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts)來管理代碼評審過程。

### <a name="perform-static-code-analysis"></a>執行靜態程式碼分析

[靜態代碼分析](https://www.owasp.org/index.php/Static_Code_Analysis)（也稱為*原始程式碼分析*）通常作為代碼評審的一部分執行。 靜態代碼分析通常是指使用[污點檢查和](https://en.wikipedia.org/wiki/Taint_checking)[資料流程分析](https://en.wikipedia.org/wiki/Data-flow_analysis)等技術運行靜態代碼分析工具，以查找非運行代碼中的潛在漏洞。

Azure 應用商店提供[開發人員工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review)，用於執行靜態代碼分析並協助代碼評審。

### <a name="validate-and-sanitize-every-input-for-your-application"></a>驗證和清理應用程式的每個輸入

將所有輸入視為不受信任的輸入，以保護應用程式免受最常見的 Web 應用程式漏洞的影響。 不受信任的資料是噴射攻擊的工具。 應用程式的輸入包括 URL 中的參數、來自使用者的輸入、來自資料庫或 API 的資料以及使用者可能操作的任何傳入內容。 應用程式應在應用程式以任何方式使用資料之前[驗證](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs)資料在語法和語義上是否有效（包括將其顯示回使用者）。

在資料流程的早期驗證輸入，以確保只有正確格式的資料才進入工作流。 您不希望格式不正確的資料保留在資料庫中或觸發下游元件中的故障。

黑名單和白名單是執行輸入語法驗證的兩種常規方法：

  - 黑名單嘗試檢查給定的使用者輸入是否不包含"已知惡意"內容。

  - 白名單嘗試檢查給定使用者輸入是否與一組"已知良好"輸入匹配。 基於字元的白名單是一種白名單形式，其中應用程式檢查使用者輸入是否僅包含"已知良好"字元或輸入與已知格式匹配。
    例如，這可能涉及檢查使用者名是否僅包含字母數位字元，或者是否僅包含兩個數字。

白名單是構建安全軟體的首選方法。
黑名單容易出錯，因為不可能想到可能錯誤輸入的完整清單。

在伺服器上執行此操作，而不是在用戶端（或在伺服器上和用戶端上）執行此工作。

### <a name="verify-your-applications-outputs"></a>驗證應用程式的輸出

在視覺上或文檔中呈現的任何輸出應始終進行編碼和轉義。 [轉義](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29)（也稱為*輸出編碼*）用於説明確保不受信任的資料不是噴射攻擊的工具。 轉義與資料驗證相結合，提供分層防禦，以提高整個系統的安全性。

轉義可確保所有內容都顯示為*輸出。* 轉義還可以讓解譯器知道資料不是要執行的，這可以防止攻擊起作用。 這是另一種稱為*跨網站腳本*（XSS） 的常見攻擊技術。

如果您使用的是來自協力廠商的 Web 框架，則可以使用[OWASP XSS 預防備忘單](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)驗證您在網站上的輸出編碼選項。

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>聯繫資料庫時使用參數化查詢

切勿在代碼中"動態"創建內聯資料庫查詢，並直接將其發送到資料庫。 插入到應用程式中的惡意程式碼可能會導致資料庫被盜、擦除或修改。 您的應用程式還可用於在承載資料庫的作業系統上運行惡意作業系統命令。

而是使用參數化查詢或預存程序。 使用參數化查詢時，可以從代碼安全地調用該過程並將其傳遞給字串，而不必擔心它將被視為查詢語句的一部分。

### <a name="remove-standard-server-headers"></a>刪除標準伺服器標頭

伺服器、X-供電-By 和 X-AspNet 版本等標頭顯示有關伺服器和基礎技術的資訊。 我們建議您禁止這些標頭，以避免對應用程式進行指紋識別。
請參閱[刪除 Azure 網站上的標準伺服器標頭](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)。

### <a name="segregate-your-production-data"></a>隔離生產資料

您的生產資料或"真實"資料不應用於開發、測試或業務意圖以外的任何其他目的。 應使用遮罩（[匿名）](https://en.wikipedia.org/wiki/Data_anonymization)資料集進行所有開發和測試。

這意味著訪問您的真實資料的人更少，這減少了您的攻擊面。 這也意味著更少的員工看到個人資料，從而消除了潛在的保密違規。

### <a name="implement-a-strong-password-policy"></a>實施強密碼原則

要防禦暴力猜測和基於字典的猜測，必須實施強密碼原則，以確保使用者創建複雜的密碼（例如，最小長度為 12 個字元，並且需要字母數位和特殊字元）。

您可以使用標識框架創建和強制執行密碼原則。 Azure AD B2C 通過提供[內置策略](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow)、[自助服務密碼重設](../../active-directory-b2c/user-flow-self-service-password-reset.md)等，説明您進行密碼管理。

要防禦對預設帳戶的攻擊，請驗證所有金鑰和密碼是否可替換，以及它們在安裝資源後生成或替換。

如果應用程式必須自動生成密碼，請確保生成的密碼是隨機的，並且它們具有高熵。

### <a name="validate-file-uploads"></a>驗證檔上載

如果你的應用程式允許[檔上傳](https://www.owasp.org/index.php/Unrestricted_File_Upload)，請考慮您可以採取的預防措施，為這種危險的活動。 在許多攻擊中，第一步是將一些惡意程式碼放入受攻擊的系統。 使用檔上載可説明攻擊者完成此目的。 OWASP 提供了用於驗證檔的解決方案，以確保要上載的檔是安全的。

反惡意軟體保護有助於識別和刪除病毒、間諜軟體和其他惡意軟體。 您可以安裝[微軟反惡意軟體](../fundamentals/antimalware.md)或微軟合作夥伴的端點保護解決方案（[趨勢微](https://www.trendmicro.com/azure/)[，Broadcom，](https://www.broadcom.com/products)[邁克菲](https://www.mcafee.com/us/products.aspx)[，Windows防禦器](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)，和[端點保護](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)）。

[Microsoft 反惡意軟體](../fundamentals/antimalware.md)包括即時保護、計畫掃描、惡意軟體修復、簽名更新、引擎更新、示例報告和排除事件收集等功能。 您可以將 Microsoft Antimalware 和合作夥伴解決方案與 [Azure 資訊安全中心](../../security-center/security-center-partner-integration.md)整合，以方便部署和執行內建偵測 (警示與事件)。

### <a name="dont-cache-sensitive-content"></a>不緩存敏感內容

不要在瀏覽器上緩存敏感內容。 瀏覽器可以存儲緩存和歷史記錄的資訊。 緩存的檔存儲在一個資料夾中，如臨時 Internet 檔資料夾，在 Internet 資源管理器的情況下。 再次引用這些頁面時，瀏覽器將顯示其緩存中的頁面。 如果向使用者顯示敏感資訊（位址、信用卡詳細資訊、社會保險號、使用者名），則資訊可能存儲在瀏覽器的緩存中，並通過檢查瀏覽器的緩存或只需按瀏覽器的 **"後退**"按鈕即可檢索。

## <a name="verification"></a>驗證
驗證階段涉及全面努力確保代碼符合前幾個階段確立的安全和隱私原則。

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>查找和修復應用程式依賴項中的漏洞

掃描應用程式及其相關庫以標識任何已知的易受攻擊的元件。 可用於執行此掃描的產品包括[OWASP 依賴項檢查](https://www.owasp.org/index.php/OWASP_Dependency_Check)[、Snyk](https://snyk.io/)和[黑鴨](https://www.blackducksoftware.com/)。

由[Tinfoil 安全支援](https://www.tinfoilsecurity.com/)的漏洞掃描可用於 Azure 應用服務 Web 應用。 [Tinfoil 安全掃描通過 App 服務](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)為開發人員和管理員提供了一種快速、集成且經濟的方法，在惡意參與者利用漏洞之前發現和解決漏洞。

> [!NOTE]
> 您還可以[將錫箔安全性與 Azure AD 集成](../../active-directory/saas-apps/tinfoil-security-tutorial.md)。 將 Tinfoil 安全性與 Azure AD 集成，為您提供了以下優勢：
>  - 在 Azure AD 中，您可以控制誰有權訪問錫箔安全。
>  - 使用者可以通過使用其 Azure AD 帳戶自動登入到 Tinfoil 安全（單一登入）。
>  - 您可以在單個中心位置 Azure 門戶中管理帳戶。

### <a name="test-your-application-in-an-operating-state"></a>以操作狀態測試應用程式

動態應用程式安全測試 （DAST） 是一個在操作狀態下測試應用程式以查找安全性漏洞的過程。 DAST 工具在程式執行時分析程式，以查找安全性漏洞，如記憶體損壞、不安全伺服器配置、跨網站腳本編寫、使用者許可權問題、SQL 注入和其他關鍵安全問題。

DAST 不同于靜態應用程式安全測試 （SAST）。 SAST 工具在代碼未執行時分析代碼的原始程式碼或編譯版本，以便查找安全性漏洞。

執行 DAST，最好在安全專業人員（[滲透測試儀](../fundamentals/pen-testing.md)或漏洞評估員）的協助下執行 DAST。 如果安全專業人員不可用，您可以使用 Web 代理掃描器和一些培訓自行執行 DAST。 儘早插入 DAST 掃描器，以確保不會在代碼中引入明顯的安全問題。 有關 Web 應用程式漏洞掃描程式的清單，請參閱[OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools)網站。

### <a name="perform-fuzz-testing"></a>執行模糊測試

在[模糊測試中](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)，通過故意將格式錯誤或隨機的資料引入應用程式來誘導程式失敗。 誘導程式故障有助於在應用程式發佈之前揭示潛在的安全問題。

[安全風險檢測](https://docs.microsoft.com/security-risk-detection/)是 Microsoft 獨特的模糊測試服務，用於查找軟體中的安全嚴重錯誤。

### <a name="conduct-attack-surface-review"></a>執行攻擊表面檢查

在代碼完成後查看攻擊面有助於確保已考慮對應用程式或系統的任何設計或實現更改。 它有助於確保已審查和緩解因更改而創建的任何新攻擊媒介，包括威脅模型。

您可以通過掃描應用程式來構建攻擊面的圖片。 微軟提供了一個攻擊面分析工具，稱為[攻擊表面分析器](https://www.microsoft.com/download/details.aspx?id=24487)。 您可以從許多商業動態測試和漏洞掃描工具或服務中進行選擇，包括[OWASP Zed攻擊代理專案](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)、[阿拉奇尼](http://arachni-scanner.com/)、[斯基普魚和](https://code.google.com/p/skipfish/)[w3af。](http://w3af.sourceforge.net/) 這些掃描工具對應用進行爬網並映射可通過 Web 訪問的應用程式的各個部分。 您還可以在 Azure 應用商店中搜索類似的[開發人員工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)。

### <a name="perform-security-penetration-testing"></a>執行安全滲透測試

確保應用程式的安全與測試任何其他功能一樣重要。 使[滲透測試](../fundamentals/pen-testing.md)成為構建和部署過程的標準部分。 安排對已部署的應用程式進行定期安全測試和漏洞掃描，並監視打開的埠、端點和攻擊。

### <a name="run-security-verification-tests"></a>運行安全驗證測試

Azure （AzSK）[的安全 DevOps 工具組](https://azsk.azurewebsites.net/index.html)包含用於 Azure 平臺多個服務的 SVT。 定期運行這些 SVT，以確保 Azure 訂閱和組成應用程式的不同資源處於安全狀態。 您還可以使用 AzSK 的連續集成/連續部署 （CI/CD） 擴展功能來自動執行這些測試，使 SVT 可作為 Visual Studio 擴展提供。

## <a name="next-steps"></a>後續步驟
在以下文章中，我們建議安全控制和活動，以説明您設計和部署安全應用程式。

- [設計安全應用程式](secure-design.md)
- [部署安全應用程式](secure-deploy.md)
