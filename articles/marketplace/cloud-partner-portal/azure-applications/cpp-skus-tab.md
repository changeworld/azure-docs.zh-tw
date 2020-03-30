---
title: 為 Azure 應用程式產品配置 SKU |Azure 應用商店
description: 如何設定 Azure 受控應用程式和 Azure 解決方案範本的 SKU。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 043394a1303456ce5b209bb84b5afaf09f6beba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289068"
---
# <a name="azure-application-skus-tab"></a>Azure 應用程式 SKU 索引標籤

本文說明如何使用 SKU 索引標籤來建立 Azure 應用程式。 

> [!IMPORTANT]
> 設定 SKU 的步驟與受控應用程式供應項目和解決方案範本供應項目的步驟不同。 如需了解這些差異，請參閱這篇文章。 

## <a name="configure-azure-application-skus"></a>設定 Azure 應用程式 SKU

### <a name="create-a-new-sku"></a>建立新的 SKU

若要建立新的 SKU，使用下列步驟：

1. 選取 [SKU]**** 索引標籤。
2. 在 SKU 下，選取 [+ 新增 SKU]****。

    ![新增 SKU 提示](./media/azureapp-plus-sku.png)

3. 在 [新增 SKU] 快顯視窗中，輸入 [SKU 識別碼]****。 此識別碼的長度限制為 50 個字元，且只能包含小寫英數字元、連字號或底線。 SKU ID 不能以破折號結尾。
4. 客戶會看到此 SKU 識別碼顯示在產品的 URL、Resource Manager 範本 (如果適用) 和計費報告中。 發佈產品/服務後，無法修改此 ID。

### <a name="sku-details-for-a-solution-template"></a>解決方案範本的 SKU 詳細資料

下一個螢幕截圖將顯示解決方案範本的 SKU 詳細資訊表單。

![解決方案範本的 SKU 詳細資料表單](./media/azureapp-sku-details-solutiontemplate.png)

提供以下 SKU 值。  需要附加星號的欄位。

|    欄位         |       描述                                                            |
|  ---------       |     ---------------                                                          |
|  **標題\***     | SKU 的標題。 此標題顯示在此項目的資源庫中。   |
| **總結\***    | SKU 的簡短摘要說明。 (長度上限是 100 個字元。)  |
| **描述\*** | SKU 的詳細說明。 支援基本 HTML。                 | 
| **SKU 類型\***   | Azure 應用程式解決方案的類型，選擇此方案的 "**解決方案範本**"。 |
| **雲可用性\*** | SKU 的位置。 預設值為**公共 Azure**。  <b/>   **公共 Azure** - 應用將部署到具有市場集成的所有公共 Azure 區域的客戶。  <b/>   **Azure 政府雲**- 應用將部署在 Azure 政府雲中。 在發佈到[Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)之前，Microsoft 建議發行者測試並驗證其解決方案在此環境中按預期工作。 若要執行暫存和測試，請要求[試用帳戶](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)。  |
| **這是私有 SKU 嗎？\*** | 如果此 SKU 僅對選定的客戶組可用，請選擇"**是**"。 |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government 是政府社群雲端，US 聯邦政府、本地客戶或部落客戶，及符合資格可以為這些實體提供服務的合作夥伴，擁有此雲端的控管權限。


### <a name="sku-details-for-managed-application"></a>受控應用程式的 SKU 詳細資料

下一個螢幕擷取畫面會顯示受控應用程式的 SKU 詳細資料表單。

   ![受控應用程式的 SKU 詳細資料表單](./media/azureapp-sku-details-managedapplication.png)

配置以下 SKU 設置。 需要附加星號的欄位。

