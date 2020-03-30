---
title: Azure 合作夥伴和客戶使用方式歸因 |Azure 應用商店
description: 有關如何追蹤客戶 Azure Marketplace 解決方案使用狀況的概觀
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 9/23/2019
ms.author: dsindona
ms.openlocfilehash: 348633ffc91bc25a226b05743a18d2c87533a01d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280638"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure 合作夥伴客戶使用狀況屬性

身為 Azure 的軟體合作夥伴，您的解決方案需要 Azure 元件或必須直接部署在 Azure 基礎結構上。 部署合作夥伴解決方案並佈建其自有 Azure 資源的客戶會發現深入了解部署狀態以及對 Azure 成長的影響並不容易。 獲得概略的部署狀況資訊之後，您就可獲得與 Microsoft 銷售團隊相同的資訊，並獲得 Microsoft 合作夥伴計劃的信用額度。

Microsoft 現在提供一個模型，協助合作夥伴以更好的方式追蹤客戶在 Azure 上部署之軟體的 Azure 使用狀況。 這個新方法使用 Azure Resource Manager 來協調 Azure 服務的部署。

身為 Microsoft 合作夥伴，您可以將 Azure 使用狀況與您代表客戶佈建的任何 Azure 資源相關聯。 您可以透過 Azure Marketplace、快速入門存放庫、私人 GitHub 存放庫與一對一客戶支援來構成此關聯關係。 客戶使用方式歸因支援三個部署選項：

- Azure 資源管理器範本：合作夥伴可以使用資源管理器範本部署 Azure 服務來運行合作夥伴的軟體。 合作夥伴可建立 Resource Manager 範本以定義其 Azure 解決方案的基礎結構與設定。 Resource Manager 範本可讓您與您的客戶在其生命週期中部署您的解決方案。 您可以確信您的資源會以一致的狀態部署。
- Azure Resource Manager API：合作夥伴可直接呼叫 Resource Manager API，來部署 Resource Manager 範本或產生 API 呼叫以直接佈建 Azure 服務。
- Terraform：合作夥伴可以使用雲協調器（如 Terraform）來部署資源管理器範本或直接部署 Azure 服務。

客戶使用方式歸因用於新部署，不支援標記已部署的現有資源。

[Azure 應用程式](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)：解決方案範本產品 /發佈到 Azure 應用商店上，需要客戶使用方式歸因。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>使用 Resource Manager 範本
使用資源管理器範本在客戶的訂閱上部署許多合作夥伴解決方案。 如果 Azure 應用商店、GitHub 或作為快速入門有可用的資源管理器範本，則修改範本以啟用客戶使用方式歸因的過程應該是直接的。

如需有關建立及發佈「解決方案範本」的詳細資訊，請參閱

