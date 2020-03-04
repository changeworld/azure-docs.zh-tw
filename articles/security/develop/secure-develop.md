---
title: 在 Microsoft Azure 上開發安全的應用程式
description: 本文討論在 web 應用程式專案的執行和驗證階段中，應考慮的最佳做法。
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
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78255651"
---
# <a name="develop-secure-applications-on-azure"></a>在 Azure 上開發安全的應用程式
在本文中，我們會提供您在開發雲端應用程式時應考慮的安全性活動和控制項。 涵蓋 Microsoft[安全性開發週期（SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的執行期間和驗證階段所要考慮的安全性問題和概念。 其目標是要協助您定義活動和 Azure 服務，您可以用來開發更安全的應用程式。

本文涵蓋下列 SDL 階段：

- 實作
- 驗證

## <a name="implementation"></a>實作
「實現」階段的重點是建立早期預防的最佳作法，以及偵測和移除程式碼中的安全性問題。
假設您的應用程式將以您不想要使用的方式來使用。 這可協助您防止意外或刻意誤用應用程式。

### <a name="perform-code-reviews"></a>執行程式碼審查

在您簽入程式碼之前，請先進行程式[代碼審核](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs)，以提高整體程式碼品質，並降低建立 bug 的風險。 您可以使用[Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts)來管理程式碼審核程式。

### <a name="perform-static-code-analysis"></a>執行靜態程式碼分析

[靜態程式碼分析](https://www.owasp.org/index.php/Static_Code_Analysis)（也稱為*原始程式碼分析*）通常是在程式碼審核過程中執行。 靜態程式碼分析通常是指執行靜態程式碼分析工具，藉由使用[污點檢查](https://en.wikipedia.org/wiki/Taint_checking)和[資料流程分析](https://en.wikipedia.org/wiki/Data-flow_analysis)等技術，尋找非執行程式碼中的潛在弱點。

Azure Marketplace 提供的[開發人員工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review)會執行靜態程式碼分析，並協助程式碼審查。

### <a name="validate-and-sanitize-every-input-for-your-application"></a>驗證和淨化應用程式的每個輸入

將所有輸入視為不受信任，以保護您的應用程式免于出現最常見的 web 應用程式弱點。 不受信任的資料是插入式攻擊的車輛。 應用程式的輸入包括 URL 中的參數、使用者的輸入、資料庫中的資料或 API，以及使用者可能操作的任何傳遞內容。 應用程式應該在應用程式以任何方式（包括向使用者顯示資料）之前，[驗證](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs)資料在語法上和語義上都有效。

在資料流程早期驗證輸入，以確保只有正確格式的資料進入工作流程。 您不想要在資料庫中保存格式不正確的資料，或在下游元件中觸發故障。

列入封鎖清單和允許清單是執行輸入語法驗證的兩個一般方法：

  - 列入封鎖清單會嘗試檢查指定的使用者輸入是否未包含「已知的惡意」內容。

  - 允許清單會嘗試檢查指定的使用者輸入是否符合一組「已知良好」的輸入。 以字元為基礎的允許清單是一種允許清單形式，其中應用程式會檢查使用者輸入是否只包含「已知良好」字元，或輸入是否符合已知的格式。
    例如，這可能牽涉到檢查使用者名稱是否只包含英數位元，或只包含兩個數字。

允許清單是建立安全軟體的慣用方法。
列入封鎖清單很容易發生錯誤，因為無法將可能不正確輸入的完整清單思考。

請在伺服器上執行此工作，而不是在用戶端（或是在伺服器和用戶端上）。

### <a name="verify-your-applications-outputs"></a>驗證應用程式的輸出

您以視覺化方式或在檔中呈現的任何輸出，都應一律進行編碼和轉義。 「[轉義](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29)」（也稱為「*輸出編碼*」）是用來協助確保不受信任的資料不是插入式攻擊的車輛。 與資料驗證結合的進行轉義，可提供多層式防禦，以提高系統整體的安全性。

[轉義] 可確保所有專案都會顯示為 [*輸出]。* 此外，也可讓解譯器知道資料並非要執行，這可防止攻擊的運作。 這是另一種常見的攻擊方法，稱為*跨網站腳本*（XSS）。

如果您使用協力廠商的 web 架構，您可以使用[OWASP XSS 防護](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)功能提要，在網站上驗證輸出編碼的選項。

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>當您連線到資料庫時，請使用參數化查詢

絕對不要在程式碼中「即時」建立內嵌資料庫查詢，並將其直接傳送到資料庫。 插入您應用程式的惡意程式碼可能會導致您的資料庫遭竊、抹除或修改。 您的應用程式也可以用來在裝載資料庫的作業系統上執行惡意的作業系統命令。

請改用參數化查詢或預存程式。 當您使用參數化查詢時，可以安全地從程式碼叫用程式，並將字串傳遞給它，而不必擔心它會被視為查詢語句的一部分。

### <a name="remove-standard-server-headers"></a>移除標準伺服器標頭

伺服器、X 驅動和 X-AspNet 版本之類的標頭會顯示有關伺服器和基礎技術的資訊。 我們建議您隱藏這些標頭，以避免應用程式的指紋。
請參閱[移除 Azure 網站上的標準伺服器標頭](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)。

### <a name="segregate-your-production-data"></a>隔離您的生產資料

您的實際執行資料或「實際」資料不應用於開發、測試或任何其他用途，而非企業所預期。 遮罩（[匿名](https://en.wikipedia.org/wiki/Data_anonymization)）資料集應用於所有開發和測試。

這表示較少的人可以存取您的實際資料，這可減少您的攻擊面。 這也表示較少的員工會看到個人資料，這可消除潛在的機密性缺口。

### <a name="implement-a-strong-password-policy"></a>執行強式密碼原則

若要防範暴力密碼破解和字典式猜測，您必須執行強式密碼原則，以確保使用者建立複雜的密碼（例如，長度最少12個字元，而且需要英數位元和特殊字元）。

您可以使用身分識別架構來建立和強制執行密碼原則。 Azure AD B2C 提供[內建原則](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow)、[自助式密碼重設](../../active-directory-b2c/user-flow-self-service-password-reset.md)等等，協助您進行密碼管理。

若要防禦預設帳戶的攻擊，請確認所有金鑰和密碼都是可取代的，而且會在您安裝資源後加以產生或取代。

如果應用程式必須自動產生密碼，請確定產生的密碼是隨機的，而且具有高熵。

### <a name="validate-file-uploads"></a>驗證檔案上傳

如果您的應用程式允許檔案上[傳](https://www.owasp.org/index.php/Unrestricted_File_Upload)，請考慮您可以為此具風險活動採取的預防措施。 在許多攻擊中，第一個步驟是將一些惡意程式碼放入遭受攻擊的系統中。 使用檔案上傳可協助攻擊者達成此目的。 OWASP 提供驗證檔案的解決方案，以確保您要上傳的檔案是安全的。

反惡意程式碼防護可協助識別及移除病毒、間諜軟體和其他惡意軟體。 您可以安裝[Microsoft Antimalware](../fundamentals/antimalware.md)或 Microsoft 合作夥伴的端點保護解決方案（[Trend 微](https://www.trendmicro.com/azure/)、 [Broadcom](https://www.broadcom.com/products)、 [McAfee](https://www.mcafee.com/us/products.aspx)、 [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)和[Endpoint Protection](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)）。

[Microsoft Antimalware](../fundamentals/antimalware.md)包括即時保護、排程掃描、惡意程式碼補救、簽章更新、引擎更新、範例報告和排除事件收集等功能。 您可以將 Microsoft Antimalware 和合作夥伴解決方案與 [Azure 資訊安全中心](../../security-center/security-center-partner-integration.md)整合，以方便部署和執行內建偵測 (警示與事件)。

### <a name="dont-cache-sensitive-content"></a>不要快取敏感性內容

不要快取瀏覽器上的敏感內容。 瀏覽器可以儲存快取和歷程記錄的資訊。 快取的檔案會儲存在資料夾中，例如 Internet Explorer 的臨時 Internet Files 資料夾。 再次參考這些頁面時，瀏覽器會顯示其快取中的頁面。 如果使用者顯示機密資訊（位址、信用卡詳細資料、社會安全號碼、使用者名稱），則資訊可能會儲存在瀏覽器的快取中，並藉由檢查瀏覽器的快取，或直接按瀏覽器的 [**上一頁**] 按鈕來取得。

## <a name="verification"></a>驗證
驗證階段牽涉到完整的工作，以確保程式碼符合先前階段中所建立的安全性和隱私權原則。

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>尋找並修正應用程式相依性中的弱點

您可以掃描您的應用程式及其相依程式庫，以識別任何已知的易受攻擊元件。 可用來執行這項掃描的產品包括[OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check)相依性檢查、[Snyk](https://snyk.io/)和[黑色](https://www.blackducksoftware.com/)。

由[Tinfoil security](https://www.tinfoilsecurity.com/)提供技術支援的弱點掃描適用于 Azure App Service Web Apps。 [Tinfoil 透過 App Service 進行的安全性掃描](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)可提供開發人員和系統管理員快速、整合且經濟實惠的方式，讓您在惡意執行者可以利用這些弱點之前，先探索並解決漏洞。

> [!NOTE]
> 您也可以將[Tinfoil security 與 Azure AD 整合](../../active-directory/saas-apps/tinfoil-security-tutorial.md)。 Tinfoil Security 與 Azure AD 整合可提供下列優點：
>  - 在 Azure AD 中，您可以控制可存取 Tinfoil Security 的人員。
>  - 您的使用者可以使用其 Azure AD 帳戶自動登入 Tinfoil Security （單一登入）。
>  - 您可以在單一集中位置、Azure 入口網站中管理您的帳戶。

### <a name="test-your-application-in-an-operating-state"></a>以操作狀態測試您的應用程式

「動態應用程式安全性測試」（DAST）是一種以作業狀態測試應用程式以找出安全性弱點的流程。 DAST 工具會在執行時分析程式，以找出安全性弱點，例如記憶體損毀、不安全的伺服器設定、跨網站腳本、使用者權限問題、SQL 插入式，以及其他重要的安全性考慮。

DAST 與靜態應用程式安全性測試（SAST）不同。 當程式碼未執行時，SAST 工具會分析原始程式碼或編譯版本的程式碼，以找出安全性缺陷。

執行 DAST，最好是安全性專家的協助（[滲透測試人員](../fundamentals/pen-testing.md)或弱點評估者）。 如果無法使用安全性專業人員，您可以使用 Web Proxy 掃描器和一些訓練來自行執行 DAST。 請提早插入 DAST 掃描器，以確保您不會在程式碼中引進明顯的安全性問題。 如需 web 應用程式弱點掃描器的清單，請參閱[OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools)網站。

### <a name="perform-fuzz-testing"></a>執行模糊測試

在[模糊測試](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)中，您會故意將格式不正確或隨機的資料引進應用程式來引發程式失敗。 引發程式失敗有助於在發行應用程式之前，顯示潛在的安全性問題。

[安全性風險偵測](https://docs.microsoft.com/security-risk-detection/)是 Microsoft 獨特的模糊測試服務，可用於尋找軟體中的安全性嚴重錯誤。

### <a name="conduct-attack-surface-review"></a>執行攻擊面審查

在程式碼完成後檢查受攻擊面，有助於確保已考慮對應用程式或系統進行任何設計或執行變更。 它有助於確保已檢查並減輕因變更而建立的任何新攻擊媒介，包括威脅模型。

您可以藉由掃描應用程式來建立受攻擊面的圖片。 Microsoft 提供一個稱為「[受攻擊面分析器](https://www.microsoft.com/download/details.aspx?id=24487)」的受攻擊面分析工具。 您可以選擇許多商業動態測試和弱點掃描工具或服務，包括[OWASP Zed 攻擊 Proxy 專案](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)、 [Arachni](http://arachni-scanner.com/)、 [Skipfish](https://code.google.com/p/skipfish/)和[w3af](http://w3af.sourceforge.net/)。 這些掃描工具會將您的應用程式編目，並對應可透過網路存取的應用程式元件。 您也可以在 Azure Marketplace 中搜尋類似的[開發人員工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)。

### <a name="perform-security-penetration-testing"></a>執行安全性滲透測試

確保您的應用程式安全，與測試任何其他功能一樣重要。 讓[滲透測試](../fundamentals/pen-testing.md)成為組建和部署程式的標準部分。 針對已部署的應用程式排程週期性安全性測試和弱點掃描，並監視開啟的埠、端點和攻擊。

### <a name="run-security-verification-tests"></a>執行安全性驗證測試

[適用于 azure 的 Secure DevOps 套件](https://azsk.azurewebsites.net/index.html)（AzSK）包含 azure 平臺多項服務的 SVTs。 您會定期執行這些 SVTs，以確保您的 Azure 訂用帳戶和組成應用程式的不同資源都處於安全狀態。 您也可以使用 AzSK 的持續整合/持續部署（CI/CD）延伸模組功能來自動化這些測試，使 SVTs 可作為 Visual Studio 延伸模組。

## <a name="next-steps"></a>後續步驟
在下列文章中，我們建議可協助您設計和部署安全應用程式的安全性控制和活動。

- [設計安全的應用程式](secure-design.md)
- [部署安全的應用程式](secure-deploy.md)