|    欄位         |       描述                                                            |
|  ---------       |     ---------------                                                          |
|  **標題\***     | SKU 的標題。 此標題顯示在此項目的資源庫中。   |
| **總結\***    | SKU 的簡短摘要說明。 (長度上限是 100 個字元。)  |
| **描述\*** | SKU 的詳細說明。 支援基本 HTML。                 | 
| **SKU 類型\***   | Azure 應用程式解決方案的類型，選擇此方案的**託管應用程式**。 
| **雲可用性\*** | SKU 的位置。 預設值為**公共 Azure**。  <b/>   **公共 Azure** - 應用將部署到具有市場集成的所有公共 Azure 區域的客戶。  <b/>   **Azure 政府雲**- 應用將部署在 Azure 政府雲中。 在發佈到[Azure 政府](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)之前，Microsoft 建議發行者測試並驗證其解決方案在此環境中按預期工作。 若要執行暫存和測試，請要求[試用帳戶](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)。   Microsoft Azure Government 是政府社群雲端，US 聯邦政府、本地客戶或部落客戶，及符合資格可以為這些實體提供服務的合作夥伴，擁有此雲端的控管權限。 |
| **這是私有 SKU 嗎？\*** | 如果此 SKU 僅對選定的客戶組可用，請選擇"**是**"。 |
| **國家/地區可用性\*** | 使用 **"選擇區域"** 查看可用國家/地區的清單。 檢查每個國家/地區，然後選取 [確定]**** 以儲存您的選擇。  <b/>   ![國家/地區和區域可用性清單](./media/azure-app-select-country-region.png)  |
| **舊定價\*** | SKU 的價格，以每月 USD 為單位。 價格會根據設定時當前匯率的當地貨幣來設定。 由於您最後會擁有這些設定，請驗證這些選項。 要單獨設置或查看每個國家/地區的價格，請匯出定價試算表並輸入自訂定價。  您必須保存定價更改，才能匯出/導入定價資料。  |
| **簡化貨幣定價\*** | SKU 的價格，以每月 USD 為單位。 這必須與舊的定價相同。 如需詳細資訊，請參閱[簡化的貨幣定價](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer)。 |
|  |  |


### <a name="package-details-for-solution-template"></a>解決方案範本的套件詳細資料

