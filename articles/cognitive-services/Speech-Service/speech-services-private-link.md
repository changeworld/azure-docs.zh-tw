---
title: 如何搭配語音服務使用私人端點
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用語音服務搭配 Azure Private Link 所提供的私人端點
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: alexeyo
ms.openlocfilehash: 61be4b45df94c902c0473b94a6dd83237c72da3c
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98196109"
---
# <a name="use-speech-services-through-a-private-endpoint"></a>透過私人端點使用語音服務

[Azure Private Link](../../private-link/private-link-overview.md) 可讓您使用 [私人端點](../../private-link/private-endpoint-overview.md)連接至 Azure 中的服務。 私人端點是只能在特定 [虛擬網路](../../virtual-network/virtual-networks-overview.md) 和子網中存取的私人 IP 位址。

本文說明如何使用 Azure 認知服務中的語音服務來設定和使用 Private Link 和私人端點。

> [!NOTE]
> 繼續進行之前，請先參閱 [如何搭配使用虛擬網路與認知服務](../cognitive-services-virtual-networks.md)。

本文也會說明 [如何在稍後移除私人端點，但仍使用語音資源](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints)。

## <a name="create-a-custom-domain-name"></a>建立自訂功能變數名稱

私人端點需要 [認知服務的自訂子功能變數名稱稱](../cognitive-services-custom-subdomains.md)。 使用下列指示，為您的語音資源建立一個。

