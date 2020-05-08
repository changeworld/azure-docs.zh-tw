---
title: 在 Microsoft 商業 marketplace 中建立受控服務供應專案
description: 如何使用合作夥伴中心的商業 Marketplace 入口網站，為 Azure Marketplace 中的清單建立新的受控服務供應專案。
author: JnHs
ms.author: jenhayes
manager: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 41b2e12a94f5751e31f482c913c71b4731b1cf6c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871916"
---
# <a name="create-a-managed-service-offer"></a>建立受控服務供應項目

受控服務提供啟用[Azure 燈塔](../../lighthouse/overview.md)案例的協助。 當客戶接受受控服務供應專案時，他們就可以將資源上線，以進行[Azure 委派的資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)。 開始之前，請先[在合作夥伴中心建立商業 Marketplace 帳戶](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)（如果您尚未這麼做）。 請確定您的帳戶已在商業 marketplace 方案中註冊。

您必須擁有[銀級或金級雲端平臺專長認證等級](https://partner.microsoft.com/membership/cloud-platform-competency)，或必須是[AZURE 專家的 MSP](https://partner.microsoft.com/membership/azure-expert-msp) ，才能發佈受控服務供應專案。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [**商用 Marketplace** > **總覽**]。
3. 在 [總覽] 頁面上，選取 [ **+ 新增供應** > 專案] [**受控服務**]。

    ![說明左側導覽功能表。](./media/new-offer-managed-service.png)

>[!NOTE]
>發行供應專案之後，在合作夥伴中心對其進行的編輯，只會在重新發佈供應專案後出現在店面中。 請務必在進行變更之後，一律重新發佈。

## <a name="new-offer"></a>新增供應項目

輸入**供應專案識別碼**。 這是您帳戶中每個供應專案的唯一識別碼。

* Marketplace 供應專案和 Azure Resource Manager 範本的網址中，客戶可以看到此識別碼（如果適用的話）。
* 請一律使用小寫字母和數字。 它可以包含連字號和底線，但不能有空格，而且限制為50個字元。 例如，如果您輸入 [**測試-供應專案-1**]，則供應專案 web `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`位址會是。
* 選取 [**建立**] 之後，即無法變更供應專案識別碼。

輸入**供應專案別名**。 這是在合作夥伴中心使用供應專案的名稱。

* 此名稱不會在 marketplace 中使用，而且與向客戶顯示的供應專案名稱和其他值不同。
* 選取 [**建立**] 之後，就無法變更第一個供應專案別名。

選取 [**建立**] 以產生供應專案並繼續。

## <a name="offer-setup"></a>供應專案設定

## <a name="connect-lead-management"></a>連接潛在客戶管理

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

根據[受管理的服務認證原則](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)，必須要有**潛在客戶目的地**。 這會在每次客戶部署您的供應專案時，于您的 CRM 系統中建立一筆記錄。

如需詳細資訊，請參閱[潛在客戶管理總覽](./commercial-marketplace-get-customer-leads.md)。

選取 [**儲存草稿**] 再繼續。

## <a name="properties"></a>屬性

此頁面可讓您定義用來在 marketplace 上分組供應專案的類別，以及支援供應專案的法律合約。

### <a name="category"></a>類別

選取最少1個，最多五個類別用來將您的供應專案放入適當的 marketplace 搜尋區域。 請務必在供應專案描述中，呼叫您的供應專案如何支援這些類別。

### <a name="terms-and-conditions"></a>條款及條件

在 [**條款及條件**] 欄位中提供您自己的法律條款與條件。 您也可以提供 URL，以便找到您的條款及條件。 客戶必須接受這些條款，才能試用您的供應專案。

選取 [**儲存草稿**] 再繼續。

## <a name="offer-listing"></a>供應專案清單

此頁面可讓您定義供應專案的 marketplace 詳細資料（例如供應專案名稱、描述和映射）。

> [!NOTE]
> 供應專案清單內容（例如描述、檔、螢幕擷取畫面和使用規定）不需要英文，只要供應專案的描述開頭為片語，「此應用程式僅適用于 [非英文語言]。」 您也可以提供*有用的連結 URL*來提供內容，而不是供應專案清單內容所使用的語言。

### <a name="name"></a>名稱

您在此處輸入的名稱會向客戶顯示，做為供應專案清單的標題。 當您建立供應專案時，此欄位會預先填入您在**供應專案別名**中輸入的文字，但您可以變更此值。 此名稱可能是商標（您可以包含商標或著作權符號）。 名稱不能超過50個字元，而且不能包含任何 emoji。

### <a name="search-results-summary"></a>搜尋結果摘要

提供供應專案的簡短描述（最多100個字元），這可用於 marketplace 搜尋結果。

### <a name="long-summary"></a>完整摘要

提供供應專案的較長描述（最多256個字元）。 這段完整摘要也可以用於 marketplace 搜尋結果。

### <a name="description"></a>描述

提供供應專案的較長描述（最多3000個字元）。 在 marketplace 清單總覽中，客戶會看到這項描述。 包含您供應專案的價值主張、主要優點、類別及/或產業關聯、應用程式內購買機會，以及任何必要的公開。

撰寫描述的一些秘訣：  

- 在您的描述中的前幾個句子清楚描述您供應項目的價值主張。 在您的價值主張中包含下列專案：
  - 供應專案的描述
  - 受益于供應專案的使用者類型
  - 供應專案位址的客戶需求或痛苦
- 請記住，前幾個句子可能會顯示在搜尋引擎結果中。  
- 請勿依賴特色與功能來銷售您的產品。 反之，要鎖定在您所提供的價值。  
- 請盡可能使用業界特定詞彙或凸顯優點的字眼。

若要讓您的供應專案描述更吸引人，請使用 rtf 編輯器來套用格式設定。

![使用 rich 文字編輯器](./media/rich-text-editor.png)

| <center>變更文字格式 | <center>新增專案符號或編號 | <center>新增或移除文字縮排 |
| --- | --- | --- |
| <center>![使用 rich 文字編輯器來變更文字格式](./media/text-editor3.png) |  <center>![使用 rich 文字編輯器加入清單](./media/text-editor4.png) |  <center>![使用 rich 文字編輯器縮排](./media/text-editor5.png) |

### <a name="privacy-policy-link"></a>隱私權原則連結

輸入您組織的隱私權原則 URL （裝載于您的網站上）。 貴使用者必須負責確保您的應用程式符合隱私權法律和法規，以及提供有效的隱私權原則。

### <a name="useful-links"></a>實用的連結

提供解決方案的選擇性補充線上檔。 按一下 [ **+ 新增連結**] 來新增其他有用的連結。

### <a name="contact-information"></a>連絡資訊

在本節中，您必須提供**支援連絡人**的名稱、電子郵件和電話號碼，以及**工程連絡人**。 此資訊不會向客戶顯示，但可供 Microsoft 使用，並可提供給 CSP 合作夥伴。

### <a name="support-urls"></a>支援 Url

如果您支援**Azure 全球客戶**和/或**Azure Government 客戶**的網站，請在這裡提供這些 url。

### <a name="marketplace-images"></a>Marketplace 映射

在本節中，您可以提供向客戶顯示供應專案時所要使用的標誌和影像。 所有影像都必須是 .png 格式。

>[!NOTE]
>如果您在上傳檔案時發生問題，請確定您的區域網路不會https://upload.xboxlive.com封鎖合作夥伴中心所使用的服務。

#### <a name="marketplace-logos"></a>Marketplace 標誌

提供四個圖元大小的供應專案標誌：

- **小型**（40 x 40）
- **中**（90 x 90）
- **大型**（115 x 115）
- **寬**（255 x 115）

您的標誌應遵循這些指導方針：

- Azure 設計具有簡單的調色盤。 限制標誌上的主要和次要色彩數目。
- 入口網站的佈景主題色彩是白色與黑色。 請勿使用這些色彩作為標誌的背景色彩。 請使用可讓標誌在入口網站突顯出來的色彩。 建議您使用簡單的主要色彩。
- 如果您使用透明背景，請確定標誌與文字不是白色、黑色或藍色。
- 標誌的外觀與風格應該是「一般」，且避免使用漸層。 請不要在標誌上使用漸層背景。
- 請勿在標誌上放置文字 (甚至是公司或品牌名稱)。
- 確定標誌不會自動縮放。

#### <a name="screenshots"></a>螢幕擷取畫面

新增最多五個螢幕擷取畫面，以顯示供應專案的運作方式。 所有螢幕擷取畫面都必須是 1280 x 720 圖元。

#### <a name="videos"></a>影片

您可以選擇性地新增最多五個示範您供應專案的影片。 這些影片應裝載于 YouTube 和/或 Vimeo。 針對每一個，輸入影片的名稱、URL，以及影片的縮圖影像（1280 x 720 圖元）。

#### <a name="additional-marketplace-listing-resources"></a>其他 marketplace 清單資源

- [Marketplace 供應專案清單的最佳做法](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

選取 [**儲存草稿**] 再繼續。

## <a name="preview"></a>預覽

將您的供應專案即時發佈到更廣泛的 marketplace 供應專案之前，您必須先將其提供給有限的預覽物件。 這可讓您先確認您的供應專案會出現在 Azure Marketplace 中，才能讓客戶使用。 Microsoft 支援服務和工程小組也可以在此預覽期間查看您的供應專案。

您可以在 [**預覽物件**] 區段中輸入 Azure 訂用帳戶識別碼，以定義預覽物件。 您可以手動輸入最多10個訂用帳戶識別碼，或上傳包含最多100個訂用帳戶識別碼的 .csv 檔案。

與這些訂用帳戶相關聯的任何客戶，都能在上線之前，在 Azure Marketplace 中看到供應專案。 請務必在此包含您自己的訂用帳戶，以便預覽您的供應專案。

選取 [**儲存草稿**] 再繼續。

## <a name="plan-overview"></a>計畫總覽

每個供應項目都必須有一或多個方案 (有時稱為 SKU)。 您可以新增多個方案來支援設定為不同價格的不同功能，或為特定客戶的限定對象自訂特定方案。 客戶可以檢視父代供應項目下可供他們使用的方案。

在 [**計畫總覽**] 頁面上，選取 [ **+ 建立新方案**]。 然後輸入**方案識別碼**和**方案名稱**。 這兩個值都只能包含小寫英數位元、連字號和底線，且最多50個字元。 這些值可能會向客戶顯示，而且在您發佈供應專案之後，即無法變更。

當您輸入這些值後，請選取 [**建立**]，以繼續處理您的計畫。 有三個區段需要完成：**規劃清單**、**定價和可用性**，以及**技術**設定。

### <a name="plan-listing"></a>計畫清單

首先，提供方案的**搜尋結果摘要**。 這是您計畫的簡短描述（最多100個字元），可用於 marketplace 搜尋結果。

接下來，輸入**描述**，以提供更詳細的計畫說明。

### <a name="pricing-and-availability"></a>價格與可用性

目前，只有一個可用於受控服務供應專案的定價模型：自備**授權（BYOL）**。 這表示您會直接向您的客戶收取此供應項目的相關費用，而且 Microsoft 不會向您收取任何費用。

[**計畫可見度**] 區段可讓您指出此計畫是否應為[私](../../marketplace/private-offers.md)用。 如果您將 [**這是**未核取的私人方案框] 保留為未核取，則不會將您的方案限制為特定客戶（或特定數目的客戶）。

若要使此方案只有特定客戶才能取得，請選取 [是]****。 當您這麼做時，必須提供訂用帳戶識別碼來識別客戶。 您可以輸入一個（最多10個訂用帳戶），或上傳一個 .csv 檔案（在所有方案中的訂用帳戶上限為10000個）。 請務必在此包含您自己的訂用帳戶，以便您可以測試及驗證供應項目。

> [!IMPORTANT]
> 方案發行為公開之後，您就無法將它變更為私用。 若要控制哪些客戶可以接受您的供應專案和委派資源，請使用私用方案。 有了公用方案，您就不能將可用性限制在特定客戶或甚至是特定數目的客戶（不過，如果您選擇這樣做，則可以完全停止銷售方案）。 只有當您在發佈供應專案時，如果您已包含將**角色定義**設為 [[受控服務註冊指派刪除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)] 的**授權**，您就可以在客戶接受供應專案之後，[移除對委派的存取權](../../lighthouse/how-to/remove-delegation.md)。 您也可以與客戶聯繫，並要求他們[移除您的存取權](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)。

### <a name="technical-configuration"></a>技術設定

您計畫的這個區段會建立一個資訊清單，其中包含用來管理客戶資源的授權資訊。 需要此資訊才能啟用[Azure 委派的資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)。

請務必查看[Azure 燈塔案例中的租使用者、角色和使用者](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)，以瞭解支援哪些角色，以及定義授權的最佳作法。

> [!NOTE]
> 如先前所述，您**授權**項目中的使用者和角色將適用於購買方案的每位客戶。 如果您想要限制對特定客戶的存取，則必須發佈私人方案以供其專屬使用。

#### <a name="manifest"></a>file:///

首先，提供資訊清單的 [版本]****。 使用的格式為 *n.n.n* (例如，1.2.5)。

接下來，輸入您的 [租用戶識別碼]****。 這是與您組織的 Azure Active Directory （Azure AD）租使用者識別碼相關聯的 GUID;也就是，您將從中存取客戶資源的管理租使用者。 如果您目前沒有此資訊，您可將滑鼠指標暫留在 Azure 入口網站右上角的帳戶名稱上，或選取 [切換目錄]**** 就能看到它。

如果您發行新版本的供應專案，而且需要建立已更新的資訊清單，請選取 [ **+ 新增資訊清單**]。 請務必增加前一個資訊清單版本的版本號碼。

#### <a name="authorization"></a>授權

授權會定義您的管理租使用者中的實體，而這些實體可以存取購買方案之客戶的資源和訂用帳戶。 每個實體都會指派一個內建角色，以授與特定層級的存取權。

您最多可以為每個方案建立二十個授權。

> [!TIP]
> 在大部分情況下，您會想要將角色指派給 Azure AD 的使用者群組或服務主體，而不是一系列的個別使用者帳戶。 如此一來，當您的存取需求變更時，就可以新增或移除個別使用者的存取權，而不需要更新並重新發佈方案。 將角色指派給 Azure AD 群組時，請[確定**群組類型**為 [**安全性**]，而不是 [ **Office 365**](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)]。 如需其他建議，請參閱 [Azure Lighthouse 案例中的租用戶、角色和使用者](../../lighthouse/concepts/tenants-users-roles.md)。

您必須為每個**授權**提供下列項目。 然後，您可以視需要選取 [ **+ 新增授權**]，以新增更多使用者和角色定義。

- **Azure AD 物件**識別碼：使用者、使用者群組或應用程式的 Azure AD 識別碼，會將特定許可權（如角色定義所定義）授與客戶的資源。
- **Azure AD 物件顯示名稱**：可協助客戶瞭解此授權用途的易記名稱。 客戶會在委派資源時看到此名稱。
- **角色定義**：從清單中選取其中一個可用的 Azure AD 內建角色。 此角色將會決定 [Azure AD 物件識別碼]**** 欄位中的使用者，對於您的客戶資源會有那些權限。 如需這些角色的說明，請參閱[Azure 委派資源管理的](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-delegated-resource-management)[內建角色](../../role-based-access-control/built-in-roles.md)和角色支援。
  > [!NOTE]
  > 當適用的新內建角色新增至 Azure 時，將會在這裡提供，雖然可能會在出現一些延遲的情況下顯示。
- 可**指派的角色**：只有在您已在此授權的**角色定義**中選取 [使用者存取系統管理員] 時，才會顯示此選項。 若是如此，您必須在此新增一或多個可指派的角色。 [ **Azure AD 物件識別碼**] 欄位中的使用者可以將這些角色指派給[受控](../../active-directory/managed-identities-azure-resources/overview.md)識別，以便[部署可補救的原則](../../lighthouse/how-to/deploy-policy-remediation.md)。 請注意，不會有其他一般與「使用者存取系統管理員」角色相關聯的權限套用至此使用者。

> [!TIP]
> 若要確保您可以視需要[移除委派的存取權](../../lighthouse/how-to/remove-delegation.md)，包括將**角色定義**設定為[受控服務註冊指派刪除角色](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的**授權**。 如果未指派此角色，則只有客戶租用戶中的使用者可以移除委派的資源。

完成計畫的所有章節之後，您可以選取 [ **+ 建立新方案**]，視需要建立額外的計畫。 完成時，選取 [儲存]****。

選取 [**儲存草稿**] 再繼續。

## <a name="publish"></a>發佈

### <a name="submit-offer-to-preview"></a>提交供應專案以供預覽

當您完成供應專案的所有必要區段之後，請選取入口網站右上角的 [**審查併發布**]。

如果這是您第一次發佈此供應專案，您可以：

- 請參閱供應專案每個區段的完成狀態。
  - **未啟動**-區段尚未觸及，需要完成。
  - **不完整**-區段包含需要修正的錯誤，或需要提供更多資訊的詳細資訊。 回到一節，並加以更新。
  - **Complete** -區段完成，已提供所有必要的資料，而且沒有任何錯誤。 供應專案的所有區段都必須處於「完整」狀態，您才能提交供應專案。
- 在 [**認證的注意事項**] 區段中，提供測試指示給認證小組，確保您的應用程式已正確測試，以及有助於瞭解應用程式的任何補充注意事項。
- 選取 [**提交**] 以提交供應專案進行發佈。 當供應專案的預覽版本可供您審查和核准時，我們會傳送電子郵件給您。 返回 [合作夥伴中心]，並選取供應專案的 [**上線**]，將您的供應專案發佈至公用（或私用供應專案給私人物件）。

### <a name="customer-experience-and-offer-management"></a>客戶經驗與供應專案管理

當客戶部署您的供應專案時，他們將能夠委派訂用帳戶或資源群組來進行[Azure 委派的資源管理](../../lighthouse/concepts/azure-delegated-resource-management.md)。 如需此程式的詳細資訊，請參閱[客戶上線流程](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)。

您可以隨時[發佈供應項目的更新版本](update-existing-offer.md)。 例如，您可能會想要將新的角色定義新增至先前發佈的供應項目。 當您這麼做時，已新增供應項目的客戶會在 Azure 入口網站的 [[服務提供者****](../../lighthouse/how-to/view-manage-service-providers.md)] 頁面中看到一個圖示，讓他們知道有可用的更新。 每個客戶都可以檢閱變更，並決定是否要更新為新的版本。

## <a name="next-steps"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
- [瞭解 Azure 燈塔](../../lighthouse/overview.md)