* [創建並部署第一個資源管理器範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)。
* [Azure 應用程式產品 /服務](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)。
* 視頻：[為 Azure 應用商店構建解決方案範本和託管應用程式](https://channel9.msdn.com/Events/Build/2018/BRK3603)。


## <a name="add-a-guid-to-your-template"></a>將 GUID 新增到您的範本

若要新增全域唯一識別碼 (GUID)，您只需要在主要範本檔案中進行一處修改：

1. 使用建議的方法[建立 GUID](#create-guids) 並[註冊 GUID](#register-guids-and-offers)。

1. 開啟 Resource Manager 範本。

1. 在主要範本檔案中加入新的資源。 資源只需要置於 **mainTemplate.json** 或 **azuredeploy.json** 檔案中，而非任何巢狀或連結的範本中。

1. 在 **pid-** 前置詞後方輸入 GUID 值 (例如 pid-eb7927c8-dd66-43e1-b0cf-c346a422063)。

1. 檢查範本是否有任何錯誤。

1. 在適當的存放庫中重新發佈範本。

1. [驗證範本部署中的 GUID 已成功建立](#verify-the-guid-deployment)。

### <a name="sample-resource-manager-template-code"></a>範例 Resource Manager 範本程式碼

若要啟用範本的追蹤資源，您必須在資源區段下新增下列其他資源。 在將下列範例程式碼新增至主要範本檔案時，請務必使用您自己的輸入對該範例進行修改。
資源只需要新增至 **mainTemplate.json** 或 **azuredeploy.json** 檔案，不可新增至任何巢狀或連結的範本。

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>使用 Resource Manager API

在某些情況下，您可能會想要直接對 Resource Manager REST API 進行呼叫，以部署 Azure 服務。 [Azure 支援多個 SDK](https://docs.microsoft.com/azure/?pivot=sdkstools) 以啟用此作業。 您可以使用其中一個 SDK，或直接呼叫 REST API 來部署資源。

如果您使用 Resource Manager 範本，應該依照稍早的指示標記您的解決方案。 如果您不是使用 Resource Manager 範本，並進行直接 API 呼叫，仍可標記您的部署以與 Azure 資源的使用量建立關聯。

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>使用 Resource Manager API 標記部署

要啟用客戶使用方式歸因，在設計 API 呼叫時，請在請求中的使用者代理標頭中包括 GUID。 針對每個供應項目或 SKU 新增 GUID。 使用 **pid-** 前置詞設定字串格式，並包括合作夥伴產生的 GUID。 以下是可插入到使用者代理程式中的 GUID 格式範例：

![範例 GUID 格式](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> 此字串的格式至關重要。 若未包括 **pid-** 前置詞，就無法查詢資料。 不同的 SDK 會以不同的方式追蹤。 若要實作此方法，請檢閱支援和適用於慣用 Azure SDK 的方法。

#### <a name="example-the-python-sdk"></a>範例：Python SDK

針對 Python，請使用 **config** 屬性。 您只能將屬性新增到 UserAgent。 以下是範例：

![將屬性新增到使用者代理程式](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> 為每個用戶端新增屬性。 沒有全域靜態設定。 您可以標記用戶端處理站，以確定每個用戶端都在正在追蹤。 如需詳細資訊，請參閱 [GitHub 上的此用戶端處理站範例](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)。

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>使用 Azure PowerShell 標記部署

若您透過 Azure PowerShell 部署資源，請使用下列方法附加您的 GUID：

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>使用 Azure CLI 標記部署

當您使用 Azure CLI 來附加您的 GUID 時，請設定 **AZURE_HTTP_USER_AGENT** 環境變數。 您可以在指令碼的範圍內設定此變數。 您也可以針對殼層範圍設定全域變數：

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
有關詳細資訊，請參閱[Azure SDK。](https://docs.microsoft.com/azure/go/)

## <a name="use-terraform"></a>使用地形

對 Terraform 的支援可通過 Azure 提供程式的 1.21.0[https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)版本獲得： 。  此支援適用于通過 Terraform 部署其解決方案的所有合作夥伴，以及 Azure 提供程式部署和計量的所有資源（版本 1.21.0 或更高版本）。

Terraform 的 Azure 提供程式添加了一個名為[*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id)的新可選欄位，該欄位是指定用於解決方案的跟蹤 GUID 的位置。 此欄位的值也可以從*ARM_PARTNER_ID*環境變數中獲取。

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
希望通過 Terraform 通過客戶使用方式歸因跟蹤其部署的合作夥伴需要執行以下操作：

* 創建 GUID（應為每個優惠或 SKU 添加 GUID）
* 更新其 Azure 提供程式以將*partner_id*的值設置為 GUID（不要用"pid-"預先修復 GUID，只需將其設置為實際 GUID）

## <a name="create-guids"></a>建立 GUID

GUID 是具有 32 個十六進位數字的參考號碼。 若要建立 GUID 來進行追蹤，您應該使用 GUID 產生器。 Azure 儲存體小組已建立 [GUID 產生器表單](https://aka.ms/StoragePartners)，其會透過電子郵件傳送正確格式的 GUID 給您，且可跨不同追蹤系統重複使用。

> [!Note]
> 強烈建議您使用[Azure 存儲的 GUID 產生器表單](https://aka.ms/StoragePartners)來創建 GUID。 如需詳細資訊，請參閱[常見問題集](#faq)。

建議您為每個產品的每個供應項目與散發通道建立唯一的 GUID。 如果您不想要分割報告，您可以讓產品的多個散發通道使用單一 GUID。

若您使用範本部署產品，且該產品在 Azure Marketplace 與 GitHub 上都有提供，您必須建立並註冊 2 個相異的 GUID：

*   Azure Marketplace 中的產品 A
*   GitHub 上的產品 A

報告是由合作夥伴值 (Microsoft 合作夥伴 ID) 與 GUID 所完成。

您也能以更細微的層級來追蹤 GUID (例如，供應項目的變體 SKU)。

## <a name="register-guids-and-offers"></a>註冊 GUID 與供應項目

必須註冊 GUID 才能啟用客戶使用方式歸因。

範本 GUID 的所有註冊都在合作夥伴中心內完成。

將 GUID 添加到範本或使用者代理中，並在合作夥伴中心註冊 GUID 後，將跟蹤所有部署。

1. 註冊為[商業市場出版商](https://aka.ms/JoinMarketplace)。

   * 合作夥伴必須在[合作夥伴中心中擁有設定檔](https://docs.microsoft.com/azure/marketplace/become-publisher)。 我們鼓勵您在 Azure Marketplace 或 AppSource 中列出該供應項目。
   * 合作夥伴可以註冊多個 GUID。
   * 合作夥伴可以註冊為非 Marketplace 解決方案範本與供應項目註冊 GUID。

1. 登錄到[合作夥伴中心](https://partner.microsoft.com/dashboard)。

1. 在右上角，選擇設置齒輪圖示，然後選擇 **"開發人員"設置**。

1. 在 **"帳戶設置"頁上**，選擇 **"添加跟蹤 GUID"。**

1. 在**GUID**框中，輸入您的跟蹤 GUID。 只輸入 GUID，不要輸入 **pid-** 前置詞。 在 **"描述"** 框中，輸入您的產品/服務名稱或說明。

1. 若要註冊多個 GUID，請再次選取 [新增追蹤 GUID]****。 頁面上會出現額外的方塊。

1. 選取 [儲存]****。


## <a name="verify-the-guid-deployment"></a>驗證 GUID 部署

在您修改範本並執行測試部署之後，您可以使用下列 PowerShell 指令碼來擷取您已部署並標記的資源。

您可以使用指令碼來確認 GUID 是否已成功加入至您的 Resource Manager 範本。 該腳本不適用於資源管理器 API 或 Terraform 部署。

登入 Azure。 選取具有您想要在執行指令碼之前驗證之部署的訂用帳戶。 在部署的訂用帳戶內容中執行指令碼。

部署的 **GUID** 與 **resourceGroup** 名稱是必要參數。

您可以在 GitHub 上取得[原始指令碼](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1)。

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Report

您可以在合作夥伴中心分析儀表板中找到客戶使用方式歸因的報告。 ([https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)). 要查看報表，您必須使用合作夥伴中心憑據才能登錄。 如果遇到報告或登錄的任何問題，請按照"獲取支援"部分中的說明創建支援請求。

在合作夥伴關聯類型下拉清單中選擇"跟蹤範本"以查看報表。

![客戶使用方式歸因報告](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>通知您的客戶

合作夥伴應向客戶通報使用客戶使用方式歸因的部署。 Microsoft 會向合作夥伴回報與這些部署關聯的 Azure 使用狀況。 下列範例包括您可以用來通知客戶有關這些部署的內容。 在範例中，將 \<PARTNER> 取代為您的公司名稱。 合作夥伴應該確保通知符合他們的資料隱私權和收集原則，包括讓客戶從追蹤排除的選項。

### <a name="notification-for-resource-manager-template-deployments"></a>Resource Manager 範本部署的通知

當您部署此範本時，Microsoft 可以透過已部署的 Azure 資源來識別 \<PARTNER> 軟體的安裝。 Microsoft 可讓用來支援軟體的 Azure 資源相互關聯。 Microsoft 會收集這項資訊，以透過其產品提供最佳體驗以及經營業務。 Microsoft 隱私權原則 (位於 https://www.microsoft.com/trustcenter) 規範此資料的收集與控管。

### <a name="notification-for-sdk-or-api-deployments"></a>SDK 或 API 部署的通知

當您部署 \<PARTNER> 軟體時，Microsoft 可以透過已部署的 Azure 資源來識別 \<PARTNER> 軟體的安裝。 Microsoft 可讓用來支援軟體的 Azure 資源相互關聯。 Microsoft 會收集這項資訊，以透過其產品提供最佳體驗以及經營業務。 Microsoft 隱私權原則 (位於 https://www.microsoft.com/trustcenter) 規範此資料的收集與控管。

## <a name="get-support"></a>取得支援

有兩個支援管道，具體取決於您面臨的問題。

如果您在合作夥伴中心遇到任何問題（如查看客戶使用方式歸因報告或登錄），請在此處與合作夥伴中心支援小組創建支援請求：[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

如果您需要有關應用商店入職和/或客戶使用一般歸因的説明，例如如何設置客戶使用方式歸因，請按照以下步驟操作：

1. 移至[支援連結](https://go.microsoft.com/fwlink/?linkid=844975)。

1. 在 [問題類型]**** 下，選取 [Marketplace 上線]****。

1. 針對您的問題選擇 [類別]****：

   - 針對使用狀況關聯問題，請選取 [其他]****。
   - 針對 Azure Marketplace CPP 存取問題，請選取 [存取問題]****。

     ![選擇問題類別](media/marketplace-publishers-guide/lu-article-incident.png)

1. 選取 [提出要求]****。

1. 在下一頁，輸入必要值。 選取 **[繼續]**。

1. 在下一頁，輸入必要值。

   > [!Important]
   > 在 [事件標題]**** 方塊中，輸入 **ISV 使用狀況追蹤**。 請詳細描述您的問題。

   ![輸入事件標題的 ISV 使用狀況追蹤](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. 完成表單，然後選取 [提交]****。

您還可以從 Microsoft 合作夥伴技術顧問那裡獲得技術銷售前、部署和應用開發方案的技術指導，以瞭解並納入客戶使用方式歸因。

### <a name="how-to-submit-a-technical-consultation-request"></a>如何提交技術諮詢請求

1. 訪問[https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney).
1. 選擇雲基礎架構和管理，將打開一個新頁面，供您查看技術之旅。
1. 在"部署服務"下，按一下"提交請求"按鈕
1. 使用您的 MSA （MPN 帳戶） 或 AAD（合作夥伴儀表板帳戶）登錄;根據您的登錄憑據，將打開連線請求表單：
    * 填寫/查看聯繫資訊。
    * 諮詢詳細資訊可以預先填充或從下拉清單中選擇。
    * 輸入標題和問題描述（提供盡可能多的詳細資訊）。
1. 按一下 [提交]

查看分步說明，並在 上[https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions)使用螢幕截圖。

### <a name="whats-next"></a>後續步驟

Microsoft 合作夥伴技術顧問將與您聯繫，以設置呼叫範圍，以滿足您的需求範圍。

## <a name="faq"></a>常見問題集

**將 GUID 加入至範本的優點為何？**

Microsoft 為合作夥伴提供客戶部署其解決方案的視圖，以及對其影響使用方式的見解。 Microsoft 與合作夥伴也可以使用此資訊來加強與銷售團隊之間的合作關係。 Microsoft 與合作夥伴可以使用該資料來取得個別合作夥伴對 Azure 成長的更一致檢視。

**加入 GUID 之後能否予以變更？**

是，客戶或實作合作夥伴可自訂範本並能變更或移除該 GUID。 我們建議合作夥伴主動向客戶和合作夥伴描述資源和 GUID 的作用，以防止刪除或編輯 GUID。 變更 GUID 只會影響新的部署和資源，現有的部署和資源不受影響。

**我可以從類似 GitHub 的非 Microsoft 存放庫追蹤部署的範本嗎？**

是，只要有 GUID，當範本部署時，都會追蹤使用量。 合作夥伴必須在 CPP 中具有設定檔，以註冊用於在 Azure 應用商店外部部署的 GUID。

**客戶也會收到報告嗎？**

客戶可以在 Azure 入口網站內，追蹤其個別資源或客戶所定義資源群組的使用情況。

**此方法是否類似于記錄的數位合作夥伴 （DPOR）？**

這種將部署與使用狀況連結至合作夥伴解決方案的新方法，提供將合作夥伴解決方案連結到 Azure 使用量的機制。 DPOR 可將諮詢 (系統整合者) 或管理 (受控服務提供者) 合作夥伴與客戶的 Azure 訂用帳戶建立關聯。

**使用 Azure 儲存體 GUID 產生器表單的好處是什麼？**

Azure 儲存體的 GUID 產生器表單保證會產生所需格式的 GUID。 此外，如果您是使用任何 Azure 儲存體的資料平面追蹤方法，可以利用相同的 GUID 來追蹤 Marketplace 控制平面。 這可讓您運用夥伴屬性的單一整合 GUID，而不需要維護個別的 GUID。

**是否可以在 Azure 應用商店中使用私有自訂 VHD 作為解決方案範本產品？**

不，你不能。 虛擬機器映射必須來自 Azure 應用商店，請參閱： [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)。

您可以使用自訂 VHD 在市場中創建 VM 產品/服務，並將其標記為私有，以便任何人都看不到它。 然後在解決方案範本中引用此 VM。

**無法更新主範本*的內容版本*屬性？**

在某些情況下，使用來自另一個範本的 TemplateLink 部署範本時，可能是一個 Bug，該範本由於某種原因預期舊內容版本。 解決方法是使用中繼資料屬性：

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
