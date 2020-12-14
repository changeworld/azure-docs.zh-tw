---
title: Azure AD B2C 自訂原則總覽 |Microsoft Docs
description: 關於 Azure Active Directory B2C 自訂原則和識別體驗架構的主題。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed477a931ed63c0db378ff84f85544072492ef96
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387032"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Azure AD B2C 自訂原則總覽

自訂原則是定義 Azure Active Directory B2C (Azure AD B2C) 租用戶行為的設定檔。 在 Azure AD B2C 入口網站中預先定義 [使用者流程](user-flow-overview.md) ，以進行最常見的身分識別工作。 身分識別開發人員可以完全編輯自訂原則，以完成許多不同的工作。

自訂原則是可完全設定的原則導向，可協調標準通訊協定格式（例如 OpenID Connect、OAuth、SAML）和少數非標準通訊協定（例如 REST API 型系統對系統宣告交換）實體之間的信任。 此架構會建立便於使用的白色標記體驗。

自訂原則以一或多個 XML 格式的檔案表示，各檔案在階層鏈中彼此參考。 XML 元素會定義建築物 blokes、與使用者互動、與其他合作物件的互動，以及商務邏輯。 

## <a name="custom-policy-starter-pack"></a>自訂原則入門套件

Azure AD B2C 自訂原則 [入門套件](custom-policy-get-started.md#get-the-starter-pack) 隨附數個預先建立的原則，可讓您快速進行。 這當中每個入門套件，針對要完成所述的案例，均包含所需最小數目技術設定檔與使用者旅程圖：

- **LocalAccounts** - 只能使用本機帳戶。
- **SocialAccounts** - 只能使用社交 (或同盟) 帳戶。
- **SocialAndLocalAccounts** - 可使用本機帳戶與社交帳戶。 我們大部分的範例都會參考此原則。
- **SocialAndLocalAccountsWithMFA** - 啟用社交、本機與多重要素驗證選項。

## <a name="understanding-the-basics"></a>瞭解基本概念 

### <a name="claims"></a>宣告

宣告會在 Azure AD B2C 原則執行期間，提供資料的暫存位置。 它可以儲存使用者的相關資訊，例如名字、姓氏，或從使用者或其他系統取得的任何其他宣告 (宣告交換) 。 [宣告結構描述](claimsschema.md)是您宣告自有宣告的位置。 

當原則執行時，Azure AD B2C 會在內部和外部方之間傳送和接收宣告，然後將這些宣告的子集傳送給您的信賴憑證者應用程式，作為權杖的一部分。 宣告的使用方式如下： 

- 宣告是針對目錄使用者物件儲存、讀取或更新。
- 從外部識別提供者接收宣告。
- 宣告是使用自訂 REST API 服務來傳送或接收。
- 在註冊或編輯設定檔流程期間，會將資料收集為使用者的宣告。

### <a name="manipulating-your-claims"></a>操作您的宣告

[宣告轉換](claimstransformations.md)是預先定義的函式，可用來將指定的宣告轉換成另一個宣告、評估宣告，或設定宣告值。 例如，將專案加入至字串集合、變更字串的大小寫，或評估資料和時間宣告。 宣告轉換會指定轉換方法。 

### <a name="customize-and-localize-your-ui"></a>自訂和當地語系化您的 UI

當您想要在網頁瀏覽器中呈現頁面時，從使用者收集資訊時，請使用 [自我判斷技術設定檔](self-asserted-technical-profile.md)。 您可以編輯自行判斷提示的技術設定檔，以 [新增宣告並自訂使用者輸入](custom-policy-configure-user-input.md)。

若要自訂自我判斷技術設定檔 [的使用者介面](customize-ui-with-html.md) ，您可以使用自訂的 HTML 內容，在 [內容定義](contentdefinitions.md) 元素中指定 URL。 在自我判斷技術設定檔中，您會指向此內容定義識別碼。

若要自訂特定語言的字串，請使用 [當地語系化](localization.md) 元素。 內容定義可能包含 [當地語系化](localization.md) 的參考，以指定要載入的當地語系化資源清單。 Azure AD B2C 會合併使用者介面元素與從您 URL 載入的 HTML 內容，然後對使用者顯示此頁面。 

## <a name="relying-party-policy-overview"></a>信賴憑證者原則總覽

信賴憑證者應用程式或 SAML 通訊協定（稱為服務提供者）會呼叫信賴憑證者 [原則](relyingparty.md) 來執行特定的使用者旅程圖。 信賴憑證者原則會指定要執行的使用者旅程圖，以及權杖包含的宣告清單。 

![顯示原則執行流程的圖表](./media/custom-policy-trust-frameworks/custom-policy-execution.png)

所有使用相同原則的信賴憑證者應用程式都會收到相同的權杖宣告，而使用者會經歷相同的使用者旅程圖。

### <a name="user-journeys"></a>使用者旅程

[使用者旅程](userjourneys.md) 可讓您使用路徑來定義商務邏輯，以供使用者用來取得應用程式的存取權。 使用者會透過使用者旅程來取得要呈現給應用程式的宣告。 使用者旅程圖是由一系列的 [協調流程步驟](userjourneys.md#orchestrationsteps)所建立。 使用者必須到達最後一個步驟，才能取得權杖。 

以下說明如何將協調流程步驟新增至 [社交和本機帳戶入門套件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) 原則。 以下是已新增的 REST API 呼叫範例。

![自訂的使用者旅程圖](media/custom-policy-trust-frameworks/user-journey-flow.png)


### <a name="orchestration-steps"></a>協調流程步驟

協調流程步驟會參考實作為其預定用途或功能的方法。 這個方法稱為 [技術設定檔](technicalprofiles.md)。 當您的使用者旅程圖需要分支來更妥善地表示商務邏輯時，協調流程步驟會參考到 [子旅程](subjourneys.md)。 子旅程包含自己的一組協調流程步驟。

使用者必須到達使用者旅程圖中的最後一個協調流程步驟，才能取得權杖。 但使用者可能不需要經歷所有的協調流程步驟。 協調流程步驟可以根據在協調流程步驟中所定義的 [前置條件](userjourneys.md#preconditions) ，有條件地執行。 

協調流程步驟完成之後，Azure AD B2C 會將輸出宣告儲存在 **宣告包** 中。 在使用者旅程圖中，任何進一步的協調流程步驟都可以利用宣告包中的宣告。

下圖顯示使用者旅程圖的協調流程步驟可以存取宣告包的方式。

![Azure AD B2C 使用者旅程圖](media/custom-policy-trust-frameworks/user-journey-diagram.png)

### <a name="technical-profile"></a>技術設定檔

技術設定檔提供與不同類型的合作物件通訊的介面。 使用者旅程圖會透過協調流程步驟來合併通話技術設定檔，以定義您的商務邏輯。

所有類型的技術設定檔都共用相同的概念。 您可以傳送輸入宣告、執行宣告轉換，以及與已設定的合作物件進行通訊。 程式完成之後，技術設定檔會將輸出宣告傳回給宣告包。 如需詳細資訊，請參閱 [技術設定檔總覽](technicalprofiles.md)

### <a name="validation-technical-profile"></a>驗證技術設定檔

當使用者與使用者介面互動時，您可能會想要驗證所收集的資料。 若要與使用者互動，必須使用 [自我判斷技術設定檔](self-asserted-technical-profile.md) 。

若要驗證使用者輸入，請從自我判斷技術設定檔呼叫 [驗證技術設定檔](validation-technical-profile.md) 。 

驗證技術設定檔是呼叫任何非互動式技術設定檔的方法。 在此情況下，技術設定檔會傳回輸出宣告或錯誤訊息。 錯誤訊息會呈現給使用者的螢幕上，讓使用者可以重試。

下圖說明 Azure AD B2C 如何使用驗證技術設定檔來驗證使用者認證。

![驗證技術設定檔圖表](media/custom-policy-trust-frameworks/validation-technical-profile.png)

## <a name="inheritance-model"></a>繼承模型

每個入門套件包含下列檔案：

- **基底** 檔案，包含大部分的定義。 為了有利於原則的疑難排解和長期維護，建議您盡可能不要變更這個檔案。
- **延伸** 模組檔案，其中保存您租使用者的唯一設定變更。 此原則檔衍生自基底檔案。 使用此檔案以新增新的功能，或覆寫現有功能。 例如，使用此檔案與新的識別提供者形成同盟。
- **信賴憑證者 (RP)** 檔案，這是以單一工作為主的檔案，直接由信賴憑證者叫用，例如 Web、行動或桌面應用程式。 每項獨特的工作（例如註冊、登入、密碼重設或設定檔編輯）都需要自己的信賴憑證者原則檔。 此原則檔衍生自延伸模組檔案。

繼承模型如下所示：

- 任何層級的子原則可以繼承自父原則，並藉由新增新元素以進行擴充。
- 在更複雜的案例中，您可以新增更多的 inhabitance 層級 (總計) 
- 您可以新增更多信賴憑證者原則。 例如，刪除我的帳戶、變更電話號碼、SAML 信賴憑證者原則等等。

下圖顯示原則檔和信賴憑證者應用程式之間的關聯性。

![顯示信任架構原則繼承模型的圖表](media/custom-policy-trust-frameworks/policies.png)


## <a name="guidance-and-best-practices"></a>指導方針和最佳做法

### <a name="best-practices"></a>最佳作法

在 Azure AD B2C 自訂原則中，您可以整合自己的商務邏輯，讓使用者體驗您需要的服務和擴充功能。 我們有一組最佳作法和建議，可讓您開始使用。

- 在 **擴充原則** 或轉送合作物件 **原則** 內建立您的邏輯。 您可以藉由參考相同的識別碼來新增新的元素，這會覆寫基底原則。 這可讓您向外延展您的專案，並在 Microsoft 發行新的入門套件之後，更輕鬆地升級基底原則。
- 在 **基本原則** 中，我們強烈建議避免進行任何變更。  必要時，請建立變更的批註。
- 當您要覆寫元素（例如技術設定檔中繼資料）時，請避免從基本原則複製整個技術設定檔。 相反地，請只複製元素的必要區段。 如需如何進行變更的範例，請參閱 [停用電子郵件驗證](custom-policy-disable-email-verification.md) 。
- 若要減少與核心功能共用的技術設定檔重複，請使用 [技術設定檔包含](technicalprofiles.md#include-technical-profile)。
- 避免在登入期間寫入 Azure AD 目錄，這可能會導致節流問題。
- 如果您的原則具有外部相依性，例如 REST API 可確保它們具有高可用性。
- 若要獲得更好的使用者體驗，請確定您的自訂 HTML 範本是使用 [線上內容傳遞](https://docs.microsoft.com/azure/cdn/)來全域部署。 Azure 內容傳遞網路 (CDN) 可讓您縮短載入時間、節省頻寬和加快回應速度。
- 如果您想要對使用者旅程作變更。 將整個使用者旅程從基本原則複製到延伸原則。 將唯一的使用者旅程圖識別碼提供給您所複製的使用者旅程圖。 然後，在 [信賴](relyingparty.md)憑證者原則中，將 [預設使用者旅程](relyingparty.md#defaultuserjourney) 圖元素變更為指向新的使用者旅程圖。

## <a name="troubleshooting"></a>疑難排解

使用 Azure AD B2C 原則進行開發時，您可能會在執行使用者旅程圖時遇到錯誤或例外狀況。 可以使用 Application Insights 來進行調查。

- 將 Application Insights 與 Azure AD B2C 整合，以 [診斷例外](troubleshoot-with-application-insights.md)狀況。
- [VS code 的 Azure AD B2C 延伸](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)模組可協助您根據原則名稱和時間來存取和[視覺化記錄](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md)。
- 設定自訂原則時，最常見的錯誤是 XML 格式不正確。 在上傳 XML 檔案之前，請先使用 [XML 架構驗證](troubleshoot-custom-policies.md) 來識別錯誤。

## <a name="continuous-integration"></a>持續整合

藉由使用持續整合和傳遞 (您在 Azure Pipelines 中設定的 CI/CD) 管線，您可以在軟體傳遞和程式碼控制自動化 [中包含 Azure AD B2C 自訂原則](deploy-custom-policies-devops.md) 。 當您部署至不同的 Azure AD B2C 環境（例如開發、測試和生產環境）時，建議您移除手動程式，並使用 Azure Pipelines 來執行自動化測試。

## <a name="prepare-your-environment"></a>準備您的環境

您可以開始使用 Azure AD B2C 自訂原則：

1. [建立 Azure AD B2C 租用戶](tutorial-create-tenant.md)
1. 使用 Azure 入口網站[註冊 web 應用程式](tutorial-register-applications.md)。 因此，您將能夠測試您的原則。
1. 新增必要的 [原則金鑰](custom-policy-get-started.md#add-signing-and-encryption-keys) ，並 [註冊 Identity Experience Framework 的應用程式](custom-policy-get-started.md#register-identity-experience-framework-applications)
1. [取得 Azure AD B2C 原則入門套件](custom-policy-get-started.md#get-the-starter-pack) ，並上傳至您的租使用者。 
1. 上傳入門套件之後，請 [測試您的註冊或登入原則](custom-policy-get-started.md#test-the-custom-policy)
1. 建議您下載並安裝 [Visual Studio Code](https://code.visualstudio.com/) (VS Code) 。 Visual Studio Code 是輕巧但功能強大的原始程式碼編輯器，可在桌面執行，適用於 Windows、macOS 和 Linux。 您可以使用 VS Code 編輯 Azure AD B2C 自訂原則 XML 檔案。
1. 若要快速流覽 Azure AD B2C 自訂原則，建議您安裝 [Azure AD B2C 擴充功能以進行 VS Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)
 
## <a name="next-steps"></a>後續步驟

設定並測試您的 Azure AD B2C 原則之後，您就可以開始自訂原則。 請流覽下列文章，以瞭解如何：

- 使用自訂原則[新增宣告並自訂使用者輸入](custom-policy-configure-user-input.md)。 瞭解如何定義宣告、自訂一些入門套件技術設定檔，以將宣告新增至使用者介面。
- 使用自訂原則來自訂應用程式的[使用者介面](customize-ui-with-html.md)。 瞭解如何建立您自己的 HTML 內容，並自訂內容定義。
- 使用自訂原則來當地語系化您應用程式的[使用者介面](custom-policy-localization.md)。 瞭解如何藉由新增當地語系化的資源元素，來設定支援的語言清單，以及提供語言特定標籤。
- 在您的原則開發和測試期間，您可以 [停用電子郵件驗證](custom-policy-disable-email-verification.md)。 瞭解如何覆寫技術設定檔中繼資料。
- 使用自訂原則來[設定以 Google 帳戶登入](identity-provider-google-custom.md)。 瞭解如何使用 OAuth2 技術設定檔來建立新的宣告提供者。 然後自訂使用者旅程圖，以包含 Google 登入選項。
- 若要診斷自訂原則的問題，您可以 [使用 Application Insights 收集 Azure Active Directory B2C 記錄](troubleshoot-with-application-insights.md)檔。 瞭解如何新增技術設定檔，以及設定您的轉送合作物件原則。