![解決方案範本的套件詳細資料](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

提供以下**包詳細資訊**值。  需要附加星號的欄位。

- **版本\***- 要上載的包的版本。 版本標籤格式應為 X.Y.Z，其中 X、Y 和 Z 是整數。
- **包檔 （.zip）\* ** - 此包包含以下檔，保存在 .ZIP 檔案中。
  - **mainTemplate.json\* ** - 用於部署解決方案/應用程式並創建為解決方案定義的資源的部署範本檔。 有關詳細資訊，請參閱[如何編寫部署範本檔](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)。
  - **createUIDefinition.json\* ** - Azure 門戶使用此檔來生成使用者介面以預配此解決方案/應用程式。 如需詳細資訊，請參閱[為您的受控應用程式建立 Azure 入口網站使用者介面](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)。
  - 腳本（如果需要） - 運行範本時可能需要的任何其他腳本，例如 。 `Microsoft.Compute/virtualMachines/extensions`
  - 嵌套範本（如果需要） - 任何其他嵌套範本。

  > [!IMPORTANT] 
  > 此套件應該包含佈建此應用程式所需的任何巢狀範本或指令碼。 主範本.json 檔和 createUIDefinition.json 檔必須位於根資料夾中。 有關部署專案的詳細資訊，請參閱 Azure[資源管理器範本 - 最佳實踐指南](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts)。


### <a name="package-details-for-managed-application"></a>受控應用程式的套件詳細資料

   ![受控應用程式的套件詳細資料](./media/azureapp-sku-pkgdetails-managedapplication.png)

提供以下包詳細資訊。  需要附加星號的欄位。

- **版本\***- 要上載的包的版本。 版本標籤格式應為 X.Y.Z，其中 X、Y 和 Z 是整數。
- **包檔 （.zip）\* ** - 此包包含以下檔，保存在 .ZIP 檔案中。
  - applianceMainTemplate.json - 用來部署解決方案/應用程式並建立所定義資源的部署範本檔案。 有關詳細資訊，請參閱[快速入門：使用 Azure 門戶創建和部署 Azure 資源管理器範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。 
  - applianceCreateUIDefinition.json - Azure 入口網站會使用這個檔案來產生使用者介面，以便佈建此解決方案/應用程式。 如需詳細資訊，請參閱[為您的受控應用程式建立 Azure 入口網站使用者介面](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)。
  - mainTemplate.json - 只包含 Microsoft.Solution/appliances 資源的範本檔案。 有關詳細資訊，請參閱瞭解[Azure 資源管理器範本的結構和語法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)。 <br>
請注意這項資源的下列索引鍵屬性：
    - "種類" - 在市場管理應用程式中，該值應為"市場"。
    - "託管資源組Id" - 將部署設備MainTemplate.json中定義的所有資源組。
    - "PublisherPackageId" - 唯一識別套件的字串。 此值需要構造如下：它是 [發行者 Id] 的串聯。[優惠 Id]-預覽 [SKUID]。[包版本]。

  >[!IMPORTANT] 
  >此套件應該包含佈建此應用程式所需的任何巢狀範本或指令碼。 這些檔必須位於根資料夾中：MainTemplate.json、設備MainTemplate.json和"設備CreateUI定義.json"。

- **租戶\*ID** - 組織的 Azure 活動目錄租戶 ID。
- **啟用 JIT 訪問？\* ** - 選擇 **"是**"以啟用使用此產品/服務的客戶部署的即時管理存取權限。

  >[!NOTE] 
  >如果您啟用 JIT，則必須更新 CreateUiDefinition.json 檔案以支援 JIT 存取。

針對受控應用程式，您必須設定授權和原則設定。


#### <a name="authorization"></a>授權

將使用者、群組或應用程式的 Azure Active Directory 識別碼，新增至您要向其授與受控資源群組的存取權。 授予的許可權由角色定義 Id 指示。它可以是擁有者、參與者或任何自訂角色。


#### <a name="policy-settings"></a>原則設定

新增受控應用程式符合的原則。 如需深入了解 Azure 資源原則，請參閱[什麼是 Azure 原則？](../../../governance/policy/overview.md)

   ![受控應用程式的授權和原則設定](./media/azureapp-sku-details-managedapp-auth-policy.png)

**如需建立新的授權：**

1. 在 [授權]**** 下，選取 [+ 新增授權]****。
2. 針對**主體識別碼**，將使用者、群組或應用程式的 Azure Active Directory 識別碼，輸入至您要向其授與受控資源群組的存取權。 授予的許可權由角色定義指示。
3. 對於**角色定義**，從下拉清單中選擇這些選項之一：擁有者或參與者。 如需詳細資訊，請參閱 [Azure 資源的內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

>[!NOTE] 
>可以新增多個授權。 但是，建議創建活動目錄使用者組，並在"主要 Id"中指定其 ID。 如此可新增更多使用者到使用者群組，而不需要更新 SKU。

**如需建立新的原則：**

1. 在 [原則設定]**** 下，選取 [+ 新增原則]****。
2. 針對 [原則名稱]****，請輸入原則名稱。 該名稱的長度上限為 50 個字元。
3. 針對 [原則]****，從下拉式清單中選取其中一個選項： 選擇當應用程式使用資料時，資料提供者要啟用的原則。 如需詳細資訊，請參閱 [Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/index)。

    ![受控應用程式的授權設定](./media/azureapp-sku-policy-settings.png)

4. 針對 [原則 SKU]****，選取免費或標準作為原則 SKU 的類型。 稽核原則需要標準 SKU。


## <a name="next-steps"></a>後續步驟

您將在["市場"選項卡](./cpp-marketplace-tab.md)中進一步描述您的報價和供應行銷資產。 
