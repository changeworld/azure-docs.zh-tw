---
title: 規劃商用 marketplace 的 Azure 應用程式供應專案
description: 瞭解如何規劃新的 Azure 應用程式供應專案，以在 Azure Marketplace 中進行清單或銷售，或使用 Microsoft 合作夥伴中心中的商用 Marketplace 入口網站，透過雲端解決方案提供者 (CSP) 方案進行規劃。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: bcb8cc6da3d2fc631058386103575549e376a32c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452159"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>規劃商用 marketplace 的 Azure 應用程式供應專案

本文說明將 Azure 應用程式供應專案發佈至 Microsoft 商用 marketplace 的不同選項和需求。

## <a name="before-you-begin"></a>開始之前

設計、建置和測試這些 Azure 應用程式供應項目需要具備關於 Azure 平台與建置供應項目所用技術的技術知識。 您的工程小組應具備下列 Microsoft 技術的知識：

- 對於 [Azure 服務](https://azure.microsoft.com/services/)的基本了解。
- 如何[設計和架構 Azure 應用程式](https://azure.microsoft.com/solutions/architecture/)。
- 具備 [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、[Azure 儲存體](https://azure.microsoft.com/services/?filter=storage#storage)和 [Azure 網路](https://azure.microsoft.com/services/?filter=networking#networking)的運用知識。
- 具備 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) 的運用知識。
- 具備 [JSON](https://www.json.org/) 的運用知識。

### <a name="technical-documentation-and-resources"></a>技術文件和資源

當您針對商用 marketplace 規劃 Azure 應用程式供應專案時，請參閱下列資源。

- [了解 Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)
- 快速入門：
    - [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)
    - [Azure 範本最佳作法指南](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [發佈應用程式定義](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    - [部署服務目錄應用程式](../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)
- 教學課程：
    - [建立定義檔案](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
- 範例：
    - [Azure CLI](../azure-resource-manager/managed-applications/cli-samples.md)
    - [Azure PowerShell](../azure-resource-manager/managed-applications/powershell-samples.md)
    - [受控應用程式解決方案](../azure-resource-manager/managed-applications/sample-projects.md)

[為 Azure Marketplace 建置解決方案範本和受控應用程式](https://channel9.msdn.com/Events/Build/2018/BRK3603) \(英文\) 這部影片可針對 Azure 應用程式供應項目類型提供完整的介紹：

- 有哪些供應專案類型可供使用
- 需要哪些技術資產
- 如何撰寫 Azure Resource Manager 範本
- 開發和測試應用程式 UI
- 如何發佈應用程式供應專案
- 應用程式審查流程

### <a name="suggested-tools"></a>建議的工具

選擇下列其中一種指令碼環境，或兩者均使用，以協助管理 Azure 應用程式：

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure)

建議您將下列工具新增至開發環境：

- [Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md)
- 具有下列擴充功能的 [Visual Studio Code](https://code.visualstudio.com/) \(英文\)：
    - 擴充功能：[Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) \(英文\)
    - 擴充功能：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify) \(英文\)
    - 擴充功能：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

您可以在 [Azure 開發人員工具](https://azure.microsoft.com/tools/)頁面中檢閱可用的工具。 如果您使用 Visual Studio，請參閱 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="listing-options"></a>清單選項

發佈供應專案之後，您供應專案的清單選項就會顯示為供應專案清單頁面左上角的按鈕。 例如，下列螢幕擷取畫面顯示 Azure Marketplace 中具有 [ _立即取得_ ] 按鈕的供應專案清單頁面。 如果您已選擇提供試用產品，也會顯示 [ _試用_ 產品] 按鈕。

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="說明 Azure Marketplace 上的清單頁面。":::

## <a name="test-drive"></a>試用產品

您可以選擇啟用 Azure 應用程式供應專案的試用產品，讓客戶在購買之前先試用您的供應專案。 若要深入瞭解試用產品，請參閱 [什麼是試用產品？](what-is-test-drive.md)。 如需設定不同類型的試用產品的相關資訊，請參閱 [試用產品技術](test-drive-technical-configuration.md)設定。

您也可以閱讀試用產品的 [最佳作法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) ，並下載 [測試磁片磁碟機總覽](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (確定您的快顯封鎖程式) 。

> [!NOTE]
> 即使 skimmingBecause 所有 Azure 應用程式都是使用 Azure Resource Manager 範本來執行時，使用者應該注意的資訊，Azure 應用程式唯一可用的試用產品類型 [是以 Azure Resource Manager 為基礎的試用](azure-resource-manager-test-drive.md)產品。

## <a name="customer-leads"></a>潛在客戶

您必須將您的供應專案連線到客戶關係管理 (CRM) 系統才能收集客戶資訊。 系統會要求客戶提供權限，以分享客戶資訊。 這些客戶詳細資料，以及供應專案名稱、識別碼和線上商店找到您的供應專案，將會傳送至您所設定的 CRM 系統。 商業 marketplace 支援各種 CRM 系統，以及使用 Azure 資料表或使用 Power Automate 設定 HTTPS 端點的選項。

在供應專案建立期間或之後，您可以隨時新增或修改 CRM 連接。 如需詳細指引，請參閱 [您的商業 marketplace 供應專案客戶潛在客戶](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

## <a name="categories-and-subcategories"></a>類別與子類別

您最多可以選擇一或多個類別，以將您的供應專案分組到適當的商業市場搜尋區域中。 您最多可以為每個主要和次要類別選擇兩個子類別。 如需類別目錄和子類別的完整清單，請參閱 [供應專案清單最佳做法](gtm-offer-listing-best-practices.md#categories)。

## <a name="legal-contracts"></a>法律合約

為了簡化客戶的採購程式，並降低軟體廠商的法律複雜性，Microsoft 提供了一種標準合約，可供您在商業市集中提供供應專案使用。 當您在標準合約下提供您的軟體時，客戶只需要讀取並接受一次，您就不需要建立自訂條款及條件。

如果您選擇使用標準合約，您可以選擇將通用修訂條款以及最多10個自訂修訂新增至標準合約。 您也可以使用自己的條款及條件，而不是標準合約。 您將在 [ **屬性** ] 頁面中管理這些詳細資料。 如需詳細資訊，請參閱 [Microsoft 商業 marketplace 的標準合約](standard-contract.md)。

> [!NOTE]
> 使用商業 marketplace 的標準合約發佈供應專案之後，您就無法使用自己的自訂條款及條件。 這是「擇一」案例。 您可以在標準合約或您自己的條款及條件下提供您的解決方案。 如果您想要修改標準合約的條款，您可以透過標準合約的修正。

## <a name="offer-listing-details"></a>供應專案清單詳細資料

當您在合作夥伴中心中建立新的 Azure 應用程式供應專案時，您會在 [供應專案清單] 頁面上輸入文字、影像、選用影片和其他詳細資料。 這是客戶在 Azure Marketplace 中探索您的供應專案清單時，將會看到的資訊，如下列範例所示。

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="說明這項供應專案在 Azure Marketplace 中的顯示方式。":::

#### <a name="call-out-descriptions"></a>撥出描述

1. 標誌
2. 類別
3. 支援位址 (連結) 
4. 使用規定
5. 隱私權原則位址 (連結) 
6. 供應項目名稱
7. 摘要
8. 描述
9. 螢幕擷取畫面/影片

下列螢幕擷取畫面顯示 Azure 入口網站中如何顯示供應專案資訊：

[![說明這項供應專案如何出現在 Azure 入口網站中。](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>撥出描述

1. 標題
2. 描述
3. 實用連結
4. 螢幕擷取畫面

> [!NOTE]
> 如果供應專案描述的開頭是「此應用程式僅適用于 [非英文語言]」片語，則供應專案清單內容不一定要使用英文。

為了讓您更輕鬆地建立您的供應專案，請事先準備一些專案。 除非另有說明，否則需要下列專案。

- **名稱**：此名稱會顯示為商業市集中您的供應專案清單標題。 名稱可能是商標， 不能包含表情符號 (除非屬於是商標和著作權符號)，且有 50 個字元的上限。
- **搜尋結果摘要**：以100個字元或更少的純文字，將供應專案的用途或功能描述為單一句子（不含分行符號的純文字）。 此摘要可用於 (s) 搜尋結果的商業市場清單。
- **簡短描述**：提供最多256個字元的純文字。 此摘要會顯示在供應專案的詳細資料頁面上。
- **描述**：此描述會顯示在 Azure Marketplace 清單 (s) 總覽中。 請考慮包括價值主張、主要優點、預定的使用者群、任何類別或產業關聯、應用程式內購買商機、供應專案所能解決的客戶需求或難題、任何必要的洩漏，以及深入瞭解的連結。

    這個文字方塊具有 rich text 編輯器控制項，可用來讓您的描述更吸引人。 您也可以使用 HTML 標籤來格式化您的描述。 您最多可以在此方塊中輸入3000個字元的文字，其中包含 HTML 標籤與空格。 如需更多秘訣，請參閱「 [撰寫絕佳的應用程式描述](/windows/uwp/publish/write-a-great-app-description) 」和「 [商業 marketplace 供應專案描述」中支援的 HTML 標籤](supported-html-tags.md)。

- **搜尋關鍵字** (選擇性) ：提供最多三個搜尋關鍵字，讓客戶可以用來在線上商店尋找您的供應專案。 為了獲得最佳結果，也請在您的描述中使用這些關鍵字。 您不需要包含供應專案 **名稱** 和 **描述**。 該文字會自動包含在搜尋中。
- **隱私權原則連結**：公司隱私權原則的 URL。 您必須提供有效的隱私權原則，並負責確保您的應用程式符合隱私權法律和規定。
- **有用的連結** (選擇性) ：您可以為供應專案的使用者提供各種資源的連結。 例如，論壇、常見問題和版本資訊。
- **連絡人資訊**：您必須指定組織中的下列連絡人：
  - **支援連絡人**：提供客戶開啟票證時，Microsoft 合作夥伴所使用的名稱、電話和電子郵件。 您也必須包含支援網站的 URL。
  - **工程連絡人**：提供 Microsoft 的名稱、電話和電子郵件，以在您的供應專案發生問題時直接使用。 此連絡人資訊未列在商業 marketplace 中。
  - **CSP 方案聯絡** (選用) ：如果您選擇使用雲端解決方案提供者 (CSP) 方案，請提供名稱、電話和電子郵件，讓這些合作夥伴可以與您聯繫任何問題。 您也可以將 URL 包含在行銷資料中。
- **媒體–標誌**：提供 **大** 尺寸標誌的 PNG 檔案。 合作夥伴中心將使用這個來建立 **小型** 和 **中型** 標誌。 您可以選擇性地使用不同的映射取代這些映射。
  - 從 216 x 216 到 350 x 350 px 的大型 (，需要) 
  - 中型 (90 x 90 px，選擇性) 
  - Small (48 x 48 px，選擇性) 

  這些標誌可用於線上商店中的不同位置：
  - 小型標誌會出現在 Azure Marketplace 搜尋結果中。
  - 當您在 Microsoft Azure 中建立新資源時，會出現「中」標誌。
  - 大型標誌會顯示在 Azure Marketplace 的供應專案清單頁面上。

  您的標誌應遵循這些指導方針：

  - Azure 設計具有簡單的調色盤。 限制標誌上的主要和次要色彩數目。
  - 入口網站的佈景主題色彩是白色與黑色。 請勿使用這些色彩作為標誌的背景色彩。 請使用可讓標誌在入口網站突顯出來的色彩。 建議您使用簡單的主要色彩。
  - 如果您使用透明背景，請確定標誌與文字不是白色、黑色或藍色。
  - 您標誌的外觀與風格應該是平坦的，並避免標誌或背景中的漸層。 請勿在標誌上放置文字 (甚至是公司或品牌名稱)。 模糊影像將會導致您的提交遭到拒絕。
  - 確定標誌不會自動縮放。

- **媒體-螢幕擷取畫面** (選擇性) ：建議您新增顯示供應專案運作方式的螢幕擷取畫面。 您最多可以新增五個具有下列需求的螢幕擷取畫面，其中顯示供應專案的運作方式：
  - 1280 x 720 圖元
  - .png 檔案
  - 必須包含標題
- **媒體–** 影片 (選用) ：您最多可以新增五部具有下列需求的影片，以示範您的供應專案：
  - 名稱
  - URL：必須僅裝載于 YouTube 或 Vimeo 上。
  - 縮圖： 1280 x 720 .png 檔案

> [!NOTE]
> 您的供應專案必須符合一般 [商業 marketplace 憑證原則](/legal/marketplace/certification-policies#100-general.md) ，才能發佈至商用 marketplace。

## <a name="preview-audience"></a>預覽對象

預覽物件可以先存取您的供應專案，再將其發佈到線上商店，以在您發佈之前測試端對端功能。

> [!NOTE]
> 預覽物件與私人方案不同。 私人方案是您只提供給您所選擇之特定物件的方案。 這可讓您與特定客戶協調自訂方案。

您可以使用 Azure 訂用帳戶 Id 來定義預覽物件，以及各自的選擇性描述。 客戶無法看到這些欄位。

## <a name="technical-configuration"></a>技術設定

針對使用 [Marketplace 計量服務 api](partner-center-portal/marketplace-metering-service-apis.md)發出計量事件的 managed 應用程式，您必須提供您的服務在發出計量事件時將使用的身分識別。

如果您想要使用 [Batch 使用量事件](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event) \(部分機器翻譯\)，則需要進行此設定。 如果您想要提交 [使用事件](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event)，您也可以使用 [實例中繼資料服務](../active-directory/managed-identities-azure-resources/overview.md) 來取得 [ (JWT) 持有人權杖) 的 JSON web 權杖](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token) 。

- **Azure Active Directory 租使用者識別碼** (必要) ：在 Azure 入口網站內，您必須 [建立 Azure Active Directory (AD) 應用程式](../active-directory/develop/howto-create-service-principal-portal.md) ，讓我們可以驗證這兩個服務之間的連線是否在經過驗證的通訊之後。 若要尋找 Azure Active Directory (Azure AD) 應用程式的 [租使用者識別碼](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) ，請到應用程式註冊中 [Azure Active Directory 的分頁](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 。 在 [ **顯示名稱** ] 欄中，選取應用程式。 然後尋找 **屬性**，然後針對 **目錄 (租使用者) 識別碼** (例如 `50c464d3-4930-494c-963c-1e951d15360e`) 。
- **Azure Active Directory 應用程式識別碼** (必要) ：您也需要 [應用程式識別碼](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) 和驗證金鑰。 若要尋找您的應用程式識別碼，請移至您所 Azure Active Directory 的 [應用程式註冊](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 分頁。 在 [ **顯示名稱** ] 欄中選取應用程式，然後尋找 **應用程式 (用戶端) 識別碼** (例如 `50c464d3-4930-494c-963c-1e951d15360e`) 。 若要尋找驗證金鑰，請移至 [設定] 並選取 [金鑰]。 您必須提供描述和持續時間，然後才會獲得數值。

> [!NOTE]
> Azure 應用程式識別碼會與您的發行者識別碼相關聯，而且只能在這個發行者帳戶內重複使用。

## <a name="additional-sales-opportunities"></a>其他銷售商機

您可以加入宣告 Microsoft 支援的行銷和銷售通路。 在合作夥伴中心中建立供應專案時，您會在程式結束時看到兩個索引標籤：

- **透過 Csp 轉售**：使用此選項可讓 Microsoft 雲端解決方案提供者 (CSP) 合作夥伴將您的解決方案轉售為配套供應專案的一部分。 如需詳細資訊，請參閱 [雲端解決方案提供者程式](./cloud-solution-providers.md) 。
- **與 Microsoft 共同銷售**：此選項可讓 microsoft 銷售團隊在評估客戶的需求時，考慮您的 IP 共同銷售合格解決方案。 如需如何準備您的供應專案以進行評估的詳細資訊，請參閱 [合作夥伴中心中的共同銷售選項](partner-center-portal/commercial-marketplace-co-sell.md) 。 如需透過 Microsoft CSP 合作夥伴通路行銷供應專案的詳細資訊，請參閱 [雲端解決方案提供者](cloud-solution-providers.md)。

若要深入瞭解，請參閱 [使用 Azure Marketplace 拓展您的雲端業務](https://azuremarketplace.microsoft.com/sell)。

## <a name="plans"></a>規劃

Azure 應用程式供應專案至少需要一個方案。 方案會定義方案範圍和限制，以及相關聯的定價（如果適用）。 您可以為您的供應專案建立多個方案，為您的客戶提供不同的技術和定價選項。

如需方案的一般指引，包括定價模式和私人方案，請參閱 [商用 marketplace 供應專案的方案和定價](plans-pricing.md)。 下列各節將討論 Azure 應用程式計畫的其他相關資訊。

### <a name="types-of-plans"></a>方案類型

有兩種 Azure 應用程式方案： _解決方案範本_ 和 _受控應用程式_。 這兩種方案類型都支援將解決方案的部署和設定自動化，但 (VM) 以外的單一虛擬機器。 您可以自動化提供多個資源的程式，包括 Vm、網路和儲存體資源，以提供複雜的解決方案，例如 IaaS 解決方案。 這兩種方案類型都可以採用許多不同類型的 Azure 資源，包括但不限於 Vm。

- **解決方案範本** 方案是在商業 marketplace 中發佈解決方案的其中一種主要方式。 解決方案範本方案不會可交易在商業 marketplace 中，但可以用來部署透過商業市場計費的付費 VM 供應專案。 當客戶將管理解決方案，而且交易是透過另一個方案計費時，請使用方案範本方案類型。 如需建立解決方案範本的詳細資訊，請參閱 [什麼是 Azure Resource Manager？](../azure-resource-manager/management/overview.md)
- **受控應用程式** 方案可讓您輕鬆地為客戶建立並提供完全受控的現成應用程式。 它們具有與解決方案範本方案相同的功能，但有一些主要差異：
    - 資源會部署至資源群組，並由應用程式的發行者管理。 資源群組存在於客戶的訂用帳戶，但發行者租用戶中的身分識別可以存取資源群組。 
    - 您可以使用「發行者」來指定持續支援解決方案的成本，並透過商業市場支援交易。
 
    當您或您的客戶要求解決方案由夥伴管理，或您將部署以訂用帳戶為基礎的解決方案時，請使用受控應用程式方案類型。 如需受控應用程式的優點和類型的詳細資訊，請參閱 [Azure 受控應用程式總覽](../azure-resource-manager/managed-applications/overview.md)。

## <a name="next-steps"></a>後續步驟

- 若要規劃解決方案範本，請參閱 [規劃 Azure 應用程式供應專案的解決方案範本](plan-azure-app-solution-template.md)。
- 若要規劃 Azure 受控應用程式，請參閱 [為 azure 應用程式供應專案規劃 azure 受控應用程式](plan-azure-app-managed-app.md)。