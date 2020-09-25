---
title: 商業 marketplace 夥伴和客戶使用方式屬性
description: 概略了解如何追蹤 Azure Marketplace 解決方案的客戶使用狀況。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vikrambmsft
ms.author: vikramb
ms.date: 09/01/2020
ms.custom: devx-track-terraform
ms.openlocfilehash: 72d8cb0ef80210cca1dbf1d2726590c0cb8540e5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261374"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>商業 marketplace 夥伴和客戶使用方式屬性

客戶使用狀況歸因可以將客戶訂用帳戶中執行 (且部署至您的解決方案中執行) 的 Azure 資源，與身為合作夥伴的您建立關聯。 在 Microsoft 內部系統中產生這些關聯，有助於更深入了解哪些 Azure 資源執行您的軟體。 當您採用這項追蹤功能時，您會與 Microsoft 銷售團隊密切合作，並獲得 Microsoft 合作夥伴計畫的信用額度。

您可以透過 Azure Marketplace、快速入門存放庫、私人 GitHub 存放庫，以及會建立持久 IP 的 1:1 客戶參與 (例如開發應用程式)，以產生此關聯。

客戶使用狀況歸因支援三種部署選項：

- Azure Resource Manager 範本：合作夥伴可以使用 Resource Manager 範本來部署 Azure 服務，以執行合作夥伴的軟體。 合作夥伴可建立 Resource Manager 範本以定義其 Azure 解決方案的基礎結構與設定。 Resource Manager 範本可讓您與您的客戶在其生命週期中部署您的解決方案。 您可以確信您的資源會以一致的狀態部署。
- Azure Resource Manager API：合作夥伴可直接呼叫 Resource Manager API，來部署 Resource Manager 範本或產生 API 呼叫以直接佈建 Azure 服務。
- Terraform：合作夥伴可以使用 Terraform 來部署 Resource Manager 範本，或直接部署 Azure 服務。

>[!IMPORTANT]
>- 客戶使用狀況歸因所要追蹤的行為，並不包括系統整合者、受控服務提供者，或為了部署和管理 Azure 上執行的軟體而設計的工具。
>
>- 客戶使用狀況歸因適用於新的部署，「不」支援標記已部署的現有資源。
>
>- 發佈至 Azure Marketplace 的 [Azure 應用程式](./partner-center-portal/create-new-azure-apps-offer.md)供應項目需要客戶使用狀況歸因。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>建立 GUID

