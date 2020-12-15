---
title: 在 Microsoft Azure 上開發安全的應用程式
description: 本文將討論在 Web 應用程式專案的實作和驗證階段中，應考慮的最佳做法。
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
ms.openlocfilehash: 421fb7b0c91171756f55ad25c918955870054e3e
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511275"
---
# <a name="develop-secure-applications-on-azure"></a>在 Azure 上開發安全的應用程式
在本文中，我們會提供您在開發雲端應用程式時應考慮的安全性活動和控制。 其中涵蓋 Microsoft [安全性開發生命週期 (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) 執行期間和驗證階段所要考慮的安全性問題和概念。 其目標是要協助您定義活動和 Azure 服務，以便您用來開發更安全的應用程式。

本文章涵蓋下列 SDL 階段：

- 實作
- 驗證

## <a name="implementation"></a>實作
實作階段的重點是建立早期預防的最佳做法，以及偵測和移除程式碼中的安全性問題。
假設您的應用程式將以非預期的方式來使用。 這可協助防止意外或刻意地誤用應用程式。

### <a name="perform-code-reviews"></a>執行程式碼檢閱

在您簽入程式碼之前，請先進行[程式碼檢閱](/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs)來提升整體程式碼品質，並降低發生錯誤的風險。 您可以使用 [Visual Studio](/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) 來管理程式碼檢閱程序。

### <a name="perform-static-code-analysis"></a>執行靜態程式碼分析

[靜態程式碼分析](https://owasp.org/www-community/controls/Static_Code_Analysis) (也稱為「原始程式碼分析」) 通常會在程式碼檢閱過程中執行。 靜態程式碼分析通常是指執行靜態程式碼分析工具 (使用[污點檢查](https://en.wikipedia.org/wiki/Taint_checking)和[資料流程分析](https://en.wikipedia.org/wiki/Data-flow_analysis)等技術)，尋找非執行中程式碼的潛在弱點。

Azure Marketplace 提供的[開發人員工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review)，可執行靜態程式碼分析並協助程式碼檢閱。

### <a name="validate-and-sanitize-every-input-for-your-application"></a>驗證和處理應用程式的每個輸入

將所有輸入視為不信任，可為您的應用程式抵禦最常見的 Web 應用程式弱點。 不受信任的資料是插入式攻擊的媒介。 應用程式的輸入包括 URL 中的參數、使用者的輸入、資料庫中的或 API 中的資料，以及使用者可能操作的任何傳入內容。 應用程式在以任何方式 (包括向使用者顯示資料) 之前，應該先[驗證](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs)這些資料的語法和語義有效。

在資料流程中預先驗證輸入，可確保只有格式正確的資料進入工作流程。 您應避免在資料庫中保存格式不正確的資料，或在下游元件中觸發故障事件。

封鎖和 allowlisting 是執行輸入語法驗證的兩個一般方法：

  - 封鎖會嘗試檢查指定的使用者輸入是否未包含「已知為惡意」內容。

  - Allowlisting 嘗試檢查指定的使用者輸入是否符合一組「已知良好」輸入。 以字元為基礎的 allowlisting 是一種 allowlisting 形式，其中應用程式會檢查使用者輸入是否只包含「已知的良好」字元，或輸入符合已知的格式。
    例如，這可能牽涉到檢查使用者名稱是否只包含英數字元，或只包含剛好兩個數字。

Allowlisting 是建立安全軟體的慣用方法。
封鎖很容易發生錯誤，因為不可能想要有可能不正確輸入的完整清單。

請在伺服器上執行此工作，而不是在用戶端 (或是在伺服器和用戶端上)。

### <a name="verify-your-applications-outputs"></a>驗證應用程式的輸出

您以視覺化方式或在文件中呈現的任何輸出，都應一律進行編碼和逸出。 [逸出](https://owasp.org/www-community/Injection_Theory#Escaping_.28aka_Output_Encoding.29) (也稱為輸出編碼) 可用來協助確保不受信任的資料不是插入式攻擊的媒介。 與資料驗證結合的逸出功能可提供多層式防禦，以提高系統整體的安全性。

逸出功能可確保所有項目都會顯示為「輸出」。 此外，也可讓解譯器知道資料並非要執行，這可防止攻擊執行。 這是另一種常見的攻擊技巧，稱為「跨網站指令碼處理 (XSS)」。

如果您使用第三方的 Web 架構，您可以使用 [OWASP XSS 防護功能提要](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)，在網站上驗證輸出編碼的選項。

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>當您連線到資料庫時，請使用參數化查詢

絕對不要在程式碼中「即時」建立內嵌資料庫查詢，以及將其直接傳送到資料庫。 插入您應用程式的惡意程式碼可能會導致您的資料庫遭到竊取、抹除或修改。 應用程式也可能遭人用來在裝載資料庫的作業系統上執行惡意的作業系統命令。

請改用參數化查詢或預存程序。 當您使用參數化查詢時，可以安全地從程式碼叫用程序，並將字串傳遞至該處，而不必擔心其被視為查詢語句的一部分。

### <a name="remove-standard-server-headers"></a>移除標準伺服器標頭

Server、X-Powered-By、X-AspNet-Version 等標頭會揭露有關伺服器和基礎技術的資訊。 我們建議您隱藏這些標頭，以避免應用程式的特徵遭到竊取。
請參閱[移除 Azure 網站上的標準伺服器標頭](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)。

### <a name="segregate-your-production-data"></a>隔離您的生產資料

您的生產資料或「實際」資料不應用於開發、測試或任何其他非企業所預期的用途。 所有開發和測試資料都應該使用遮罩的 ([匿名](https://en.wikipedia.org/wiki/Data_anonymization)) 資料集。

這表示只有少數人可以存取您的實際資料，進而減少您的攻擊面。 這也表示只有少數員工會看到個人資料，進而消除潛在的機密洩漏風險。

### <a name="implement-a-strong-password-policy"></a>實作增強式密碼原則。

若要防止暴力密碼破解及以字典為基礎的猜測，您必須實作強式密碼原則，以確保使用者建立複雜的密碼 (例如，長度最少 12 個字元及要求使用英數字元和特殊字元)。

您可以使用身分識別架構來建立和強制實作密碼原則。 Azure AD B2C 提供的[內建原則](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow)、[自助式密碼重設](../../active-directory-b2c/user-flow-self-service-password-reset.md)等功能，可協助您進行密碼管理。

若要防禦對於預設帳戶的攻擊，請確認所有金鑰和密碼均可取代，而且會在安裝資源後產生或取代。

如果應用程式必須自動產生密碼，請確保所產生的密碼是隨機的且具有高熵。

### <a name="validate-file-uploads"></a>驗證檔案上傳

如果您的應用程式允許[上傳檔案](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)，請考慮為此風險活動採取預防措施。 在許多攻擊中，第一部舊是將一些惡意程式碼放入遭受攻擊的系統中。 使用檔案上傳可幫助攻擊者完成此動作。 OWASP 提供驗證檔案的解決方案，以確保您要上傳的檔案是安全的。

反惡意程式碼軟體可協助識別及移除病毒、間諜軟體和其他惡意軟體。 您可安裝 [Microsoft Antimalware](../fundamentals/antimalware.md) 或 Microsoft 合作夥伴的端點保護解決方案 ([Trend Micro](https://www.trendmicro.com/azure/)、[Broadcom](https://www.broadcom.com/products)、[McAfee](https://www.mcafee.com/us/products.aspx)、[Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) 及 [Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection))。

[Microsoft Antimalware](../fundamentals/antimalware.md) 包含下列功能：即時防護、排程掃描、惡意程式碼補救、簽章更新、引擎更新、範例報告和排除事件收集。 您可以將 Microsoft Antimalware 和合作夥伴解決方案與 [Azure 資訊安全中心](../../security-center/security-center-partner-integration.md)整合，以方便部署和執行內建偵測 (警示與事件)。

### <a name="dont-cache-sensitive-content"></a>請勿快取敏感性內容

請勿快取瀏覽器上的敏感性內容。 瀏覽器可以儲存資訊以進行快取和記錄。 快取的檔案會儲存在資料夾中，例如若是 Internet Explorer，則會儲存在 Temporary Internet Files 資料夾內。 當您再次參閱這些頁面時，瀏覽器就會從快取內顯示這些頁面。 如果將敏感性資訊顯示給使用者 (例如地址、信用卡詳細資料、社會安全號碼或使用者名稱)，這些資訊可能會儲存在瀏覽器的快取內，且可透過檢查瀏覽器快取或直接按瀏覽器的 [上一頁] 按鈕來加以擷取。

## <a name="verification"></a>驗證
驗證階段需要兼顧每一方面，才能確保程式碼符合先前階段中所建立的安全性和隱私權原則。

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>尋找並修正應用程式相依性中的弱點

您可以掃描您的應用程式及其相依程式庫，以識別任何已知的易受攻擊元件。 可用來執行這項掃描的產品包括 [OWASP 相依性檢查](https://www.owasp.org/index.php/OWASP_Dependency_Check)、[Snyk](https://snyk.io/)及  [Black Duck](https://www.blackducksoftware.com/)。

[Tinfoil Security](https://www.tinfoilsecurity.com/) 支援的弱點掃描適用於 Azure App Service Web Apps。 [透過 App Service 進行的 Tinfoil Security 掃描](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)可讓開發人員和系統管理員趕在惡意執行者利用弱點之前，以快速、整合且經濟實惠的方式探索和解決弱點。

> [!NOTE]
> 您也可以[整合 Tinfoil Security 與 Azure AD](../../active-directory/saas-apps/tinfoil-security-tutorial.md)。 將 Tinfoil Security 與 Azure AD 整合提供下列優點：
>  - 您可以在 Azure AD 中控制可存取 Tinfoil Security 的人員。
>  - 您的使用者可以使用其 Azure AD 帳戶自動登入 Tinfoil Security (單一登入)。
>  - 您可以在 Azure 入口網站中單一且集中地管理您的帳戶。

### <a name="test-your-application-in-an-operating-state"></a>在操作狀態中測試您的應用程式

「動態應用程式安全性測試」(DAST) 是在作業狀態中測試應用程式以找出安全性弱點的流程。 DAST 工具會在執行時分析程式，以找出安全性弱點，例如記憶體損毀、不安全的伺服器設定、跨網站指令碼處理、使用者權限問題、SQL 插入式攻擊，以及其他重要的安全性考量。

DAST 與靜態應用程式安全性測試 (SAST) 不同。 SAST 工具會在程式碼未執行時，分析原始程式碼或程式碼的編譯版本，以找出安全性缺陷。

執行 DAST 時最好有安全性專業人員的協助 ([滲透測試者](../fundamentals/pen-testing.md)或弱點評估者)。 如果無法與安全性專業人員合作，您可以使用 Web Proxy 掃描器和一些訓練來自行執行 DAST。 請提早插入 DAST 掃描器，以確保您不會在程式碼中引進明顯的安全性問題。 如需 Web 應用程式弱點掃描器的清單，請參閱 [OWASP](https://owasp.org/www-community/Vulnerability_Scanning_Tools) 網站。

### <a name="perform-fuzz-testing"></a>執行模糊測試

在[模糊測試](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)中，您可以故意將格式錯誤或隨機的資料引進應用程式來引發程式失敗。 引發程式失敗有助於在發行應用程式之前，發現潛在的安全性問題。

[安全性風險偵測](https://www.microsoft.com/en-us/security-risk-detection/)是 Microsoft 獨特的模糊測試服務，用以尋找軟體中的安全性關鍵錯誤。

### <a name="conduct-attack-surface-review"></a>執行攻擊面檢閱

在程式碼完成後檢查受攻擊面，有助於確保已考量對應用程式或系統進行的任何設計或實作變更。 也有助於確保已檢閱因變更而建立的任何新攻擊媒介 (包括威脅模型)，並降低風險。

您可以藉由掃描應用程式來建立受攻擊面的圖片。 Microsoft 提供稱為 [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487) 的受攻擊面分析工具。 您可以選擇許多商業動態測試和弱點掃描工具或服務，包括 [OWASP Zed Attack Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)、[Arachni](http://arachni-scanner.com/)、[Skipfish](https://code.google.com/p/skipfish/) 和 [w3af](http://w3af.sourceforge.net/)。 這些掃描工具會耙梳您的應用程式，並對應可透過網路存取的應用程式元件。 您也可以在 Azure Marketplace 中搜尋類似的[開發人員工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)。

### <a name="perform-security-penetration-testing"></a>執行安全性滲透測試

確保您的應用程式安全，與測試任何其他功能同等重要。 讓[滲透測試](../fundamentals/pen-testing.md)成為建置和部署程序的標準部分。 在已部署應用程式上排程定期安全性測試和弱點掃描，並監視開啟連接埠、端點和攻擊。

### <a name="run-security-verification-tests"></a>執行安全性驗證測試

[Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) 包含 Azure 平台中多項服務的 SVT。 您會定期執行這些 SVT，以確保您的 Azure 訂用帳戶和組成應用程式的不同資源都處於安全狀態。 您也可以使用 AzSK 的持續整合/持續部署 (CI/CD) 擴充功能來自動化這些測試，使 SVT 可作為 Visual Studio 擴充功能。

## <a name="next-steps"></a>後續步驟
在下列文章中，我們會建議可協助您設計和部署安全應用程式的安全性控制和活動。

- [設計安全的應用程式](secure-design.md)
- [部署安全的應用程式](secure-deploy.md)