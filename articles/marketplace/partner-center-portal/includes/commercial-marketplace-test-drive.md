---
title: 包含檔案
description: 包含檔案
documentationcenter: partner-center-commercial-marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 08/13/2019
ms.author: dsindona
ms.custom: include file
ms.openlocfilehash: f2446a924ceed37c51779efc9d9e94c0252a2067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80321818"
---
通過 **"測試磁碟機**"選項卡，您可以設置演示（或"試駕"），使客戶能夠在承諾購買之前試用您的產品/服務。 在文章"[什麼是試駕"中瞭解更多資訊。](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) 如果您不想再為產品/服務提供試駕，請返回 **"產品/服務設置**"頁面並取消選中**啟用試駕**。

### <a name="technical-configuration"></a>技術配置
提供以下類型的測試磁碟機，每種磁碟機都有自己的技術配置要求。

- [Azure 資源管理器](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [邏輯應用程式](#technical-configuration-for-logic-app-test-drive)
- [電源 BI（](#technical-configuration-not-required-for-power-bi-test-drives)不需要技術配置）

#### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure 資源管理器測試磁碟機的技術配置

包含構成解決方案的所有 Azure 資源的部署範本。 適合此方案的產品僅使用 Azure 資源。 詳細瞭解如何設置 Azure[資源管理器測試磁碟機](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)。

- **區域**（必需）：目前有 26 個 Azure 支援的區域，可以在其中提供您的試駕。 通常，您需要在預期客戶數量最多的區域提供試駕，以便他們選擇最接近的區域以獲得最佳性能。 您需要確保允許訂閱部署所選擇的每個區域所需的所有資源。

- **實例**：選擇類型（熱或冷）和可用實例數，這些實例將乘以產品/服務可用的區域數。

**熱**：這種類型的實例已部署，並等待每個選定區域的訪問。 客戶可以立即訪問測試磁碟機*的熱*實例，而無需等待部署。 缺點是，這些執行個體一直在您的 Azure 訂用帳戶中執行，因此將會產生可觀的運作成本。 強烈建議至少使用一個*熱*實例，因為大多數客戶不希望等待完全部署，如果沒有*熱*實例，則會導致客戶使用量下降。

**冷**：這種類型的實例表示每個區域可能部署的實例總數。 冷實例要求在整個測試磁碟機資源管理器範本在客戶請求試駕時進行部署，因此*冷*實例的載入速度比*熱*實例慢得多。 權衡是，您只需要在試駕期間付費，它*並不總是*像*使用熱*實例那樣在 Azure 訂閱上運行。

- **試用 Azure 資源管理器範本**：上載包含 Azure 資源管理器範本的 .zip。  在快速入門文章"[使用 Azure 門戶創建和部署 Azure 資源管理器範本"](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)中瞭解有關創建 Azure 資源管理器範本詳細資訊。

- **試駕持續時間**（必需）：輸入試駕將保持活動狀態的時間長度（以小時數計）。 在此持續時間過後，試用產品將會自動終止。 此持續時間只能由整個小時數設置（例如"2"小時，"1.5"無效）。

#### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 試駕的技術配置

Microsoft 可以通過託管和維護使用此類測試磁碟機的服務預配和部署來消除設置測試磁碟機的複雜性。 無論試駕是面向業務中心、客戶參與還是運營受眾，此類託管試駕的配置都是相同的。

- **最大併發測試磁碟機**（必需）：設置一次可以使用試駕的最大客戶數。 每個併發使用者將在測試磁碟機處於活動狀態時使用 Dynamics 365 許可證，因此您需要確保有足夠的許可證來支援最大限制集。 建議值為 3-5 個。

- **試駕持續時間**（必需）：通過定義小時數輸入試駕將保持活動狀態的時間長度。 在此多小時後，會話將結束，不再使用您的許可證之一。 根據產品/服務的複雜性，我們建議值為 2-24 小時。 此持續時間只能由整個小時數設置（例如"2"小時，"1.5"無效）。  如果使用者時間已用完，並且希望再次訪問試駕，使用者可以請求新的會話。

- **實例 URL（** 必需）：客戶將開始其試駕的 URL。 通常，使用安裝示例資料（例如`https://testdrive.crm.dynamics.com`），運行應用的 Dynamics 365 實例的 URL。

- **實例 Web API URL（** 必需）：通過登錄到 Microsoft 365 帳戶並導航到 **"設置**\&"gt，檢索 Dynamics 365 實例的 Web API URL;**自訂**\&gt;**開發人員資源**\&gt;**實例 Web API（服務根 URL），** 複製此處找到的 URL（例如`https://testdrive.crm.dynamics.com/api/data/v9.0`）。

- **角色名稱**（必需）：提供您在自訂 Dynamics 365 試駕中定義的安全形色名稱。 這將在使用者試駕期間（例如，測試-磁碟機角色）分配給使用者。

#### <a name="technical-configuration-for-logic-app-test-drive"></a>邏輯應用試駕的技術配置

任何自訂產品都應使用這種類型的試駕部署範本，該範本包含各種複雜的解決方案體系結構。 有關設置邏輯應用測試磁碟機的詳細資訊，請訪問 GitHub 上的[操作](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md)[和客戶參與度](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app)。

- **區域**（必需，單選下拉清單）：目前有 26 個 Azure 支援的區域，可以在其中提供您的試駕。 邏輯應用的資源將部署在您選擇的區域中。 如果邏輯應用在特定區域中存儲了任何自訂資源，請確保在此處選擇該區域。 執行此操作的最佳方法是在門戶中的 Azure 訂閱中本地完全部署邏輯應用，並在進行此選擇之前驗證其是否正常工作。

- **最大併發測試磁碟機**（必需）：設置一次可以使用試駕的最大客戶數。 這些測試磁碟機已部署，使客戶能夠立即訪問它們，而無需等待部署。

- **試駕持續時間**（必需）：輸入試駕將保持活動狀態的時間長度（以小時數計）。 此時間段結束後，試駕將自動終止。

- **Azure 資源組名稱**（必需）：輸入邏輯應用測試磁碟機保存的[Azure 資源組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)名稱。

- **Azure 邏輯應用名稱**（必需）：輸入將測試磁碟機分配給使用者的邏輯應用的名稱。 此邏輯應用必須保存在上面的 Azure 資源組中。

- **取消預配邏輯應用名稱**（必需）：輸入邏輯應用的名稱，該應用在客戶完成後取消配置試駕。 此邏輯應用必須保存在上面的 Azure 資源組中。

#### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 測試磁碟機不需要技術配置

想要演示互動式 Power BI 視覺物件的產品可以使用嵌入式連結共用自訂儀表板作為試駕，無需進一步的技術配置。 詳細瞭解如何設置[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)範本應用。

### <a name="deployment-subscription-details"></a>部署訂閱詳細資訊

為了代表您部署測試磁碟機，請創建並提供單獨的唯一 Azure 訂閱。 （電源 BI 測試磁碟機不需要）。

- **Azure 訂閱 ID（Azure**資源管理器和邏輯應用需要）：輸入訂閱 ID 以授予對 Azure 帳戶服務的存取權限，以便進行資源使用方式報告和計費。 如果尚未創建，我們建議您考慮[創建單獨的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)以用於測試磁碟機。 您可以通過登錄到[Azure 門戶](https://portal.azure.com/)並導航到左側功能表的 **"訂閱"** 選項卡來查找 Azure 訂閱 ID。 選擇該選項卡將顯示您的訂閱 ID（例如"a83645ac-1234-5ab6-6789-1h234g764ghty"）。

- **Azure AD 租戶 ID（** 必需）：輸入 Azure 活動目錄 （AD）[租戶 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 要查找此 ID，請登錄到[Azure 門戶](https://portal.azure.com/)，選擇左側功能表中的"活動目錄"選項卡，選擇 **"屬性**"，然後查找列出的**目錄 ID**號（例如 50c464d3-4930-494c-963c-1e951d15360e）。 您還可以使用功能變數名稱 URL 在 ：[https://www.whatismytenantid.com](https://www.whatismytenantid.com)查找組織的租戶 ID。

- **Azure AD 租戶名稱**（動態 365 需要）：輸入 Azure 活動目錄 （AD） 名稱。 要查找此名稱，請登錄到[Azure 門戶](https://portal.azure.com/)，在右上角，租戶名稱將列在您的帳戶名稱下。

- **Azure AD 應用 ID（** 必需）：輸入 Azure 活動目錄 （AD）[應用程式 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)。 要查找此 ID，請登錄到[Azure 門戶](https://portal.azure.com/)，選擇左側功能表中的"活動目錄"選項卡，選擇**應用註冊**，然後查找列出的應用程式**ID**號（例如 50c464d3-4930-494c-963c-1e951d15360e）。

- **Azure AD 應用用戶端機密**（必需）：輸入 Azure AD 應用程式[用戶端金鑰](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)。 要查找此值，請登錄到 Azure[門戶](https://portal.azure.com/)。 選擇左側功能表中的**Azure 活動目錄**選項卡，選擇**應用註冊**，然後選擇試駕應用。 接下來，選擇**證書和機密**，選擇 **"新建用戶端機密**"，輸入說明，選擇 **"從不在過期**"下，然後選擇"**添加**"。 **Never** 請確保向下複製該值。 （執行此操作之前不要離開頁面，否則您將無法訪問該值。

在繼續下一節之前，請記住**保存**！

### <a name="test-drive-listings-optional"></a>試駕清單（可選）

**"試駕****"選項卡下的"試駕清單**"選項顯示您的試駕可用的語言（和市場），當前英語（美國）是唯一可用的位置。 此外，此頁顯示特定于語言的清單的狀態以及添加該清單的日期/時間。 您需要為每個語言/市場定義試駕詳細資訊（說明、使用者手冊、視頻等）。

- **說明**（必需）：描述您的試駕、演示的內容、供使用者試驗的目標、要流覽的功能以及任何相關資訊，以説明使用者確定是否獲取您的產品/服務。 此欄位中最多可輸入 3，000 個字元的文本。 

- **訪問資訊**（Azure 資源管理器和邏輯測試磁碟機所需的資訊）：解釋客戶需要瞭解哪些內容才能訪問和使用此測試磁碟機。 演練使用產品/服務的方案，以及客戶在整個試駕中訪問功能的準確情況。 此欄位中最多可輸入 10，000 個字元的文本。

- **使用者手冊**（必需）：對試駕體驗的深入演練。 《使用者手冊》應準確介紹您希望客戶從體驗試駕中獲得什麼，並作為他們可能存在的任何問題的參考。 檔必須採用 PDF 格式，並在上載後命名（最多 255 個字元）。

- **視頻：添加視頻**（可選）：視頻可以上傳到 YouTube 或 Vimeo，並在此處引用連結和縮略圖圖像 （533 x 324 圖元），以便客戶可以查看流覽資訊，説明他們更好地瞭解試駕，包括如何成功使用您的產品/服務的功能，並瞭解突出其優勢的方案。
  - **名稱**（必需）
  - **網址（僅限 YouTube 或 Vimeo）（** 必填）
  - **縮略圖 （533 x 324px）**： 影像檔必須採用 PNG 格式。

完成這些欄位後選擇 **"保存**"。
