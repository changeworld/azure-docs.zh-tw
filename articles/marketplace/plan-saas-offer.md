---
title: 如何為 Microsoft 商業 marketplace 規劃 SaaS 供應專案
description: 如何使用 Microsoft 雲端解決方案提供者中的商用 Marketplace 方案，規劃新的軟體即服務 (SaaS) 供應專案，以在 Microsoft AppSource、Azure Marketplace 或 () CSP 合作夥伴中心計畫中進行清單或銷售。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/30/2020
ms.openlocfilehash: e188c74c6eba5e4182f7b72277db8dd8e08db8d4
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913921"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>如何規劃商用 marketplace 的 SaaS 供應專案

本文說明發佈軟體即服務 (SaaS) 提供給 Microsoft 商用 marketplace 的不同選項和需求。 SaaS 供應專案可讓您透過線上訂用帳戶，而不是在個別電腦上進行本機安裝，將軟體解決方案提供給客戶並獲得授權。 本文將協助您準備供應專案，以利用合作夥伴中心發佈到商用 marketplace。

## <a name="listing-options"></a>清單選項

當您準備發佈新的 SaaS 供應專案時，您必須決定要選擇哪一個 _清單_ 選項。 當您在合作夥伴中心中建立供應專案時，您所選擇的清單選項會決定您需要提供的其他資訊。 您將會在 [  **供應專案設定** ] 頁面上定義您的清單選項，如 [如何在商業市場中建立 SaaS 供應](create-new-saas-offer.md)專案所述。

下表顯示商用 marketplace 中 SaaS 供應專案的清單選項。

