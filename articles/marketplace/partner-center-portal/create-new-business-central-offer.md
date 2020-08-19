---
title: 建立 Dynamics 365 Business Central 供應項目 - Microsoft 商業市集
description: 瞭解在合作夥伴中心的 Microsoft 商用 marketplace 中建立新的 Dynamics 365 Business Central 供應專案的步驟和考慮。 您可以在 Azure Marketplace 中或透過雲端解決方案提供者 (CSP) 方案，列出或銷售您的供應項目。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 08/07/2020
ms.openlocfilehash: 0d00f779031ec294523c861a4e9868f7cbbe790d
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606140"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>建立 Dynamics 365 Business Central 供應項目

本文將說明如何建立新的 Dynamics 365 Business Central 供應專案。 [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) 是一種企業資源規劃 (ERP) 系統，可處理各種商業流程，包括財務、營運、供應鏈、CRM、專案管理和電子商務。 進階套件也支援傳統部署模型和製造。 Dynamics 365 Business Central 的所有供應項目都必須經過我們的認證程序。

開始之前，請先[在合作夥伴中心建立商業市集帳戶](create-account.md) (如果您尚未有帳戶)。 請確定您已在商業市集方案中註冊帳戶。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[ [合作夥伴中心]](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 **[商業市集]**  >  [概觀]。
3. 在 [概觀] 頁面上，選取 **[+ 新增供應項目]**  >  **[Dynamics 365 Business Central]** 。

    ![說明左側導覽功能表。](./media/new-offer-dynamics-365-business-central.png)

> [!NOTE]
> 供應專案發佈之後，在合作夥伴中心中進行的編輯，只會在重新發佈供應專案之後出現在線上商店中。 請務必在進行變更之後，一律重新發佈。

## <a name="new-offer"></a>新增供應項目

輸入**供應項目識別碼**。 這是您帳戶中每個供應項目的唯一識別碼。

- 客戶可以在市集供應項目和 Azure Resource Manager 範本 (如果適用) 的網址中看到此識別碼。
- 請一律使用小寫字母和數字。 其可以包含連字號和底線，但不能有空格，且限制為 50 個字元。 例如，如果您輸入 **test-offer-1**，供應項目網址將為 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 選取 [建立] 之後，即無法變更供應項目識別碼。

輸入**供應項目別名**。 這是用於合作夥伴中心內供應項目的名稱。

- 此名稱不會在市集中使用，且與向客戶顯示的供應項目名稱和其他值不同。
- 在您選取 [建立] 之後，就無法變更供應項目別名。

選取 [建立] 以產生供應項目並繼續。

## <a name="offer-setup"></a>供應項目設定

請遵循下列步驟來設定供應項目。

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>您要讓潛在客戶如何與此清單供應項目互動？

選取您想要用於此供應項目的選項。

#### <a name="get-it-now-free"></a>立即取得 (免費)

提供有效的 URL (網址開頭為 HTTP 或 HTTPS)，免費向客戶列出您的供應項目，使其能存取您的應用程式。  例如： `https://contoso.com/my-app` 。

#### <a name="free-trial-listing"></a>免費試用版 (清單)

提供有效的 URL (開頭 `http` 或 `https`) 可取得試用版的有效 URL，向客戶列出您的供應專案。  例如： `https://contoso.com/trial/my-app` 。 供應項目清單免費試用版是由您的服務所建立、管理及設定，且沒有由 Microsoft 管理的訂閱。

> [!NOTE]
> 您的應用程式透過試用連結所接收的權杖，只能經由 Azure Active Directory (Azure AD) 取得使用者資訊，以便在應用程式中自動建立帳戶。 Microsoft 帳戶不支援使用此權杖進行驗證。

#### <a name="contact-me"></a>與我連絡

透過連接客戶關係管理 (CRM)系統收集客戶連絡人資訊。 系統會要求客戶提供權限，以分享客戶資訊。 這些客戶詳細資料，連同他們在您的供應項目中找到的供應項目名稱、識別碼和市集來源，都會傳送至您所設定的 CRM 系統。 如需設定 CRM 的詳細資訊，請參閱[潛在客戶管理](#customer-leads)一節。

### <a name="test-drive"></a>試用產品

試用產品是向潛在客戶展示供應項目的絕佳方式，藉由提供他們「先試用再購買」的選項，進而增加轉換，並且獲得最適合的潛在客戶。 若要深入瞭解，請從 [什麼是試用](../what-is-test-drive.md)產品著手。

若要在固定時間啟用試用產品，請選取 [啟用試用產品] 核取方塊。 若要從您的供應項目中移除試用產品，請清除此核取方塊。

### <a name="customer-leads"></a>潛在客戶

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

如需詳細資訊，請參閱[潛在客戶管理概觀](./commercial-marketplace-get-customer-leads.md)。

選取 [儲存草稿] 後再繼續。

## <a name="properties"></a>屬性

此頁面可讓您定義用來在市集上將供應項目分組的類別和產業、應用程式版本以及支援供應項目的法律合約。

### <a name="category"></a>類別

選取類別和子類別，以將您的供應專案放在適當的 marketplace 搜尋區域中。 請務必在供應項目描述中描述您的供應項目如何支援這些類別。 選取：

- 至少一個和多個類別，包括主要和次要類別 (選擇性) 。
- 每個主要和/或次要類別最多可有兩個子類別。 如果沒有任何子類別適用于您的供應專案，請選取 [ **不適用**]。

請參閱 [供應專案清單最佳做法](../gtm-offer-listing-best-practices.md)中的類別和子類別的完整清單。

### <a name="industry"></a>業界

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>應用程式版本

輸入供應項目的版本號碼。 客戶會看到此版本列在供應項目的詳細資料頁面上。

### <a name="terms-and-conditions"></a>條款及條件

若要提供您自己的法律條款及條件，請在 [條款及條件] 欄位中輸入。 您也可以提供 URL，以便於找到條款及條件。 客戶將必須先接受這些條款，才能試用您的供應項目。

選取 [儲存草稿] 後再繼續。

## <a name="offer-listing"></a>供應項目清單

您可以在此定義供應項目的詳細資料，例如名稱、描述和影像。

> [!NOTE]
> 您只能以一種語言提供供應項目清單的詳細資料。 如果供應項目描述開頭為以下詞句：「此應用程式僅適用於 [非英文語言]。」，則供應項目詳細資料不一定要使用英文。 也可利用不同於供應項目清單內容中所使用的語言，來提供供應項目內容的「說明連結 URL」。

以下是供應專案資訊在 Microsoft AppSource 中的顯示方式範例 (任何列出的價格僅供範例用途，不適合反映實際成本) ：

:::image type="content" source="media/example-d365-business-central.png" alt-text="說明這項供應專案在 Microsoft AppSource 中的顯示方式。":::

#### <a name="call-out-descriptions"></a>撥出描述

1. 標誌
2. 產品
3. 類別
4. 支援位址 (連結) 
5. 使用規定
6. 隱私權原則
7. 供應項目名稱
8. 摘要
9. 描述
10. 螢幕擷取畫面/影片

### <a name="name"></a>名稱

您在此處輸入的名稱會作為供應項目清單標題向客戶顯示。 當您建立供應項目時，此欄位會預先填入您為**供應項目別名**輸入的文字，但您可以變更此值。 此名稱可以是商標 (您也可以包含商標或著作權符號)。 名稱不能超過 50 個字元，而且不能包含任何表情圖示。

### <a name="short-description"></a>簡短描述

提供供應項目的簡短描述，最多 100 個字元。 此描述可用於市集搜尋結果。

### <a name="description"></a>描述

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>搜尋關鍵字

您可以選擇輸入最多三個搜尋關鍵字，協助客戶在市集中尋找您的供應項目。 為了獲得最佳結果，請在您的描述中也嘗試使用這些關鍵字。

### <a name="products-your-app-works-with"></a>與您的應用程式一起運作的產品

如果您想要讓客戶知道您的應用程式可搭配特定產品，請在這裡輸入最多三個產品名稱。

### <a name="helpprivacy-urls"></a>說明/隱私權 URL

本節可讓您提供連結，協助客戶深入了解您的供應項目。

#### <a name="help-link"></a>說明連結

輸入客戶可以深入了解供應項目的 URL。 您的**說明連結**不能與**支援 URL** 相同 (如下所述)。

#### <a name="privacy-policy-link"></a>隱私權原則連結

輸入貴組織隱私權原則的網址。 您必須負責確保您的應用程式符合隱私權法律和法規，並提供有效的隱私權原則。

### <a name="contact-information"></a>連絡資訊

在此區段中，您必須提供 [支援連絡人] 與 [工程連絡人] 的姓名、電子郵件和電話號碼。 此資訊不會向客戶顯示，但可供 Microsoft 使用，且可能也會提供給雲端解決方案提供者合作夥伴。

在 [支援連絡人] 區段中，您還必須提供 [支援 URL]，其中雲端解決方案提供者合作夥伴可以在此找到供應項目的支援。 您的支援 URL 不能與**說明連結**相同。

### <a name="supporting-documents"></a>支援文件

在此提供至少一個 (最多三個) 相關的行銷文件，例如白皮書、小手冊、檢查清單或簡報。 這些文件必須是 .pdf 格式。

### <a name="marketplace-images"></a>Marketplace 映像

請提供供應項目的標誌和影像。 所有影像都必須是 PNG 格式。 

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

以兩種大小上傳您的供應項目標誌：

* **小型** (48 x 48 像素)
* **大型** (216 x 216 像素)

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

>[!NOTE]
>如果在上傳檔案時發生問題，請確定您的區域網路不會封鎖合作夥伴中心所使用的 `https://upload.xboxlive.com` 服務。

#### <a name="screenshots"></a>螢幕擷取畫面

新增螢幕擷取畫面，以顯示供應項目的運作方式。 至少需要三個螢幕擷取畫面，最多可以新增至五個。 所有螢幕擷取畫面都必須是 1280 x 720 像素。

#### <a name="videos"></a>影片

您可以選擇新增最多五個示範您供應項目的影片。 這些影片應裝載於 YouTube 和/或 Vimeo 上。 針對每一個影片，輸入影片名稱、URL 和影片的縮圖影像 (1280 x 720 像素)。

#### <a name="additional-marketplace-listing-resources"></a>其他市集清單資源

[市集供應項目清單的最佳做法](../gtm-offer-listing-best-practices.md)

選取 **[儲存草稿]** 後再繼續。

## <a name="availability"></a>可用性

此頁面可讓您選擇提供供應項目的位置和方式。

### <a name="markets"></a>市場

此區段可讓您指定可提供供應項目的市場。 若要這麼做，請選取 [編輯市場] **，** 隨即會顯示 [市場選取項目] 快顯視窗。

請至少選取一個市場以發佈供應項目。 選擇 [選取所有]，在每個可能的市場中提供供應項目，或選取您想要新增的特定市場。

您在此選擇的項目僅適用於新的銷售；如果有人在特定市場上已有您的應用程式，而您後來移除了該市場，則該市場中已有該供應項目的人可以繼續使用，但該市場中的新客戶將無法取得供應項目。

> [!IMPORTANT]
> 即使這些需求並未列於此處或合作夥伴中心，您仍有責任配合當地法律的任何需求。

請記住，即使您選取所有市場，當地法律、限制或其他因素可能會導致某些國家/地區不會列出特定供應項目。

### <a name="preview-audience"></a>預覽對象

將供應項目在更廣泛的市集供應項目上線之前，您必須先將其提供給有限的**預覽對象**。 在此輸入 [隱藏金鑰] (任何僅用小寫字母及/或數字的字串)。 預覽對象的成員可以使用此隱藏金鑰做為權杖，並在市集中檢視供應項目的預覽。

然後，當準備好讓供應項目可供使用並移除預覽限制時，您將需要移除 [隱藏金鑰] 並重新發佈。

選取 **[儲存草稿]** 後再繼續。

## <a name="technical-configuration"></a>技術設定

此頁面會定義用來連接到供應項目的技術詳細資料。 如果終端客戶選擇購買項目，此連線可讓我們為他們佈建您的供應項目。

### <a name="package-type"></a>套件類型

選取適用於您供應項目的選項：

* **附加元件** – 附加元件應用程式會擴充 Dynamics 365 Business Central 的體驗和現有功能。 如需詳細資訊，請參閱[附加元件應用程式](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)。
* **連線** – 當您必須在 Dynamics 365 Business Central 與第三方解決方案或服務之間建立點對點連線時，您就可以使用連線應用程式。 如需詳細資訊，請參閱[連線應用程式](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)。

### <a name="file-upload"></a>檔案上傳

如果您選取了上方的**附加元件**，則您會在其中上傳供應項目的套件檔案，以及其所有相依擴充功能的套件檔案。

#### <a name="extensions-package-file"></a>擴充功能套件檔案

上傳供應項目的擴充功能套件檔案 (.app)。

#### <a name="library-package-file"></a>程式庫套件檔案

如果您的供應項目必須與另一個不會發佈至市集的擴充功能一起安裝，則此為必要項目。 若是如此，請在這裡上傳其 .app 檔。

#### <a name="dependency-package-file"></a>相依性套件檔案

如果您的供應項目必須與另一個已發佈至市集的擴充功能一起安裝，則此為必要項目。 若是如此，請在這裡上傳其 `.app` 或 `.zip` 檔案。

### <a name="url-to-app-installation"></a>應用程式安裝的 URL

如果您選取了上方的 [連線]，請在這裡提供您應用程式安裝的位址。 針對不需要安裝的已連線服務，請提供服務登陸頁面或註冊頁面的位址。

選取 [儲存草稿] 後再繼續。

## <a name="test-drive-technical-configuration"></a>試用產品技術設定

此頁面可讓您設定示範 (即「試用產品」)，讓客戶在購買之前先試用您的供應項目。 深入瞭解 [什麼是試用](../what-is-test-drive.md)產品。

若要啟用試用產品，請在 [供應項目設定](#test-drive) 索引標籤中，選取 [啟用試用產品] 核取方塊。若要從您的供應項目中移除試用產品，請清除此核取方塊。

當您完成試用產品的設定之後，請選取 [ **儲存草稿** ]，然後再繼續。

## <a name="supplemental-content"></a>補充內容

此頁面可讓您提供供應項目的其他相關資訊，以協助我們驗證您的供應項目。 此資訊不會向客戶顯示或發佈至市集。

### <a name="target-release"></a>目標版本

指出解決方案鎖定的 Microsoft Dynamics Business Central 版本：**目前**、**下一個主要版本**或**下一個次要版本**。 此資訊可讓我們適當地測試您的解決方案。

### <a name="supported-editions"></a>支援的版本

如果您的供應項目需要進階版本的 Microsoft Dynamics 365 Business Central，請僅選取 [進階]。 否則，請同時選取 [基本] 和 [進階]。

### <a name="key-usage-scenario"></a>重要使用案例

您必須上傳一個 PDF 檔案，在文件 (.pdf 格式) 中列出您供應項目的重要使用案例。 我們的驗證小組可能會先驗證此處所列的所有案例，然後才核准您將供應項目發佈到市集。

### <a name="app-tests-automation"></a>應用程式測試自動化

如果您的供應項目是附加元件應用程式，您必須上傳**應用程式測試自動化** 檔案 (.app)。 此檔案不適用於連線應用程式。

### <a name="test-accounts"></a>測試帳戶

如果需要測試帳戶才能讓認證小組正確地檢閱供應項目，請上傳包含**測試帳戶**資訊的 .pdf、.doc 或 .docx 檔案。

## <a name="publish"></a>發佈

### <a name="submit-offer-to-preview"></a>提交供應項目以供預覽

當您完成供應項目的所有必要區段之後，請選取入口網站右上角的 [發佈]。 系統會將您重新導向至 [檢閱和發佈] 頁面。 

如果這是您第一次發佈此供應項目，您可以：

- 查看供應項目每個區段的完成狀態。
    - 尚未開始 - 表示此區段必須已完成卻尚未觸及。
    - *不完整* – 表示此區段中有需要修正的錯誤，或是需要您提供更多詳細資訊。 回到上一個區段並加以更新。
    - *完成* – 表示此區段已完成，並已提供所有必要的資料且沒有任何錯誤。 供應項目的所有區段皆需為完成狀態，您才能提交供應項目。
- 在 [認證注意事項] 區段中，請將測試指示提供給認證小組，以確保您的應用程式已正確測試，並加上有助於了解應用程式的任何補充注意事項。
- 若要提交供應項目以進行發佈，請選取 [提交]。 當供應項目的預覽版本可供檢閱及核准時，我們會向您傳送一封電子郵件。 返回 [合作夥伴中心]， **然後選取供應** 專案的 [上線]，將您的供應專案發佈到公用。

## <a name="next-steps"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