GUID 是具有 32 個十六進位數字的唯一參考識別碼。 若要建立 GUID 來進行追蹤，您應該使用 GUID 產生器。 Azure 儲存體小組已建立 [GUID 產生器表單](https://aka.ms/StoragePartners)，其會透過電子郵件傳送正確格式的 GUID 給您，且可跨不同追蹤系統重複使用。

> [!NOTE]
> 強烈建議您使用 [Azure 儲存體的 GUID 產生器表單](https://aka.ms/StoragePartners)來建立 GUID。 如需詳細資訊，請參閱[常見問題集](#faq)。

建議您為每個產品的每個供應項目與散發通道建立唯一的 GUID。 如果您不想要分割報告，您可以讓產品的多個散發通道使用單一 GUID。

如果您使用範本來部署產品，而且可以在 Azure Marketplace 和 GitHub 上使用，您可以建立並註冊兩個不同的 GUID：

- Azure Marketplace 中的產品 A
- GitHub 上的產品 A

報告是依 Microsoft 合作夥伴網路識別碼和 GUID 產生。

您也可以註冊額外的 GUID，並在方案之間變更 GUID，以更詳細追蹤使用狀況，所謂的方案是指供應項目的不同版本。

## <a name="register-guids"></a>註冊 GUID

必須在合作夥伴中心註冊 GUID，才能啟用客戶使用狀況歸因。

將 GUID 新增至範本或使用者代理程式，並在合作夥伴中心註冊 GUID 之後，就會追蹤未來的部署。

> [!NOTE]
> 如果您要透過合作夥伴中心將 [Azure 應用程式](./partner-center-portal/create-new-azure-apps-offer.md) 供應專案發佈到 Azure Marketplace，在範本內使用的任何新 GUID 都會在上傳範本時自動註冊到您的合作夥伴中心設定檔。  

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard)。

1. 註冊為[商業市集發行者](https://aka.ms/JoinMarketplace)。

   * 合作夥伴[在合作夥伴中心需要有設定檔](become-publisher.md)。 我們鼓勵您在 Azure Marketplace 或 AppSource 中列出該供應項目。
   * 合作夥伴可以註冊多個 GUID。
   * 合作夥伴可以註冊非市集解決方案範本和供應項目的 GUID。

1. 在右上角，選取 [設定] 齒輪圖示，然後選取 [開發人員設定]。

1. 在 [帳戶設定] 頁面上，選取 [新增追蹤 GUID]。

1. 在 [GUID] 方塊中，輸入追蹤 GUID。 只輸入不含前置詞的 GUID `pid-` 。 在 [描述] 方塊中，輸入供應項目名稱或描述。

1. 若要註冊多個 GUID，請再次選取 [新增追蹤 GUID]。 頁面上會出現額外的方塊。

1. 選取 [儲存]。

## <a name="use-resource-manager-templates"></a>使用 Resource Manager 範本
許多合作夥伴解決方案都是使用 Azure Resource Manager 範本來部署。 如果您有 Azure Marketplace、GitHub 上或作為快速入門提供的 Resource Manager 範本，修改範本以啟用客戶使用方式屬性的程式相當簡單。

> [!NOTE]
> 如需有關建立及發佈「解決方案範本」的詳細資訊，請參閱
> * [建立及部署第一個 Resource Manager 範本](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)。
>* [Azure 應用程式供應項目](./partner-center-portal/create-new-azure-apps-offer.md)。
>* 影片： [為 Azure Marketplace 建立解決方案範本和受控應用程式](https://channel9.msdn.com/Events/Build/2018/BRK3603)。


若要新增全域唯一識別碼 (GUID)，您只需要在主要範本檔案中進行一處修改：

1. 使用建議的方法[建立 GUID](#create-guids) 並[註冊 GUID](#register-guids)。

1. 開啟 Resource Manager 範本。

1. 在主要範本檔案中，新增類型為 [Microsoft .resources/部署](https://docs.microsoft.com/azure/templates/microsoft.resources/deployments) 的新資源。 資源只需要置於 **mainTemplate.json** 或 **azuredeploy.json** 檔案中，而非任何巢狀或連結的範本中。

1. 輸入 `pid-` 前置詞作為資源名稱之後的 GUID 值。 例如，如果 GUID 是 eb7927c8-dd66-43e1-b0cf-c346a422063-eb7927c8-dd66-43e1-b0cf-c346a422063-eb7927c8-dd66-43e1-b0cf-c346a422063-eb7927c8-dd66-43e1-b0cf-c346a422063-pid-eb7927c8-dd66-43e1-b0cf-c346a422063，則資源名稱將會是 _pid-eb7927c8-dd66-43e1-b0cf-c346a422063-eb7927c8-dd66-43e1-b0cf-c346a422063_-eb7927c8-dd66-43e1-b0cf-c346a422063-eb7927c8-dd66-43e1-b0cf-c346a422063-pid-eb7927c8-dd66-43e1-b0cf-c346a422063。

1. 檢查範本是否有任何錯誤。

1. 在適當的存放庫中重新發佈範本。

1. [驗證範本部署中的 GUID 已成功建立](#verify-the-guid-deployment)。

### <a name="sample-resource-manager-template-code"></a>範例 Resource Manager 範本程式碼

若要啟用範本的追蹤資源，您必須在資源區段下新增下列其他資源。 在將下列範例程式碼新增至主要範本檔案時，請務必使用您自己的輸入對該範例進行修改。
資源只需要新增至 **mainTemplate.json** 或 **azuredeploy.json** 檔案，不可新增至任何巢狀或連結的範本。

```json
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2020-06-01",
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

若要啟用客戶使用狀況歸因，當您設計 API 呼叫時，請在要求中的使用者代理程式標頭中包含 GUID。 針對每個供應項目或 SKU 新增 GUID。 使用前置詞格式化字串 `pid-` ，並包含夥伴產生的 GUID。 以下是可插入到使用者代理程式中的 GUID 格式範例：

![範例 GUID 格式](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> 此字串的格式至關重要。 如果 `pid-` 未包含前置詞，則無法查詢資料。 不同的 SDK 會以不同的方式追蹤。 若要實作此方法，請檢閱支援和適用於慣用 Azure SDK 的方法。

#### <a name="example-the-python-sdk"></a>範例：Python SDK

針對 Python，請使用 **config** 屬性。 您只能將屬性新增到 UserAgent。 以下是範例：

![將屬性新增到使用者代理程式](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> 為每個用戶端新增屬性。 沒有全域靜態設定。 您可以標記用戶端處理站，以確定每個用戶端都在正在追蹤。 如需詳細資訊，請參閱 [GitHub 上的此用戶端處理站範例](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)。

#### <a name="example-the-net-sdk"></a>範例： .NET SDK

針對 .NET，請務必設定使用者代理程式。 您可以使用 c # ) [中的下列](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.fluent?view=azure-dotnet) 程式碼 (範例來設定使用者代理程式：

```csharp

var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

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
如需詳細資訊，請參閱 [Azure SDK for Go](https://docs.microsoft.com/azure/developer/go/)。

## <a name="use-terraform"></a>使用 Terraform

您可以透過 Azure 提供者的 1.21.0 版取得 Terraform 的支援：[https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)。  這項支援適用於透過 Terraform 來部署解決方案的所有夥伴，以及由 Azure Provider (1.21.0 或更新版本) 部署和計量的所有資源。

Azure provider for Terraform 新增名為 [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) 的選擇性欄位，可讓您指定用於解決方案的追蹤 GUID。 此欄位的值也可以來自 *ARM_PARTNER_ID* 環境變數。

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
如果合作夥伴想要透過 Terrafrom，取得由客戶使用狀況歸因所追蹤的部署，則需要執行下列動作：

* 建立 GUID (應該為每個供應項目或 SKU 新增 GUID)
* 更新其 Azure Provider，將 *partner_id* 的值設定為 GUID (GUID 開頭「不要」加上 "pid-"，設定為實際 GUID 即可)


## <a name="verify-the-guid-deployment"></a>驗證 GUID 部署

在您修改範本並執行測試部署之後，您可以使用下列 PowerShell 指令碼來擷取您已部署並標記的資源。

您可以使用指令碼來確認 GUID 是否已成功加入至您的 Resource Manager 範本。 此指令碼不適用於 Resource Manager API 或 Terraform 部署。

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

您可以在合作夥伴中心儀表板 ([https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)) ，找到客戶使用狀況歸因的報告。 若要查看報告，您必須使用合作夥伴中心認證來登入。 如果您在報告或登入方面遇到任何問題，請遵循〈取得支援〉一節的指示來建立支援要求。

在 [合作夥伴關聯類型] 下拉式清單中，選擇 [追蹤的範本] 來查看報告。

![客戶使用狀況歸因的報告](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>通知您的客戶

如果有部署採用客戶使用狀況歸因，合作夥伴應該告知客戶。 Microsoft 會向合作夥伴回報與這些部署關聯的 Azure 使用狀況。 下列範例包括您可以用來通知客戶有關這些部署的內容。 在範例中，將取代 \<PARTNER> 為您的公司名稱。 合作夥伴應該確保通知符合他們的資料隱私權和收集原則，包括讓客戶從追蹤排除的選項。

### <a name="notification-for-resource-manager-template-deployments"></a>Resource Manager 範本部署的通知

當您部署此範本時，Microsoft 可以透過 \<PARTNER> 已部署的 Azure 資源來識別軟體的安裝。 Microsoft 可讓用來支援軟體的 Azure 資源相互關聯。 Microsoft 會收集這項資訊，以透過其產品提供最佳體驗以及經營業務。 Microsoft 隱私權原則 (位於 https://www.microsoft.com/trustcenter ) 規範此資料的收集與控管。

### <a name="notification-for-sdk-or-api-deployments"></a>SDK 或 API 部署的通知

當您部署 \<PARTNER> 軟體時，Microsoft 可以透過 \<PARTNER> 已部署的 Azure 資源來識別軟體的安裝。 Microsoft 可讓用來支援軟體的 Azure 資源相互關聯。 Microsoft 會收集這項資訊，以透過其產品提供最佳體驗以及經營業務。 Microsoft 隱私權原則 (位於 https://www.microsoft.com/trustcenter ) 規範此資料的收集與控管。

## <a name="get-support"></a>取得支援

在 [合作夥伴中心中瞭解商用 marketplace 方案的支援](./partner-center-portal/support.md)選項。

### <a name="how-to-submit-a-technical-consultation-request"></a>如何提交技術諮詢要求

1. 請造訪[合作夥伴技術服務](https://aka.ms/TechnicalJourney)。
1. 選取 [雲端基礎結構和管理]，隨即會開啟新的頁面讓您檢視技術旅程圖。
1. 在 [部署服務] 下，按一下 [提交要求] 按鈕
1. 使用 MSA (MPN 帳戶) 或 AAD (夥伴儀表板帳戶) 登入；根據您的登入認證，將會開啟線上要求表單：
    * 完成/檢閱連絡人資訊。
    * 可能已預先填入諮詢詳細資料，否則請從下拉式清單中選取。
    * 輸入問題的標題和描述 (越詳細越好)。
1. 按一下 [提交]

在[使用技術售前和部署服務](https://aka.ms/TechConsultInstructions)，上，透過螢幕擷取畫面來檢視逐步指示。

### <a name="whats-next"></a>後續步驟

Microsoft 合作夥伴技術顧問會來電聯絡您，以設定您的需求範圍。

## <a name="faq"></a>常見問題集

**將 GUID 加入至範本的優點為何？**

Microsoft 可讓合作夥伴看到客戶部署解決方案的情形，並深入了解受影響的使用狀況。 Microsoft 與合作夥伴也可以使用此資訊來加強與銷售團隊之間的合作關係。 Microsoft 與合作夥伴可以使用該資料來取得個別合作夥伴對 Azure 成長的更一致檢視。

**加入 GUID 之後能否予以變更？**

是，客戶或實作合作夥伴可自訂範本並能變更或移除該 GUID。 建議合作夥伴主動向客戶及合作夥伴描述資源和 GUID 的作用，以避免移除或編輯 GUID。 變更 GUID 只會影響新的部署和資源，現有的部署和資源不受影響。

**我可以從類似 GitHub 的非 Microsoft 存放庫追蹤部署的範本嗎？**

是，只要有 GUID，當範本部署時，都會追蹤使用量。 合作夥伴仍然必須註冊其 GUID。

**客戶也會收到報告嗎？**

客戶可以在 Azure 入口網站內，追蹤其個別資源或客戶所定義資源群組的使用情況。 客戶不會看到依 GUID 細分的使用狀況。

**此方法和列冊數位合作夥伴 (DPOR) 類似嗎？**

這種將部署與使用狀況連結至合作夥伴解決方案的新方法，提供將合作夥伴解決方案連結到 Azure 使用量的機制。 DPOR 可將諮詢 (系統整合者) 或管理 (受控服務提供者) 合作夥伴與客戶的 Azure 訂用帳戶建立關聯。

**使用 Azure 儲存體 GUID 產生器表單的好處是什麼？**

Azure 儲存體的 GUID 產生器表單保證會產生所需格式的 GUID。 此外，如果您是使用任何 Azure 儲存體的資料平面追蹤方法，可以利用相同的 GUID 來追蹤 Marketplace 控制平面。 這可讓您運用夥伴屬性的單一整合 GUID，而不需要維護個別的 GUID。

**對於 Azure Marketplace 中的解決方案範本供應項目，我可以使用私人自訂的 VHD 嗎？**

不可以。 虛擬機器映射必須來自 Azure Marketplace，請參閱： [Azure Marketplace 上的虛擬機器供應專案發佈指南](marketplace-virtual-machines.md)。

您可以在市集使用自訂 VHD 建立 VM 供應項目，並標示為「私人」，不讓任何人看到。 然後，在您的解決方案範本中參考此 VM。

**無法更新主要範本的 *contentVersion* 屬性嗎？**

這可能是 bug，在部署範本時，使用的 TemplateLink 來自另一個因為某些原因而預期較舊 contentVersion 的範本。 因應措施是使用 metadata 屬性：

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