| 清單選項 | 交易處理 |
| ------------ | ------------- |
| 與我連絡 | 客戶直接從清單中的資訊向您聯絡。``*`` |
| 免費試用 | 系統會透過 Azure Active Directory (Azure AD) ，將客戶重新導向至您的目標 URL。``*`` |
| 立即取得 (免費)  | 系統會透過 Azure AD 將客戶重新導向至您的目標 URL。``*`` |
| 透過 Microsoft 銷售  | 透過 Microsoft 銷售的優惠稱為 _可交易_ 優惠。 這是一項可交易的供應專案，可讓 Microsoft 代表發行者來推動軟體授權的金錢交換。 我們會使用您選擇的定價模型來為 SaaS 供應專案收費，並代表您管理客戶交易。 Azure 基礎結構使用費用會直接向您（合作夥伴）收費。 您應考慮定價模型中的基礎結構成本。 以下將在 [SaaS 計費](#saas-billing) 中更詳細地說明這一點。  |
|||

``*`` 發行者負責支援軟體授權交易的所有層面，包括但不限於訂單、履行、計量、計費、發票、付款和收集。

如需這些清單選項的詳細資訊，請參閱 [商業市場交易功能](marketplace-commercial-transaction-capabilities-and-considerations.md)。

發佈供應專案之後，您為供應專案選擇的清單選項就會顯示為供應專案清單頁面左上角的按鈕。 例如，下列螢幕擷取畫面顯示 Azure Marketplace 中的供應專案清單頁面，並顯示 [ **Contact me** ] 和 [ **試用磁片磁碟機** ] 按鈕。

![說明線上商店中的供應專案清單。](./media/listing-options.png)

## <a name="technical-requirements"></a>技術需求

技術需求會根據您為供應專案選擇的清單選項而有所不同。

[ _Contact me_ ] 清單選項沒有技術需求。 您可以選擇將客戶關係管理連線 (CRM) 系統來管理潛在客戶。 本文稍後的「 [客戶潛在客戶](#customer-leads) 」一節會說明這一點。

_現在就 (免費的)_、_免費試用_，以及 _透過 Microsoft_ 清單選項銷售，都有下列技術需求：

- 您的 SaaS 應用程式必須是多租使用者解決方案。
- 您可以 (MSA) 和 [Azure Active Directory (Azure AD) ](https://azure.microsoft.com/services/active-directory/) 來啟用 Microsoft 帳戶來驗證使用者。
- 您必須建立登陸頁面。 當使用者購買您的供應專案之後，系統會將他們導向登陸頁面。 這可協助他們完成所需的任何額外布建或設定。 如需建立登陸頁面的指引，請參閱下列文章：
  - [在商業市場中建立可交易 SaaS 供應專案的登陸頁面](azure-ad-transactable-saas-landing-page.md)
  - [在商業市場中建立免費或試用 SaaS 供應專案的登陸頁面](azure-ad-free-or-trial-landing-page.md)

這些額外的技術需求僅適用于 _透過 Microsoft_ (可交易) 清單選項的銷售：

- Azure AD 使用單一登入 (SSO) 身分識別管理，以及存取登陸頁面的購買使用者需要驗證。 如需詳細指引，請參閱 [在商業市場中 Azure AD 和可交易 SaaS](azure-ad-saas.md)供應專案。
- 您必須使用 [SaaS 履行 api](./partner-center-portal/pc-saas-fulfillment-api-v2.md) 來與 Azure Marketplace 和 Microsoft AppSource 整合。 您必須公開可與 SaaS 訂用帳戶互動的服務，以建立、更新及刪除使用者帳戶和服務方案。 必須在 24 小時內支援重大的 API 變更。 需定期發行非重大的 API 變更。 [Api](./partner-center-portal/pc-saas-fulfillment-api-v2.md)的檔中提供說明所收集欄位之使用方式的圖表和詳細說明。
- 您必須為您的供應專案建立至少一個方案。 您的方案會根據您在發佈之前選取的定價模型來計價：「一般 _費率_ 」或「 _每位使用者_」。 本文稍後會提供更多有關 [方案](#plans) 的詳細資料。
- 客戶可隨時取消您的供應專案。

### <a name="technical-information"></a>技術資訊

如果您要建立可交易供應專案，您必須收集 [ **技術** 設定] 頁面的下列資訊。 如果您選擇單獨處理交易，而不是建立可交易供應專案，請略過本節並移至 [ [試用] 磁片磁碟機](#test-drives)。

- **登陸頁面 url**： SAAS 網站 url (例如：) 在 `https://contoso.com/signup` 從商業市場取得您的供應專案之後，使用者會被導向至，並從新建立的 SaaS 訂用帳戶觸發設定流程。 此 URL 會收到一個權杖，可用來呼叫履行 Api 來取得您互動式註冊頁面的布建詳細資料。

  此 URL 會以可唯一識別特定客戶 SaaS 購買的 marketplace 購買識別權杖參數來呼叫。 您必須使用 [解析 API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)來交換對應 SaaS 訂用帳戶詳細資料的權杖。 這些詳細資料和您想要收集的任何其他資訊，都應該用來作為客戶互動網頁的一部分，以完成客戶註冊並啟用其購買。 在此頁面上，使用者應使用 Azure Active Directory (Azure AD) 註冊一次驗證。

  當客戶從 Azure 入口網站或 M365 系統管理中心啟動受控 SaaS 體驗時，也會呼叫具有 marketplace 購買識別碼權杖參數的 URL。 您應該處理這兩個流程：在新客戶購買之後第一次提供權杖，以及針對管理其 SaaS 解決方案的現有客戶再次提供該權杖時。

    您設定的登陸頁面應該啟動並執行24/7。 這是您在商業市集中購買 SaaS 供應專案的新資訊，或供應專案使用中訂用帳戶的設定要求時，您將收到通知的唯一方式。

- 連線 **webhook**：適用于 Microsoft 必須傳送給您的所有非同步事件 (例如，當 SaaS 訂用帳戶已取消) 時，我們會要求您提供連接 webhook URL。 我們會呼叫此 URL 來通知您活動。

  您提供的 webhook 應該啟動並執行24/7。 這是您將透過商業 marketplace 購買客戶 SaaS 訂閱相關更新的通知。

  > [!NOTE]
  > 在 Azure 入口網站中，我們會要求您建立單一租使用者 [Azure Active Directory (Azure AD) 應用程式](../active-directory/develop/howto-create-service-principal-portal.md) ，讓一個 Azure App 識別碼用來驗證這兩個服務之間的連線。 若要尋找 [租使用者識別碼](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)，請移至您的 Azure Active Directory 並選取 [ **屬性**]，然後尋找所列出的目錄識別碼號碼。 例如： `50c464d3-4930-494c-963c-1e951d15360e` 。

- **Azure Active Directory 租使用者識別碼**： (也稱為目錄識別碼) 。 在 Azure 入口網站中，我們會要求您 [註冊 Azure Active Directory (AD) 應用程式](../active-directory/develop/howto-create-service-principal-portal.md) ，以便將其新增至 API 的存取控制清單 (ACL) ，以確保您有權呼叫它。 若要尋找 Azure Active Directory (AD) 應用程式的租使用者識別碼，請移至 Azure Active Directory 中的 [應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 分頁。 在 [ **顯示名稱** ] 欄中，選取應用程式。 然後尋找列出的 **(租使用者) 識別碼** (例如) 的目錄 `50c464d3-4930-494c-963c-1e951d15360e` 。

- **Azure Active Directory 應用程式識別碼**：您也需要 [應用程式識別碼](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)。 若要取得其值，請移至 Azure Active Directory 中的 [應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 分頁。 在 [ **顯示名稱** ] 欄中，選取應用程式。 然後尋找列出的應用程式 (用戶端) 識別碼 (例如 `50c464d3-4930-494c-963c-1e951d15360e`) 。

  Azure AD 的應用程式識別碼會與您的合作夥伴中心帳戶中的發行者識別碼相關聯。 您必須針對該帳戶中的所有供應專案使用相同的應用程式識別碼。

  > [!NOTE]
  > 如果發行者在合作夥伴中心有兩個以上不同的帳戶，則應該使用兩個或更多個不同的 Azure AD 應用程式識別碼，其中一個帳戶。 合作夥伴中心中的每個夥伴帳戶都應該針對透過此帳戶發佈的所有 SaaS 供應專案，使用唯一的 Azure AD 應用程式識別碼。

## <a name="test-drives"></a>試用產品
您可以選擇啟用 SaaS 應用程式的試用產品。 試用產品可讓客戶在固定的時數記憶體取預先設定的環境。 您可以啟用任何發佈選項的試用產品，不過這項功能有其他需求。 若要深入瞭解試用產品，請參閱 [什麼是試用產品？](what-is-test-drive.md)。 如需設定不同類型的試用產品的相關資訊，請參閱 [試用產品技術](test-drive-technical-configuration.md)設定。

> [!TIP]
> [試用產品不同于免費試用版](plans-pricing.md#free-trials)。 您可以提供試用產品、免費試用或兩者。 它們都能為您的客戶提供解決方案一段固定時間。 但是，試用產品也包含實際操作的自我引導式導覽，說明您產品的主要功能，以及在實際執行案例中所示範的優點。

## <a name="customer-leads"></a>潛在客戶

您必須將您的供應專案連線到客戶關係管理 (CRM) 系統才能收集客戶資訊。 系統會要求客戶提供權限，以分享客戶資訊。 這些客戶詳細資料，以及供應專案名稱、識別碼和線上商店找到您的供應專案，將會傳送至您所設定的 CRM 系統。 商業 marketplace 支援各種 CRM 系統，以及使用 Azure 資料表或使用 Power Automate 設定 HTTPS 端點的選項。

在供應專案建立期間或之後，您可以隨時新增或修改 CRM 連接。 如需詳細指引，請參閱 [您的商業 marketplace 供應專案客戶潛在客戶](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

## <a name="selecting-an-online-store"></a>選取線上商店

當您發佈 SaaS 供應專案時，它將會列在 Microsoft AppSource、Azure Marketplace 或兩者中。 每個線上商店都有獨特的客戶需求。 AppSource 適用于商務解決方案，Azure Marketplace 適用于 IT 解決方案。 您的供應專案類型、交易功能和類別將會決定您供應專案的發佈位置。 類別和子類別會根據方案類型對應到每個線上商店。 

如果您的 SaaS 供應 *專案是 IT* 解決方案 (Azure Marketplace) 以及 (AppSource) 的商務解決方案，請選取適用于每個線上商店的類別和子類別。 發佈到兩個線上商店的供應專案，都應該有價值主張作為 IT 解決方案 *和* 商務解決方案。

> [!IMPORTANT]
> 具有 [計量計費](partner-center-portal/saas-metered-billing.md) 的 SaaS 供應專案可透過 Azure Marketplace 和 Azure 入口網站取得。 只有私人方案提供的 SaaS 供應專案可透過 Azure 入口網站取得。

| 計量付費 | 公用方案 | 私人方案 | 適用于： |
|---|---|---|---|
| 是             | 是         | 否           | Azure Marketplace 和 Azure 入口網站 |
| 是             | 是         | 是          | Azure Marketplace 和 Azure 入口網站 * |
| 是             | 否          | 是          | 僅 Azure 入口網站 |
| 否              | 否          | 是          | 僅 Azure 入口網站 |
|||||

&#42; 供應專案的私用方案將只能透過 Azure 入口網站取得

例如，使用計量付費帳單和私人方案的供應專案僅 (沒有公用方案) ，將由客戶在 Azure 入口網站中購買。 深入瞭解 [Microsoft 商業市集中的私人優惠](private-offers.md)。

如需有關列出線上商店所支援之選項的詳細資訊，請參閱 [線上商店的清單和定價選項](determine-your-listing-type.md#listing-and-pricing-options-by-online-store)。 如需類別和子類別的詳細資訊，請參閱 [商業市集中的類別和子](categories.md)類別。

## <a name="legal-contracts"></a>法律合約

為了簡化客戶的採購程式，並降低軟體廠商的法律複雜性，Microsoft 提供了一種標準合約，可供您在商業市集中提供供應專案使用。 當您在標準合約下提供您的軟體時，客戶只需要讀取並接受一次，您就不需要建立自訂條款及條件。

如果您選擇使用標準合約，您可以選擇將通用修訂條款以及最多10個自訂修訂新增至標準合約。 您也可以使用自己的條款及條件，而不是標準合約。 您將在 [ **屬性** ] 頁面中管理這些詳細資料。 如需詳細資訊，請參閱 [Microsoft 商業 marketplace 的標準合約](standard-contract.md)。

> [!NOTE]
> 使用商業 marketplace 的標準合約發佈供應專案之後，您就無法使用自己的自訂條款及條件。 這是「擇一」案例。 您可以在標準合約或您自己的條款及條件下提供您的解決方案。 如果您想要修改標準合約的條款，您可以透過標準合約的修正。

## <a name="offer-listing-details"></a>供應專案清單詳細資料

當您在合作夥伴中心中 [建立新的 SaaS 供應](create-new-saas-offer.md) 專案時，您會在 [ **供應專案清單** ] 頁面上輸入文字、影像、選用影片和其他詳細資料。 這是客戶在商業市集中探索您的供應專案清單時，將會看到的資訊，如下列範例所示。

:::image type="content" source="./media/example-saas-1.png" alt-text="說明這項供應專案在 Microsoft AppSource 中的顯示方式。":::

**撥出描述**

1. 標誌
2. 類別
3. 產業
4. 支援位址 (連結) 
5. 使用規定
6. 隱私權原則
7. 供應項目名稱
8. 摘要
9. 描述
10. 螢幕擷取畫面/影片
11. 文件

下列範例顯示 Azure 入口網站中的供應專案清單。

![說明 Azure 入口網站中的供應專案清單。](./media/example-managed-service-azure-portal.png)

**撥出描述**

1. 標題
1. 描述
1. 實用連結
1. 螢幕擷取畫面

> [!NOTE]
> 如果供應專案描述的開頭是「此應用程式僅適用于 [非英文語言]」片語，則供應專案清單內容不一定要使用英文。

為了讓您更輕鬆地建立您的供應專案，請事先準備一些專案。 除非另有說明，否則需要下列專案。

- **名稱**：此名稱會顯示為商業市集中您的供應專案清單標題。 名稱可能是商標， 不能包含表情符號 (除非屬於是商標和著作權符號)，且有 50 個字元的上限。
- **搜尋結果摘要**：將您供應專案的用途或功能描述為單一句子，而不含100個字元或更少的分行符號。 此摘要可用於 (s) 搜尋結果的商業市場清單。
- **描述**：此描述將會顯示在商業 marketplace 清單 (s) 總覽中。 請考慮包括價值主張、主要優點、預定的使用者群、任何類別或產業關聯、應用程式內購買商機、任何必要的洩漏，以及深入瞭解的連結。

    這個文字方塊具有 rich text 編輯器控制項，可用來讓您的描述更吸引人。 您也可以使用 HTML 標籤來格式化您的描述。 您最多可以在此方塊中輸入3000個字元的文字，包括 HTML 標籤。 如需其他秘訣，請參閱[撰寫一份出色的應用程式介紹](/windows/uwp/publish/write-a-great-app-description)。

- **消費者入門指示**：如果您選擇透過 Microsoft (可交易供應專案) 銷售您的供應專案，則需要此欄位。 這些指示可協助客戶連接到您的 SaaS 供應專案。 您最多可以新增3000個字元的文字，並連結至更詳細的線上檔。
- **搜尋關鍵字** (選擇性) ：提供最多三個搜尋關鍵字，讓客戶可以用來在線上商店尋找您的供應專案。 您不需要包含供應專案 **名稱** 和 **描述**：該文字會自動包含在搜尋中。
- **隱私權原則連結**：公司隱私權原則的 URL。 您必須提供有效的隱私權原則，並負責確保您的應用程式符合隱私權法律和規定。
- **連絡人資訊**：您必須提供組織中的下列連絡人：
  - **支援連絡人**：提供客戶開啟票證時，Microsoft 合作夥伴所使用的名稱、電話和電子郵件。 您也必須包含支援網站的 URL。
  - **工程連絡人**：提供 Microsoft 的名稱、電話和電子郵件，以在您的供應專案發生問題時直接使用。 此連絡人資訊未列在商業 marketplace 中。
  - **Csp 方案聯絡** (選用) ：如果您選擇參加 CSP 方案，請提供名稱、電話和電子郵件，讓這些合作夥伴可以與您聯繫任何問題。 您也可以將 URL 包含在行銷資料中。
- **有用的連結** (選擇性) ：您可以為供應專案的使用者提供各種資源的連結。 例如，論壇、常見問題和版本資訊。
- **支援檔**：您最多可以提供三個客戶面向的檔，例如白皮書、摺頁冊、檢查清單或 PowerPoint 簡報。
- **媒體–標誌**：提供 **大型** 標誌的 PNG 檔案。 合作夥伴中心將使用這個來建立 **小型** 和 **中型** 標誌。 您可以選擇性地使用不同的映射取代這些映射。

   - 從 216 x 216 到 350 x 350 px 的大型 (，需要) 
   - 中型 (90 x 90 px，選擇性) 
   - Small (48 x 48 px，選擇性) 

  這些標誌可用於線上商店中的不同位置：

  - 小型標誌會出現在 Azure Marketplace 搜尋結果中，以及 Microsoft AppSource 的主頁面和搜尋結果頁面上。
  - 當您在 Microsoft Azure 中建立新資源時，會出現「中」標誌。
  - 大型標誌會顯示在 Azure Marketplace 和 Microsoft AppSource 的供應專案清單頁面上。

- **媒體-螢幕擷取畫面**：您至少必須新增一到五個螢幕擷取畫面，且具有下列需求，以顯示供應專案的運作方式：
  - 1280 x 720 圖元
  - .png 檔案
  - 必須包含標題
- **媒體-** 影片 (選用) ：您最多可以新增四部具有下列需求的影片，以示範您的供應專案：
  - 名稱
  - URL：必須僅裝載于 YouTube 或 Vimeo 上。
  - 縮圖： 1280 x 720 .png 檔案

> [!Note]
> 您的供應專案必須符合一般 [商業 marketplace 認證原則](/legal/marketplace/certification-policies#100-general) 和 [軟體即服務原則](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) ，才能發佈至商用 marketplace。

## <a name="preview-audience"></a>預覽對象
預覽物件可以先存取您的供應專案，再將其發佈到線上商店，以在您發佈之前測試端對端功能。 在 [ **預覽物件** ] 頁面上，您可以定義有限的預覽物件。 如果您選擇單獨處理交易，而不是透過 Microsoft 銷售您的供應專案，則無法使用此設定。 若是如此，您可以略過本節並移至 [其他銷售商機](#additional-sales-opportunities)。

> [!NOTE]
> 預覽物件與私人方案不同。 私人方案是您只提供給您所選擇之特定物件的方案。 這可讓您與特定客戶協調自訂方案。 如需詳細資訊，請參閱下一節：方案。

您可以將邀請傳送給 Microsoft 帳戶 (MSA) 或 Azure Active Directory (Azure AD) 電子郵件地址。 手動新增10個電子郵件地址，或使用 .csv 檔案最多匯入20。 如果您的供應專案已上線，您仍然可以定義預覽物件來測試您的供應專案的任何變更或更新。

## <a name="plans"></a>規劃

可交易優惠需要至少一個方案。 方案會定義方案範圍和限制，以及相關聯的定價。 您可以為您的供應專案建立多個方案，為您的客戶提供不同的技術和定價選項。 如果您選擇單獨處理交易，而不是建立可交易供應專案，則不會顯示 [ **方案** ] 頁面。 如果是，請略過本節並移至 [其他銷售商機](#additional-sales-opportunities)。

如需方案的一般指引，包括定價模型、免費試用和私人方案，請參閱商業 marketplace 供應專案的 [方案和定價](plans-pricing.md) 。 下列各節會討論 SaaS 供應專案的特定其他資訊。

### <a name="saas-pricing-models"></a>SaaS 定價模式

SaaS 供應專案可以使用兩個定價模型的其中一個搭配每個方案： _固定費率_ 或 _每位使用者_。 相同供應專案中的所有方案都必須與相同的定價模型相關聯。 例如，供應專案不能有一項固定費率的方案，以及每位使用者的另一個方案。

一般 **費率**–以單一每月或每年的原價費率，啟用對您供應專案的存取。 有時稱為網站型定價。 使用此定價模型，您可以選擇性地定義計量方案，以使用 marketplace 計量服務 API 來向客戶收取非固定費率所涵蓋使用量的費用。 如需計量計費的詳細資訊，請參閱 [使用商用 marketplace 計量服務的 SaaS](./partner-center-portal/saas-metered-billing.md)計費。 如果您的 SaaS 服務的使用行為是高載，您也應該使用此選項。

**每位使用者** -啟用您的供應專案存取權，其價格取決於可以存取供應專案或佔用基座的使用者數目。 使用這個以使用者為基礎的模型，您可以設定方案所支援的最小和最大使用者數目。 您可以建立多個方案，以根據使用者數目來設定不同的價格點。 這些欄位為選擇性。 如果未選取此項，則會將使用者數目視為沒有限制 (的最小值為1，最多可支援您的服務可支援) 。 可以更新您的方案時編輯上述欄位。

> [!IMPORTANT]
> 發佈供應專案之後，您就無法變更計價模式。 此外，相同供應項目的所有方案都必須使用相同的定價模式。

### <a name="saas-billing"></a>SaaS 計費

針對在 (發行者) Azure 訂用帳戶中執行的 SaaS 應用程式，基礎結構使用方式會直接向您收費;客戶看不到實際的基礎結構使用量費用。 您應將 Azure 基礎結構使用費用組合成軟體授權定價，以彌補您部署來執行解決方案的基礎結構成本。

透過 Microsoft 的 SaaS 應用程式供應專案，會使用 [計量付費服務](./partner-center-portal/saas-metered-billing.md)，以固定費用、每位使用者或耗用量費用為基礎，以每月或每年計費。 商業 marketplace 是在代理程式模型上運作，因此發行者會設定價格、Microsoft 帳單客戶和 Microsoft 向發行者收取收益，同時支付機構費用。

下列範例顯示的是成本和支出的範例細目，以示範機構模型。 在此範例中，Microsoft 會向客戶收取 $100.00 的軟體授權費用，並支付 $80.00 給發行者。

| 授權成本 | 每月 $100 |
| ------------ | ------------- |
| Azure 使用量成本 (D1/1 核心) | 直接向發行者收費，不是向客戶收費 |
| 由 Microsoft 向客戶收取的費用 | 每月 $100.00 (發行者必須在授權費用中計入任何產生或轉嫁的基礎結構費用) |
| **Microsoft 收取的費用** | **每月 $100** |
| Microsoft 向您支付授權成本的 80%<br>`*` 針對合格的 SaaS 應用程式，Microsoft 會向您的授權成本支付90% 的費用| 每月 $80.00<br>``*`` 每月 $90.00 |
|||

**`*` 減少 Marketplace 服務費用**–針對您已在商業市集中發佈的特定 SaaS 供應專案，microsoft 將從20% 的 (降低其 marketplace 服務費用，如 microsoft Publisher 合約) 至10% 所述。 若您的供應專案 () 符合資格，則您的供應專案 (s) 必須由 Microsoft 指定為 Azure IP 共同銷售受到激勵。 在每個日曆月份結束之前，至少必須符合五 (5) 個工作天，才能獲得減少的 Marketplace 服務費用。 一旦符合資格，就會將降低的服務費用授與在下個月的第一天生效的所有交易，並在 Azure IP 共同銷售受到激勵狀態遺失之前繼續套用。 如需 IP 共同銷售資格的詳細資訊，請參閱 [共同銷售狀態的需求](https://aka.ms/CertificationPolicies#3000-requirements-for-co-sell-status)。 降低的 Marketplace 服務費用也適用于 Azure IP 共同銷售受到激勵 Vm、受控應用程式，以及透過商業 Marketplace 提供的任何其他合格可交易 IaaS 供應專案。

## <a name="additional-sales-opportunities"></a>其他銷售商機

您可以加入宣告 Microsoft 支援的行銷和銷售通路。 在合作夥伴中心中建立供應專案時，您會在程式結束時看到兩個索引標籤：

- **透過 Csp 轉售**：使用此選項可讓 Microsoft 雲端解決方案提供者 (CSP) 合作夥伴將您的解決方案轉售為配套供應專案的一部分。 如需此程式的詳細資訊，請參閱 [雲端解決方案提供者程式](cloud-solution-providers.md)。

- **與 Microsoft 共同銷售**：此選項可讓 microsoft 銷售團隊在評估客戶的需求時，考慮您的 IP 共同銷售合格解決方案。 如需共同銷售資格的詳細資訊，請參閱 [共同銷售狀態的需求](https://aka.ms/CertificationPolicies#3000-requirements-for-co-sell-status)。 如需如何準備您的供應專案以進行評估的詳細資訊，請參閱 [合作夥伴中心中的共同銷售選項](commercial-marketplace-co-sell.md)。

## <a name="next-steps"></a>後續步驟

- [如何在商業市場中建立 SaaS 供應專案](create-new-saas-offer.md)
- [陳列供應項目的最佳做法](gtm-offer-listing-best-practices.md)