> [!WARNING]
> 啟用自訂功能變數名稱的語音資源會使用不同的方式來與語音服務互動。 您可能必須針對這兩種案例調整您的應用程式程式碼：啟用 [私人端點](#use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled) 且 [*未* 啟用私人端點](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints)。
>
> 當您啟用自訂功能變數名稱時，此作業 [無法](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)復原。 返回 [區功能變數名稱稱](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) 的唯一方法是建立新的語音資源。
>
> 如果您的語音資源具有許多相關聯的自訂模型和透過 [語音 Studio](https://speech.microsoft.com/)建立的專案，強烈建議您先嘗試使用測試資源進行設定，再修改生產環境中使用的資源。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要使用 Azure 入口網站建立自訂功能變數名稱，請依照下列步驟執行：

1. 前往 [Azure 入口網站](https://portal.azure.com/)並登入 Azure 帳戶。
1. 選取所需的語音資源。
1. 在左窗格的 [ **資源管理** ] 群組中，選取 [ **網路**]。
1. 在 [ **防火牆與虛擬網路** ] 索引標籤上，選取 [ **產生自訂功能變數名稱**]。 新的右面板隨即出現，並顯示為您的資源建立唯一自訂子域的指示。
1. 在 [ **產生自訂功能變數名稱** ] 面板中，輸入自訂功能變數名稱。 您的完整自訂網域看起來會像這樣： `https://{your custom name}.cognitiveservices.azure.com` 。 
    
    請記住，在建立自訂功能變數名稱之後，就 _無法_ 變更它。
    
    輸入自訂功能變數名稱之後，請選取 [ **儲存**]。
1. 作業完成後，在 [ **資源管理** ] 群組中，選取 [ **金鑰和端點**]。 確認資源的新端點名稱以這種方式啟動： `https://{your custom name}.cognitiveservices.azure.com` 。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 建立自訂功能變數名稱，請確認您的電腦具有 PowerShell 7.x 版或更新版本（含 Azure PowerShell 模組版本5.1.0 或更新版本）。 若要查看這些工具的版本，請遵循下列步驟：

1. 在 PowerShell 視窗中，輸入：

    `$PSVersionTable`

    確認 `PSVersion` 值為7.x 或更新版本。 若要升級 PowerShell，請遵循 [安裝各種版本 powershell](/powershell/scripting/install/installing-powershell)的指示。

1. 在 PowerShell 視窗中，輸入：

    `Get-Module -ListAvailable Az`

    如果沒有出現任何內容，或是該版本的 Azure PowerShell 模組早于5.1.0，請依照 [安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps) 的指示進行升級。

繼續之前，請執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="verify-that-a-custom-domain-name-is-available"></a>確認有可用的自訂功能變數名稱

檢查您要使用的自訂網域是否可用。 下列程式碼會使用認知服務 REST API 中的 [ [檢查網域可用性](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) ] 作業，確認網域可供使用。

> [!TIP]
> 下列程式碼在 Azure Cloud Shell 中將 *無法* 運作。

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare the OAuth token to use in the request to the Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to the Cognitive Services REST API.
$uri = "https://management.azure.com/subscriptions/" + $subId + `
    "/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18"
$body = @{
subdomainName = $subdomainName
type = "Microsoft.CognitiveServices/accounts"
}
$jsonBody = $body | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $uri -ContentType "application/json" -Authentication Bearer `
    -Token $token -Body $jsonBody | Format-List
```
如果所需的名稱可供使用，您會看到如下所示的回應：
```azurepowershell
isSubdomainAvailable : True
reason               :
type                 :
subdomainName        : my-custom-name
```
如果名稱已被採用，您將會看到下列回應：
```azurepowershell
isSubdomainAvailable : False
reason               : Sub domain name 'my-custom-name' is already used. Please pick a different name.
type                 :
subdomainName        : my-custom-name
```
## <a name="create-your-custom-domain-name"></a>建立您的自訂功能變數名稱

若要為選取的語音資源啟用自訂功能變數名稱，請使用 [AzCognitiveServicesAccount](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) Cmdlet。

> [!WARNING]
> 在下列程式碼順利執行之後，您將會建立語音資源的自訂功能變數名稱。 請記住，您 *無法* 變更此名稱。

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains the Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# WARNING: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

本節需要 Azure CLI 的最新版本。 如果您使用 Azure Cloud Shell，則已安裝最新版本。

## <a name="verify-that-the-custom-domain-name-is-available"></a>確認自訂功能變數名稱可用

檢查您要使用的自訂網域是否為免費。 使用認知服務 REST API 中的 [ [檢查網域可用性](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) ] 方法。

複製下列程式碼區塊、插入您慣用的自訂功能變數名稱，並儲存至檔案 `subdomain.json` 。

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

將檔案複製到您目前的資料夾，或將它上傳至 Azure Cloud Shell，然後執行下列命令。 使用您的 Azure 訂用帳戶識別碼來取代 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 。

```azurecli-interactive
az rest --method post --url "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.CognitiveServices/checkDomainAvailability?api-version=2017-04-18" --body @subdomain.json
```
如果所需的名稱可供使用，您會看到如下所示的回應：
```azurecli
{
  "isSubdomainAvailable": true,
  "reason": null,
  "subdomainName": "my-custom-name",
  "type": null
}
```

如果名稱已被採用，您將會看到下列回應：
```azurecli
{
  "isSubdomainAvailable": false,
  "reason": "Sub domain name 'my-custom-name' is already used. Please pick a different name.",
  "subdomainName": "my-custom-name",
  "type": null
}
```
## <a name="enable-a-custom-domain-name"></a>啟用自訂功能變數名稱

若要為選取的語音資源啟用自訂功能變數名稱，請使用 [az cognitiveservices account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) 命令。

選取包含語音資源的 Azure 訂用帳戶。 如果您的 Azure 帳戶只有一個使用中的訂用帳戶，您可以略過此步驟。 使用您的 Azure 訂用帳戶識別碼來取代 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 。
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
將自訂功能變數名稱設定為選取的資源。 將範例參數值取代為實際的參數值，然後執行下列命令。

> [!WARNING]
> 成功執行下列命令之後，您將會建立語音資源的自訂功能變數名稱。 請記住，您 *無法* 變更此名稱。

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

***

## <a name="enable-private-endpoints"></a>啟用私人端點

我們建議使用附加至虛擬網路的 [私人 DNS 區域](../../dns/private-dns-overview.md) ，並搭配私人端點的必要更新。 在布建過程中，您預設會建立私人 DNS 區域。 如果您是使用自己的 DNS 伺服器，您可能也需要變更您的 DNS 設定。 

在布建生產語音資源的私人端點之前，請 *先* 決定 DNS 策略。 並測試您的 DNS 變更，尤其是當您使用自己的 DNS 伺服器時。

您可以使用下列其中一篇文章來建立私人端點。 這些文章會使用 web 應用程式做為啟用私人端點的範例資源。

- [使用 Azure 入口網站建立私人端點](../../private-link/create-private-endpoint-portal.md)
- [使用 Azure PowerShell 建立私人端點](../../private-link/create-private-endpoint-powershell.md)
- [使用 Azure CLI 建立私人端點](../../private-link/create-private-endpoint-cli.md)

使用這些參數，而不是您所選擇之文章中的參數：

| 設定             | 值                                    |
|---------------------|------------------------------------------|
| 資源類型       | **CognitiveServices/帳戶** |
| 資源            | **\<your-speech-resource-name>**         |
| 目標子資源 | **account**                              |

**私人端點的 DNS：** 請參閱 [認知服務資源中私人端點 DNS](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints)的一般原則。 然後藉由執行下列各節所述的檢查，確認您的 DNS 設定是否正常運作。

### <a name="resolve-dns-from-the-virtual-network"></a>從虛擬網路解析 DNS

這是 *必要* 的檢查。

請遵循下列步驟，從您的虛擬網路測試自訂 DNS 專案：

1. 登入位於您已連接私人端點之虛擬網路中的虛擬機器。 
1. 開啟 Windows 命令提示字元或 Bash shell，執行 `nslookup` ，並確認它已成功解析您資源的自訂功能變數名稱。

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  168.63.129.16

   Non-authoritative answer:
   Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
   Address:  172.28.0.10
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
   ```

1. 確認 IP 位址符合您私人端點的 IP 位址。

### <a name="resolve-dns-from-other-networks"></a>從其他網路解析 DNS

只有當您已在資源的 [**網路**] 區段中啟用 [**所有網路**] 選項或 [**選取的網路] 和 [私人端點**] 存取選項時，才執行這項檢查。 

如果您打算只使用私人端點來存取資源，您可以略過本節。

1. 登入連接到允許存取資源之網路的電腦。
2. 開啟 Windows 命令提示字元或 Bash shell，執行 `nslookup` ，並確認它已成功解析您資源的自訂功能變數名稱。

   ```dos
   C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
   Server:  UnKnown
   Address:  fe80::1

   Non-authoritative answer:
   Name:    vnetproxyv1-weu-prod.westeurope.cloudapp.azure.com
   Address:  13.69.67.71
   Aliases:  my-private-link-speech.cognitiveservices.azure.com
             my-private-link-speech.privatelink.cognitiveservices.azure.com
             westeurope.prod.vnet.cog.trafficmanager.net
   ```

3. 確認 IP 位址符合您私人端點的 IP 位址。

> [!NOTE]
> 已解析的 IP 位址指向虛擬網路 proxy 端點，其會將網路流量分派至認知服務資源的私人端點。 對於具有自訂功能變數名稱但 *沒有* 私用端點的資源，其行為會有所不同。 如需詳細資訊，請參閱 [這一節](#dns-configuration) 。

## <a name="adjust-existing-applications-and-solutions"></a>調整現有的應用程式和解決方案

啟用自訂網域的語音資源會使用不同的方式來與語音服務互動。 這適用于具有和沒有私用端點的已啟用網域的自訂網域語音資源。 本節中的資訊適用于這兩種案例。

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-a-private-endpoint-enabled"></a>使用已啟用自訂功能變數名稱和私人端點的語音資源

啟用自訂功能變數名稱和私人端點的語音資源，會使用不同的方式來與語音服務互動。 本節說明如何搭配使用這類資源與語音服務 REST Api 和 [語音 SDK](speech-sdk.md)。

> [!NOTE]
> 沒有私人端點但啟用自訂功能變數名稱的語音資源，也有與語音服務互動的特殊方法。 這種方式與啟用私人端點的語音資源案例不同。 如果您有這類資源 (例如，您有一個具有私人端點的資源，但之後決定移除它們) ，請參閱「 [使用具有自訂功能變數名稱的語音資源，但不含私人端點](#use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints)」一節。

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-rest-apis"></a>具有自訂功能變數名稱和私人端點的語音資源：使用 REST Api

在本節中，我們將使用 `my-private-link-speech.cognitiveservices.azure.com` (自訂網域) 範例語音資源 DNS 名稱。

語音服務具有適用于 [語音轉換文字](rest-speech-to-text.md) 和 [文字轉換語音](rest-text-to-speech.md)的 REST api。 針對已啟用私人端點的案例，請考慮下列資訊。

語音轉換文字有兩個 REST Api。 每個 API 都有不同的用途，使用不同的端點，而且當您在啟用私人端點的案例中使用它時，需要不同的方法。

語音轉換文字 REST Api 如下：
- [語音轉換文字 REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30)，用於 [批次](batch-transcription.md) 轉譯和 [自訂語音](custom-speech-overview.md)。 v3.0 是 v2.0 [的後續版本](/azure/cognitive-services/speech-service/migrate-v2-to-v3)
- 用於線上轉譯的[短音訊語音轉換文字 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 

在私人端點案例中使用語音轉換文字 REST API，以取得短音訊和文字轉換語音 REST API。 這相當於本文稍後所述的 [語音 SDK 案例](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk) 。 

語音轉換文字 REST API v3.0 會使用一組不同的端點，因此需要針對啟用私人端點的案例使用不同的方法。

接下來的小節將說明這兩種情況。

##### <a name="speech-to-text-rest-api-v30"></a>語音轉換文字 REST API v3。0

語音資源通常會使用 [認知服務區域端點](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) 來與 [語音轉換文字 REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30)進行通訊。 這些資源具有下列命名格式： <p/>`{region}.api.cognitive.microsoft.com`.

這是範例要求 URL：

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
當您為語音資源啟用自訂網域 () 私人端點時，該資源將會針對基本 REST API 端點使用下列 DNS 名稱模式： <p/>`{your custom name}.cognitiveservices.azure.com`.

這表示在我們的範例中，REST API 端點名稱將會是： <p/>`my-private-link-speech.cognitiveservices.azure.com`.

您必須將範例要求 URL 轉換成：
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
此 URL 應該可從已附加私人端點的虛擬網路存取 (提供正確的 [DNS 解析](#resolve-dns-from-the-virtual-network)) 。

啟用語音資源的自訂功能變數名稱之後，您通常會以新的自訂網域主機名稱取代所有要求 Url 中的主機名稱。 要求的所有其他部分 (如 `/speechtotext/v3.0/transcriptions` 先前範例中的路徑) 保持不變。

> [!TIP]
> 某些客戶會開發使用區域端點 DNS 名稱區域部分的應用程式 (例如，將要求傳送至部署在特定 Azure 區域) 中的語音資源。
>
> 語音資源的自訂網域 *不* 包含部署資源所在區域的相關資訊。 因此稍早所述的應用程式邏輯將 *無法* 運作，而且需要更改。

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>簡短音訊和文字轉換語音 REST API 的語音轉換文字 REST API

適用于短音訊和[文字轉換語音 REST API](rest-text-to-speech.md)的[語音轉換文字 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio)會使用兩種類型的端點：
- 用來與認知服務進行通訊的[認知服務區域端點](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)，REST API 以取得授權權杖
- 所有其他作業的特殊端點

[本小節](#general-principles)會提供特殊端點的詳細說明，以及應如何針對啟用私人端點的語音資源轉換其 URL。 SDK 所述的相同原則適用于語音轉換文字 REST API v1.0 和文字轉換語音 REST API。

熟悉上一段所述小節中的材質，然後參閱下列範例。 此範例描述文字轉換語音 REST API。 將語音轉換文字 REST API 用於短音訊是完全相等的。

> [!NOTE]
> 當您針對私人端點案例中的簡短音訊使用語音轉換文字 REST API 時，請使用 [透過](rest-speech-to-text.md#request-headers) `Authorization` [標頭](rest-speech-to-text.md#request-headers)傳遞的授權權杖。 透過標頭將語音訂用帳戶金鑰傳遞給特殊端點 `Ocp-Apim-Subscription-Key` 將 *無法* 運作，且會產生錯誤401。

**文字轉換語音 REST API 使用範例**

我們將使用西歐作為範例 Azure 區域，以及 `my-private-link-speech.cognitiveservices.azure.com` (自訂網域) 的範例語音資源 DNS 名稱。 我們範例中的自訂功能變數名稱 `my-private-link-speech.cognitiveservices.azure.com` 屬於西歐區域中所建立的語音資源。

若要取得區域中支援的語音清單，請執行下列兩項作業：

- 取得授權權杖：
  ```http
  https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
  ```
- 使用權杖來取得語音清單：
  ```http
  https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
  ```
請參閱 [文字轉換語音 REST API 檔](rest-text-to-speech.md)中先前步驟的詳細資料。

針對啟用私人端點的語音資源，必須修改相同操作順序的端點 Url。 相同的順序看起來會像這樣：

- 取得授權權杖：
  ```http
  https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
  ```
  請參閱先前的 [語音轉換文字 REST API v3.0](#speech-to-text-rest-api-v30) 小節中的詳細說明。

- 使用取得的權杖，取得語音清單：
  ```http
  https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
  ```
  請參閱語音 SDK [一般原則](#general-principles) 小節中的詳細說明。

#### <a name="speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk"></a>具有自訂功能變數名稱和私人端點的語音資源：使用語音 SDK

若要搭配使用語音 SDK 與自訂功能變數名稱和啟用私人端點的語音資源，您必須檢查並可能變更您的應用程式程式碼。

在本節中，我們將使用 `my-private-link-speech.cognitiveservices.azure.com` (自訂網域) 範例語音資源 DNS 名稱。

##### <a name="general-principles"></a>一般原則

通常在 SDK 案例中 (以及文字轉換語音 REST API 案例) 中，語音資源會針對不同的服務供應專案使用專用的區域端點。 這些端點的 DNS 名稱格式為：

`{region}.{speech service offering}.speech.microsoft.com`

DNS 名稱範例如下：

`westeurope.stt.speech.microsoft.com`

在「 [語音服務支援的區域](regions.md)」中，會列出區域 (第一個 DNS 名稱) 的第一個元素的所有可能值。 下表顯示「語音服務」供應專案的可能值， (DNS 名稱) 的第二個元素：

| DNS 名稱值 | 語音服務供應專案                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [自訂命令](custom-commands.md)                       |
| `convai`       | [對話轉譯](conversation-transcription.md) |
| `s2s`          | [語音翻譯](speech-translation.md)                 |
| `stt`          | [語音轉文字](speech-to-text.md)                         |
| `tts`          | [文字轉換語音](text-to-speech.md)                         |
| `voice`        | [自訂語音](how-to-custom-voice.md)                      |

因此，先前的範例 (`westeurope.stt.speech.microsoft.com`) 代表西歐的語音轉換文字端點。

啟用私人端點的端點會透過特殊的 proxy 與語音服務進行通訊。 因此， *您必須變更端點連接 url*。 

「標準」端點 URL 看起來像這樣： <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

私人端點 URL 看起來像這樣： <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**範例1。** 應用程式會使用下列 URL (語音辨識，並使用西歐) 的美式英文基本模型進行通訊：

```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

若要在啟用私人端點的案例中使用它，當語音資源的自訂功能變數名稱為時 `my-private-link-speech.cognitiveservices.azure.com` ，您必須修改 URL，如下所示：

```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

請注意詳細資料：

- 主機名稱 `westeurope.stt.speech.microsoft.com` 會取代為自訂網域主機名稱 `my-private-link-speech.cognitiveservices.azure.com` 。
- 原始 DNS 名稱的第二個元素 (`stt`) 會成為 URL 路徑的第一個元素，並在原始路徑之前。 如此一來，原始的 URL `/speech/recognition/conversation/cognitiveservices/v1?language=en-US` 就會變成 `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` 。

**範例2。** 應用程式會使用下列 URL，在西歐使用自訂語音模型來合成語音：
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

下列對等的 URL 會使用啟用的私人端點，其中語音資源的自訂功能變數名稱為 `my-private-link-speech.cognitiveservices.azure.com` ：

```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

範例1中的相同原則已套用，但這次的關鍵元素是 `voice` 。

##### <a name="modifying-applications"></a>修改應用程式

遵循下列步驟來修改您的程式碼：

1. 判斷應用程式端點 URL：

   - [為您的應用程式啟用記錄](how-to-use-logging.md) ，並將其執行至記錄活動。
   - 在記錄檔中搜尋 `SPEECH-ConnectionUrl` 。 在相符的行中， `value` 參數包含您的應用程式用來存取語音服務的完整 URL。

   範例：

   ```
   (114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
   ```

   因此，應用程式在此範例中使用的 URL 為：

   ```
   wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
   ```

2. `SpeechConfig`使用完整端點 URL 來建立實例：

   1. 修改您剛剛決定的端點，如先前的 [一般原則](#general-principles) 一節中所述。

   1. 修改您建立實例的方式 `SpeechConfig` 。 最有可能的情況是，您的應用程式使用類似如下的內容：
      ```csharp
      var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
      ```
      這不適用於啟用私人端點的語音資源，因為我們在前面幾節中描述的是主機名稱和 URL 變更。 如果您嘗試使用啟用私人端點之資源的金鑰來執行現有的應用程式，而不進行任何修改，您將會收到 (401) 的驗證錯誤。

      若要使其正常運作，請修改如何將類別具現化， `SpeechConfig` 並使用「從端點」/「使用端點」初始化。 假設我們已定義下列兩個變數：
      - `subscriptionKey` 包含啟用私人端點之語音資源的索引鍵。
      - `endPoint` 包含完整 *修改過* 的端點 URL (使用對應的程式設計語言) 所需的型別。 在我們的範例中，此變數應該包含：
        ```
        wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
        ```

      建立 `SpeechConfig` 執行個體：
      ```csharp
      var config = SpeechConfig.FromEndpoint(endPoint, subscriptionKey);
      ```
      ```cpp
      auto config = SpeechConfig::FromEndpoint(endPoint, subscriptionKey);
      ```
      ```java
      SpeechConfig config = SpeechConfig.fromEndpoint(endPoint, subscriptionKey);
      ```
      ```python
      import azure.cognitiveservices.speech as speechsdk
      speech_config = speechsdk.SpeechConfig(endpoint=endPoint, subscription=subscriptionKey)
      ```
      ```objectivec
      SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithEndpoint:endPoint subscription:subscriptionKey];
      ```

> [!TIP]
> 端點 URI 中指定的查詢參數不會變更，即使它們是由其他 Api 所設定亦同。 例如，如果識別語言在 URI 中定義為查詢參數 `language=en-US` ，而且也透過對應的屬性設定為，則 `ru-RU` 會使用 uri 中的語言設定。 有效的語言就是 `en-US` 。
>
> 端點 URI 中設定的參數一律優先。 其他 Api 只能覆寫端點 URI 中未指定的參數。

修改之後，您的應用程式應該使用啟用私人端點的語音資源。 我們正在努力更順暢地支援私人端點案例。

### <a name="use-a-speech-resource-with-a-custom-domain-name-and-without-private-endpoints"></a>使用具有自訂功能變數名稱且沒有私用端點的語音資源

在本文中，我們指出了幾次讓語音資源的自訂網域無法 *復原* 的情況。 相較于使用 [區域端點名稱](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)的語音服務，這類資源將會使用不同的方式來與語音服務進行通訊。

本節說明如何搭配使用語音資源與啟用的自訂功能變數名稱，但 *不含* 任何具有語音服務 REST Api 和 [語音 SDK](speech-sdk.md)的私人端點。 這可能是在私人端點案例中使用過一次的資源，但已刪除其私人端點。

#### <a name="dns-configuration"></a>DNS 組態

請記住，啟用私人端點的語音資源自訂網域 DNS 名稱是如何 [從公用網路解析](#resolve-dns-from-other-networks)的。 在此情況下，已解析的 IP 位址會指向虛擬網路的 proxy 端點。 該端點是用來將網路流量分派至啟用私人端點的認知服務資源。

不過，在啟用自訂功能變數名稱)  (或右邊移除 *所有* 資源私人端點時，會重新布建語音資源的 CNAME 記錄。 它現在會指向對應 [認知服務區域端點](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)的 IP 位址。

因此命令的輸出 `nslookup` 看起來會像這樣：
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  fe80::1

Non-authoritative answer:
Name:    apimgmthskquihpkz6d90kmhvnabrx3ms3pdubscpdfk1tsx3a.cloudapp.net
Address:  13.93.122.1
Aliases:  my-private-link-speech.cognitiveservices.azure.com
          westeurope.api.cognitive.microsoft.com
          cognitiveweprod.trafficmanager.net
          cognitiveweprod.azure-api.net
          apimgmttmdjylckcx6clmh2isu2wr38uqzm63s8n4ub2y3e6xs.trafficmanager.net
          cognitiveweprod-westeurope-01.regional.azure-api.net
```
將它 [與本節的](#resolve-dns-from-other-networks)輸出做比較。

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-rest-apis"></a>具有自訂功能變數名稱且沒有私用端點的語音資源：使用 REST Api

##### <a name="speech-to-text-rest-api-v30"></a>語音轉換文字 REST API v3。0

語音轉換文字 REST API v3.0 使用方式完全等同于 [啟用私人端點的語音資源](#speech-to-text-rest-api-v30)案例。

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>簡短音訊和文字轉換語音 REST API 的語音轉換文字 REST API

在此情況下，使用語音轉換文字 REST API 的簡短音訊和文字轉換語音 REST API 的使用方式與一般案例沒有任何差異，而語音轉換文字 REST API 的聲音則是一個例外。  (請參閱下列附注。 ) 您應該使用 [語音轉換文字 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 中所述的兩個 api，以取得簡短的音訊和 [文字轉換語音 REST API](rest-text-to-speech.md) 檔。

> [!NOTE]
> 當您針對自訂網域案例中的短音訊使用語音轉換文字 REST API 時，請使用 [透過](rest-speech-to-text.md#request-headers) `Authorization` [標頭](rest-speech-to-text.md#request-headers)傳遞的授權權杖。 透過標頭將語音訂用帳戶金鑰傳遞給特殊端點 `Ocp-Apim-Subscription-Key` 將 *無法* 運作，且會產生錯誤401。

#### <a name="speech-resource-with-a-custom-domain-name-and-without-private-endpoints-usage-with-the-speech-sdk"></a>具有自訂功能變數名稱且沒有私用端點的語音資源：使用語音 SDK

使用語音 SDK 搭配具備自訂網域功能的語音資源， *而不* 需要私人端點，則需要對應用程式程式碼的檢查和可能變更。 請注意，這些變更與 [啟用私人端點的語音資源](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)案例不同。 我們正在努力更順暢地支援私人端點和自訂網域案例。

在本節中，我們將使用 `my-private-link-speech.cognitiveservices.azure.com` (自訂網域) 範例語音資源 DNS 名稱。

在 [啟用私人端點的語音資源](#speech-resource-with-a-custom-domain-name-and-a-private-endpoint-usage-with-the-speech-sdk)一節中，我們說明了如何判斷端點 URL、加以修改，並透過「從端點」/「端點」/「使用端點」初始化類別實例來進行工作 `SpeechConfig` 。

但是，如果您嘗試在移除所有私人端點之後，執行相同的應用程式 (讓對應的 DNS 記錄重新布建) 有一段時間，您將會收到 (404) 的內部服務錯誤。 原因是 [DNS 記錄](#dns-configuration) 現在指向區域認知服務端點，而不是虛擬網路 proxy，且此處找不到類似的 URL 路徑 `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` 。

如果您使用下列程式碼的樣式，將應用程式復原至的標準具現化 `SpeechConfig` ，則您的應用程式將會終止，並出現驗證錯誤 (401) ：

```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```

##### <a name="modifying-applications"></a>修改應用程式

若要讓您的應用程式使用具有自訂功能變數名稱且沒有私人端點的語音資源，請遵循下列步驟：

1. 從認知服務 REST API 要求授權權杖。 [本文說明如何](../authentication.md#authenticate-with-an-authentication-token) 取得權杖。

   在端點 URL 中使用您的自訂功能變數名稱。 在我們的範例中，此 URL 為：
   ```http
   https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
   ```
   > [!TIP]
   > 您可以在 Azure 入口網站中找到此 URL。 在 [語音資源] 頁面的 [ **資源管理** ] 群組底下，選取 [ **金鑰和端點**]。

1. `SpeechConfig`使用您在上一節中取得的授權權杖來建立實例。 假設我們已定義下列變數：

   - `token`：上一節中取得的授權權杖
   - `azureRegion`：語音資源 [區域](regions.md) 的名稱 (範例： `westeurope`) 
   - `outError`：僅適用于 [目標 C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) 案例) 的 (

   建立 `SpeechConfig` 如下的實例：

   ```csharp
   var config = SpeechConfig.FromAuthorizationToken(token, azureRegion);
   ```
   ```cpp
   auto config = SpeechConfig::FromAuthorizationToken(token, azureRegion);
   ```
   ```java
   SpeechConfig config = SpeechConfig.fromAuthorizationToken(token, azureRegion);
   ```
   ```python
   import azure.cognitiveservices.speech as speechsdk
   speech_config = speechsdk.SpeechConfig(auth_token=token, region=azureRegion)
   ```
   ```objectivec
   SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithAuthorizationToken:token region:azureRegion error:outError];
   ```
> [!NOTE]
> 呼叫端必須確定授權權杖是否有效。 在授權權杖到期之前，呼叫端必須使用新的有效權杖來呼叫這個 setter，以重新整理它。 由於設定值會在您建立新的辨識器或合成器時複製，因此新的權杖值不會套用至已建立的辨識器或合成器。
>
> 針對這些設定，請設定對應辨識器或合成器的授權權杖，以重新整理權杖。 如果您未重新整理權杖，辨識器或合成器會在操作時遇到錯誤。

進行這種修改之後，您的應用程式應該使用不具私人端點的自訂功能變數名稱的語音資源。

## <a name="pricing"></a>定價

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="learn-more"></a>深入了解

* [Azure Private Link](../../private-link/private-link-overview.md)
* [語音 SDK](speech-sdk.md)
* [語音轉換文字 REST API](rest-speech-to-text.md)
* [文字轉換語音 REST API](rest-text-to-speech.md)
