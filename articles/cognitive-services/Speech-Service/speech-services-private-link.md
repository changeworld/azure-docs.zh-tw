---
title: 如何搭配使用私人端點與語音服務
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
ms.openlocfilehash: f905582615b16780fae179ba6a21bd4343bd47f3
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/23/2020
ms.locfileid: "97755798"
---
# <a name="use-speech-service-through-a-private-endpoint"></a>透過私人端點使用語音服務

[Azure Private Link](../../private-link/private-link-overview.md) 可讓您使用 [私人端點](../../private-link/private-endpoint-overview.md)連接至 Azure 中的服務。
私人端點是只能在特定 [虛擬網路](../../virtual-network/virtual-networks-overview.md) 和子網記憶體取的私人 IP 位址。

本文說明如何使用 Azure 認知語音服務來設定和使用 Private Link 和私人端點。

> [!NOTE]
> 本文說明使用 Azure 認知語音服務設定和使用 Private Link 的詳細資訊。 繼續之前，請先參閱如何搭配 [使用虛擬網路與認知服務](../cognitive-services-virtual-networks.md)。

執行下列工作，以透過私人端點使用語音服務：

1. [建立語音資源自訂功能變數名稱](#create-a-custom-domain-name)
2. [建立並設定私人端點 (s) ](#enable-private-endpoints)
3. [調整現有的應用程式和解決方案](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)

若要稍後移除私人端點，但仍使用語音資源，您將執行 [本節中的](#use-speech-resource-with-custom-domain-name-without-private-endpoints)工作。

## <a name="create-a-custom-domain-name"></a>建立自訂功能變數名稱

私人端點需要 [認知服務自訂子功能變數名稱稱](../cognitive-services-custom-subdomains.md)。 請遵循下列指示，為您的語音資源建立一個。

> [!CAUTION]
> 啟用自訂功能變數名稱的語音資源會使用不同的方式來與語音服務互動。
> 您可能必須調整 [啟用私人端點](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled) 的應用程式程式碼，而 [**不** 是啟用私人端點](#use-speech-resource-with-custom-domain-name-without-private-endpoints) 的案例。
>
> 當您啟用自訂功能變數名稱時，此作業 [**無法**](../cognitive-services-custom-subdomains.md#can-i-change-a-custom-domain-name)復原。 返回 [區功能變數名稱稱](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) 的唯一方法是建立新的語音資源。
>
> 如果您的語音資源具有許多相關聯的自訂模型和透過 [語音 Studio](https://speech.microsoft.com/) 建立的專案， **強烈** 建議您先嘗試使用測試資源進行設定，再修改生產環境中所使用的資源。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要使用 Azure 入口網站建立自訂功能變數名稱，請遵循下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com/) 並登入您的 Azure 帳戶。
1. 選取所需的語音資源。
1. 在左側流覽窗格的 [ **資源管理** ] 群組中，按一下 [ **網路**]。
1. 在 [ **防火牆與虛擬網路** ] 索引標籤中，按一下 [ **產生自訂功能變數名稱**]。 新的右面板隨即出現，並顯示為您的資源建立唯一自訂子域的指示。
1. 在 [產生自訂功能變數名稱] 面板中，輸入自訂功能變數名稱部分。 您的完整自訂網域看起來會像這樣： `https://{your custom name}.cognitiveservices.azure.com` 。 
    **建立自訂功能變數名稱之後，就 _無法_ 變更！重新閱讀上述的警告警示。** 輸入自訂功能變數名稱之後，請按一下 [ **儲存**]。
1. 作業完成之後，請在 [ **資源管理** ] 群組中，按一下 [ **金鑰和端點**]。 確認資源的新端點名稱會以這種方式啟動：

    `https://{your custom name}.cognitiveservices.azure.com`

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 建立自訂功能變數名稱，請確認您的電腦具有 PowerShell 7.x 版或更新版本（含 Azure PowerShell 模組版本5.1.0 或更新版本）。 若要查看這些工具的版本，請遵循下列步驟：

1. 在 PowerShell 視窗中，輸入：

    `$PSVersionTable`

    確認 PSVersion 值大於 7. x。 若要升級 PowerShell，請遵循 [安裝各種版本的 powershell](/powershell/scripting/install/installing-powershell) 以進行升級的指示。

1. 在 PowerShell 視窗中，輸入：

    `Get-Module -ListAvailable Az`

    如果未顯示任何內容，或 Azure PowerShell 模組版本低於5.1.0，請依照 [安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps) 上的指示進行升級。

繼續之前，請執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="verify-custom-domain-name-is-available"></a>確認自訂功能變數名稱可用

您需要檢查您想要使用的自訂網域是否可用。 請遵循下列步驟，使用認知服務 REST API 中的 [ [檢查網域可用性](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) ] 作業來確認網域可供使用。

> [!TIP]
> 下列程式碼在 Azure Cloud Shell 中將 **無法** 運作。

```azurepowershell
$subId = "Your Azure subscription Id"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
# You can skip this step if your Azure account has only one active subscription.
Set-AzContext -SubscriptionId $subId

# Prepare OAuth token to use in request to Cognitive Services REST API.
$Context = Get-AzContext
$AccessToken = (Get-AzAccessToken -TenantId $Context.Tenant.Id).Token
$token = ConvertTo-SecureString -String $AccessToken -AsPlainText -Force

# Prepare and send the request to Cognitive Services REST API.
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

若要為選取的語音資源啟用自訂功能變數名稱，我們會使用 [AzCognitiveServicesAccount 指令程式](/powershell/module/az.cognitiveservices/set-azcognitiveservicesaccount) 。

> [!CAUTION]
> 順利執行下列程式碼之後，您將會建立語音資源的自訂功能變數名稱。
> **無法** 變更此名稱。 如需詳細資訊，請參閱上面的 **警告** 警示。

```azurepowershell
$resourceGroup = "Resource group name where Speech resource is located"
$speechResourceName = "Your Speech resource name"
$subdomainName = "custom domain name"

# Select the Azure subscription that contains Speech resource.
# You can skip this step if your Azure account has only one active subscription.
$subId = "Your Azure subscription Id"
Set-AzContext -SubscriptionId $subId

# Set the custom domain name to the selected resource.
# CAUTION: THIS CANNOT BE CHANGED OR UNDONE!
Set-AzCognitiveServicesAccount -ResourceGroupName $resourceGroup `
    -Name $speechResourceName -CustomSubdomainName $subdomainName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本節需要 Azure CLI 的最新版本。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="verify-the-custom-domain-name-is-available"></a>確認自訂功能變數名稱可用

您需要檢查您想要使用的自訂網域是否為免費。 我們將使用認知服務 REST API 中的 [ [檢查網域可用性](/rest/api/cognitiveservices/accountmanagement/checkdomainavailability/checkdomainavailability) 方法]。

複製下方的程式碼區塊，插入您慣用的自訂功能變數名稱，並儲存至檔案 `subdomain.json` 。

```json
{
    "subdomainName": "custom domain name",
    "type": "Microsoft.CognitiveServices/accounts"
}
```

將檔案複製到您目前的資料夾，或將它上傳至 Azure Cloud Shell，然後執行下列命令。  (取代 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 為您的 Azure 訂用帳戶識別碼) 。

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
## <a name="enable-custom-domain-name"></a>啟用自訂功能變數名稱

若要為選取的語音資源啟用自訂功能變數名稱，我們會使用 [az cognitiveservices account update](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_update) 命令。

選取包含語音資源的 Azure 訂用帳戶。 如果您的 Azure 帳戶只有一個使用中的訂用帳戶，您可以略過此步驟。  (取代 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 為您的 Azure 訂用帳戶識別碼) 。
```azurecli-interactive
az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
將自訂功能變數名稱設定為選取的資源。 將範例參數值取代為實際的參數值，然後執行下列命令。

> [!CAUTION]
> 成功執行以下命令之後，您將會建立語音資源的自訂功能變數名稱。 **無法** 變更此名稱。 如需詳細資訊，請參閱上面的警告警示。

```azurecli
az cognitiveservices account update --name my-speech-resource-name --resource-group my-resource-group-name --custom-domain my-custom-name
```

**_

## <a name="enable-private-endpoints"></a>啟用私人端點

使用 Azure 入口網站、Azure PowerShell 或 Azure CLI 啟用私人端點。

建議您在布建過程中，使用附加至虛擬網路的 [私人 DNS 區域](../../dns/private-dns-overview.md) ，並搭配私人端點的必要更新（依預設會建立）。 但是，如果您使用自己的 DNS 伺服器，您可能需要對 DNS 設定進行其他變更。 請參閱 [ [私人端點的 DNS](#dns-for-private-endpoints) ] 區段。 最佳做法是在針對生產語音資源布建私人端點 () *之前，先* 決定 DNS 策略 _。 我們也建議進行初步測試，特別是當您使用自己的 DNS 伺服器時。

使用下列文章來建立私人端點 (s) 。 這些文章會將 Web 應用程式做為使用私人端點啟用的範例資源。 請改用下列參數：

| 設定             | 值                                    |
|---------------------|------------------------------------------|
| 資源類型       | **CognitiveServices/帳戶** |
| 資源            | **\<your-speech-resource-name>**         |
| 目標子資源 | **account**                              |

- [使用 Azure 入口網站建立私人端點](../../private-link/create-private-endpoint-portal.md)
- [使用 Azure PowerShell 建立私人端點](../../private-link/create-private-endpoint-powershell.md)
- [使用 Azure CLI 建立私人端點](../../private-link/create-private-endpoint-cli.md)

### <a name="dns-for-private-endpoints"></a>私人端點的 DNS

熟悉 [認知服務資源中私人端點的 DNS](../cognitive-services-virtual-networks.md#dns-changes-for-private-endpoints)一般原則。 然後檢查您的 DNS 設定是否正常運作 (請參閱下一節) 。

#### <a name="mandatory-check-dns-resolution-from-the-virtual-network"></a> (強制檢查) 。 來自虛擬網路的 DNS 解析

我們將使用 `my-private-link-speech.cognitiveservices.azure.com` 此區段的範例語音資源 DNS 名稱。

登入位於您已連接私人端點的虛擬網路中的虛擬機器。 開啟 Windows 命令提示字元或 Bash shell，執行 `nslookup` 並確認它已成功解析您的資源自訂功能變數名稱：
```dos
C:\>nslookup my-private-link-speech.cognitiveservices.azure.com
Server:  UnKnown
Address:  168.63.129.16

Non-authoritative answer:
Name:    my-private-link-speech.privatelink.cognitiveservices.azure.com
Address:  172.28.0.10
Aliases:  my-private-link-speech.cognitiveservices.azure.com
```
檢查已解析的 IP 位址是否對應到您私人端點的位址。

#### <a name="optional-check-dns-resolution-from-other-networks"></a> (選擇性檢查) 。 來自其他網路的 DNS 解析

如果您打算在「混合式」模式中使用啟用私人端點的語音資源，且您已在資源的 [*網路*] 區段中啟用 [*所有網路*] 或 [*選取的網路] 和 [私人端點*] 存取選項，就需要進行這項檢查。 如果您打算只使用私人端點來存取資源，您可以略過本節。

我們會使用 `my-private-link-speech.cognitiveservices.azure.com` 做為此區段的範例語音資源 DNS 名稱。

在任何連接到您允許存取資源之網路的電腦上，開啟 Windows 命令提示字元或 Bash shell，執行命令， `nslookup` 並確認它已成功解析您的資源自訂功能變數名稱：
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

請注意，已解析的 IP 位址指向虛擬網路 proxy 端點，其會將網路流量分派至認知服務資源的私人端點。 對於具有自訂功能變數名稱但 *沒有* 私用端點的資源，其行為會有所不同。 如需詳細資訊，請參閱 [這一節](#dns-configuration) 。

## <a name="adjust-existing-applications-and-solutions"></a>調整現有的應用程式和解決方案

啟用自訂網域的語音資源會使用不同的方式來與語音服務互動。 這適用于具有和[沒有](#use-speech-resource-with-custom-domain-name-without-private-endpoints)私[用](#use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled)端點的已啟用自訂網域語音資源。 目前的區段提供這兩種案例的必要資訊。

### <a name="use-speech-resource-with-custom-domain-name-and-private-endpoint-enabled"></a>使用已啟用自訂功能變數名稱和私人端點的語音資源

啟用自訂功能變數名稱和私人端點的語音資源會使用不同的方式來與語音服務互動。 本節說明如何搭配語音服務 REST API 和 [語音 SDK](speech-sdk.md)來使用這類資源。

> [!NOTE]
> 請注意，沒有私人端點的語音資源，但啟用了 **自訂功能變數名稱** ，也有特殊的方式可以與語音服務互動，但這種方式與啟用私人端點的語音資源案例不同。 如果您有這類資源 (比方說，您有一個具有私人端點的資源，但之後決定移除它們) 請務必熟悉 [對應] [區段](#use-speech-resource-with-custom-domain-name-without-private-endpoints)。

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-rest-api"></a>具有自訂功能變數名稱和私人端點的語音資源。 使用 REST API

在本節中，我們將使用 `my-private-link-speech.cognitiveservices.azure.com` (自訂網域) 範例語音資源 DNS 名稱。

##### <a name="note-on-speech-services-rest-api"></a>語音服務 REST API 注意事項

語音服務具有 [語音轉換文字](rest-speech-to-text.md) 和 [文字轉換語音](rest-text-to-speech.md)的 REST API。 必須針對啟用私人端點的案例考慮下列各項。

語音轉換文字有兩個不同的 REST Api。 每個 API 都有不同的用途，使用不同的端點，而且在啟用私人端點的案例中使用時，需要不同的方法。

語音轉換文字 REST Api 如下：
- [語音轉換文字 REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) 用於 [批次](batch-transcription.md) 轉譯和 [自訂語音](custom-speech-overview.md)。 v3.0 是 v2.0 [的後續版本](/azure/cognitive-services/speech-service/migrate-v2-to-v3)。
- [簡短音訊的語音轉換文字 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 用於線上轉譯。 

在私人端點案例中，針對簡短音訊和文字轉換語音 REST API 使用語音轉換文字 REST API，與本文稍後所述的語音 SDK 案例相同且相當於 [語音 SDK 案例](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) 。 

語音轉換文字 REST API v3.0 正在使用一組不同的端點，因此需要不同的方法來啟用私人端點的案例。

這兩個案例都會在下一節中說明。


##### <a name="speech-to-text-rest-api-v30"></a>語音轉換文字 REST API v3。0

語音資源通常會使用 [認知服務區域端點](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints) 來與 [語音轉換文字 REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30)進行通訊。 這些資源具有下列命名格式： <p/>`{region}.api.cognitive.microsoft.com`

這是範例要求 URL：

```http
https://westeurope.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions
```
針對 (私人端點所需的語音資源啟用自訂網域之後) 這類資源將會使用下列基本 REST API 端點的 DNS 名稱模式： <p/>`{your custom name}.cognitiveservices.azure.com`

這表示在我們的範例中，REST API 端點名稱將會是： <p/>`my-private-link-speech.cognitiveservices.azure.com`

而且上述的範例要求 URL 必須轉換成：
```http
https://my-private-link-speech.cognitiveservices.azure.com/speechtotext/v3.0/transcriptions
```
此 URL 應該可從已附加私人端點的虛擬網路存取 (提供正確的 [DNS 解析](#mandatory-check-dns-resolution-from-the-virtual-network)) 。

一般來說，在啟用語音資源的自訂功能變數名稱之後，您必須以新的自訂網域主機名稱取代所有要求 Url 中的主機名稱。 要求的所有其他部分 (類似 `/speechtotext/v3.0/transcriptions` 上述範例中的路徑) 保持不變。

> [!TIP]
> 某些客戶開發的應用程式會使用區域端點 DNS 名稱的區域部分 (例如，將要求傳送至在特定 Azure 區域中部署的語音資源) 。
>
> 語音資源自訂功能變數名稱 **未** 包含部署資源所在區域的相關資訊。 因此上述的應用程式邏輯將 **無法** 運作，而且需要更改。

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>簡短音訊和文字轉換語音 REST API 的語音轉換文字 REST API

適用于簡短音訊和[文字轉換語音](rest-text-to-speech.md)[的語音轉換文字 REST API](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) REST API 會使用兩種類型的端點：
- 用來與認知服務進行通訊的[認知服務區域端點](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)，REST API 以取得授權權杖
- 所有其他作業的特殊端點

您可以在下面的「使用語音 SDK 的使用方式」一節中，提供特殊端點的詳細說明，以及應如何針對啟用私人 [端點的語音](#general-principle) 資源轉換其 URL。 針對「語音轉換文字1.0」和「文字轉換語音」 REST API，適用于 SDK 的相同原則也適用。

熟悉上一段所述小節中的材質，然後參閱下列範例。  (範例描述文字轉換語音 REST API;針對短音訊使用語音轉換文字 REST API 是完全相同的) 

> [!NOTE]
> 針對私人端點案例中 **的短音訊使用語音轉換文字 REST API** 時，您必須使用 [透過](rest-speech-to-text.md#request-headers) `Authorization` [標頭](rest-speech-to-text.md#request-headers)傳遞的授權權杖; 透過標頭將語音訂用帳戶金鑰傳遞給特殊端點 `Ocp-Apim-Subscription-Key` 將 **無法** 運作，且會產生錯誤401。

**文字轉換語音 REST API 使用範例。**

我們將使用西歐作為範例 Azure 區域，以及 `my-private-link-speech.cognitiveservices.azure.com` (自訂網域) 的範例語音資源 DNS 名稱。 `my-private-link-speech.cognitiveservices.azure.com`我們範例中的自訂功能變數名稱屬於西歐區域中所建立的語音資源。

若要取得區域中支援的語音清單，必須執行下列兩項作業：

- 取得授權權杖：
```http
https://westeurope.api.cognitive.microsoft.com/sts/v1.0/issuetoken
```
- 使用權杖來取得語音清單：
```http
https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list
```
 (在 [文字轉換語音 REST API 檔](rest-text-to-speech.md) 中查看上述步驟的詳細資料) 

針對啟用私人端點的語音資源，必須修改相同操作順序的端點 Url。 相同的順序看起來會像這樣：
- 取得授權權杖 via
```http
https://my-private-link-speech.cognitiveservices.azure.com/v1.0/issuetoken
```
 (請參閱上述的 [語音轉換文字 REST API v3.0](#speech-to-text-rest-api-v30) 小節中的詳細說明) 
- 使用取得的權杖取得語音清單 via
```http
https://my-private-link-speech.cognitiveservices.azure.com/tts/cognitiveservices/voices/list
```
 (請參閱下面的「使用語音 SDK 的使用方式」一節中的「 [一般」主體](#general-principle) 小節中的詳細說明) 

#### <a name="speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk"></a>具有自訂功能變數名稱和私人端點的語音資源。 使用語音 SDK

使用具有自訂功能變數名稱和啟用私人端點的語音資源的語音 SDK 需要審查和可能變更您的應用程式程式碼。 我們正在努力更順暢地支援私人端點案例。

在本節中，我們將使用 `my-private-link-speech.cognitiveservices.azure.com` (自訂網域) 範例語音資源 DNS 名稱。

##### <a name="general-principle"></a>一般原則

通常在 SDK 案例中 (以及文字轉換語音 REST API 案例中) 語音資源會針對不同的服務供應專案使用特殊的區域端點。 這些端點的 DNS 名稱格式為： </p>`{region}.{speech service offering}.speech.microsoft.com`

範例： </p>`westeurope.stt.speech.microsoft.com`

下表所列的是 [DNS 名稱的第一個專案] (第一個元素的所有可能值) 如下 [表所示](regions.md) ，這是「語音 (服務」供應專案的可能值) dns 名稱的第二個元素：

| DNS 名稱值 | 語音服務供應專案                                    |
|----------------|-------------------------------------------------------------|
| `commands`     | [自訂命令](custom-commands.md)                       |
| `convai`       | [對話轉譯](conversation-transcription.md) |
| `s2s`          | [語音翻譯](speech-translation.md)                 |
| `stt`          | [語音轉文字](speech-to-text.md)                         |
| `tts`          | [文字轉換語音](text-to-speech.md)                         |
| `voice`        | [自訂語音](how-to-custom-voice.md)                      |

因此，上述範例 (`westeurope.stt.speech.microsoft.com`) 代表西歐的語音轉換文字端點。

啟用私人端點的端點會透過特殊的 proxy 與語音服務進行通訊，因為 **端點連接 url 必須變更**。 套用的原則如下：「標準」端點 URL 會遵循下列模式： <p/>`{region}.{speech service offering}.speech.microsoft.com/{URL path}`

則必須變更為： <p/>`{your custom name}.cognitiveservices.azure.com/{speech service offering}/{URL path}`

**範例1。** 應用程式會使用下列 URL 來進行通訊， (在西歐) 使用美國英文的基本模型進行語音辨識： 
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

若要在啟用私人端點的案例中使用它，當語音資源的自訂功能變數名稱為此 URL 時，必須 `my-private-link-speech.cognitiveservices.azure.com` 像這樣修改：
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

讓我們更仔細看看：
- 主機名稱 `westeurope.stt.speech.microsoft.com` 被自訂網域主機名稱取代 `my-private-link-speech.cognitiveservices.azure.com`
- 原始 DNS 名稱的第二個元素 (`stt`) 會成為 URL 路徑的第一個元素，並在原始路徑之前，也就是原始 URL。 `/speech/recognition/conversation/cognitiveservices/v1?language=en-US``/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US`
 
**範例2。** 應用程式使用下列 URL 進行通訊 (語音合成使用西歐的自訂語音模型) ： 
```http
https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```
若要在啟用私人端點的案例中使用它，當語音資源的自訂功能變數名稱為此 URL 時，必須 `my-private-link-speech.cognitiveservices.azure.com` 像這樣修改： 
```http
https://my-private-link-speech.cognitiveservices.azure.com/voice/cognitiveservices/v1?deploymentId=974481cc-b769-4b29-af70-2fb557b897c4
```

套用了與範例1相同的原則，但這次的關鍵元素是 `voice` 。

##### <a name="modifying-applications"></a>修改應用程式

若要將上一節中所述的原則套用至您的應用程式程式碼，您需要執行兩項重要工作：

- 判斷您的應用程式正在使用的端點 URL
- 依照上一節所述修改您的端點 URL，並 `SpeechConfig` 明確地使用此修改過的 URL 來建立類別實例

###### <a name="determine-application-endpoint-url"></a>判斷應用程式端點 URL

- [為您的應用程式啟用記錄](how-to-use-logging.md) ，並執行它來產生記錄檔
- 在記錄檔中搜尋 `SPEECH-ConnectionUrl` 。 字串將會包含 `value` 參數，而參數會包含您的應用程式所使用的完整 URL

具有端點 URL 的記錄檔行範例：
```
(114917): 41ms SPX_DBG_TRACE_VERBOSE:  property_bag_impl.cpp:138 ISpxPropertyBagImpl::LogPropertyAndValue: this=0x0000028FE4809D78; name='SPEECH-ConnectionUrl'; value='wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?traffictype=spx&language=en-US'
```
因此，此範例中的應用程式所使用的 URL 為：
```
wss://westeurope.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
###### <a name="create-speechconfig-instance-using-full-endpoint-url"></a>`SpeechConfig`使用完整端點 URL 建立實例

依照上述 [一般原則](#general-principle) 中所述，修改您在上一節中決定的端點。

現在您需要修改您建立實例的方式 `SpeechConfig` 。 您目前的應用程式很可能會使用類似以下的內容：
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
因為先前各節所述的主機名稱和 URL 變更，所以無法用於啟用私人端點的語音資源。 如果您嘗試使用啟用私人端點之資源的金鑰來執行現有的應用程式，而不進行任何修改，您將會收到 (401) 的驗證錯誤。

若要使其運作，您必須修改具現化類別的方式， `SpeechConfig` 並使用 "from endpoint"/"與端點" 初始化。 假設我們已定義下列兩個變數：
- `subscriptionKey` 包含啟用私人端點語音資源的金鑰
- `endPoint` 包含完整 **修改過** 的端點 URL (使用連結程式設計語言) 所需的型別。 在我們的範例中，此變數應該包含
```
wss://my-private-link-speech.cognitiveservices.azure.com/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```
接著，我們需要具現化 `SpeechConfig` 類別，如下所示：
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
> 端點 URI 中指定的查詢參數不會變更，即使它們是由任何其他 Api 所設定亦同。 例如，如果辨識語言在 URI 中定義為查詢參數 "language = en-us"，而且也透過 [對應] 屬性設定為 [ru-RU]，則 URI 中的語言設定會優先，而且有效的語言為 "en-us"。 只有未在端點 URI 中指定的參數可以由其他 Api 進行設定。

修改之後，您的應用程式應該使用啟用私人的語音資源。 我們正在努力更順暢地支援私人端點案例。

### <a name="use-speech-resource-with-custom-domain-name-without-private-endpoints"></a>使用沒有私人端點的自訂功能變數名稱的語音資源

在本文中，我們已指出啟用語音資源的自訂網域是無法 **復原** 的，而這類資源將會使用不同的方式來與語音服務進行通訊，而這些服務與使用 [區域端點名稱](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)) 的「一般」 (的方式相比較。

本節說明如何使用已啟用自訂功能變數名稱的語音資源，但 **不含** 任何具有語音服務 REST API 和 [語音 SDK](speech-sdk.md)的私人端點。 這可能是私人端點案例中所使用的資源，但其私人端點 (s) 刪除。

#### <a name="dns-configuration"></a>DNS 組態

請記住，私人端點啟用語音資源的自訂網域 DNS 名稱是如何 [從公用網路解析](#optional-check-dns-resolution-from-other-networks)的。 在此情況下，IP 位址會被解析為 VNet Proxy 端點，此端點是用來將網路流量分派至啟用私人端點的認知服務資源。

不過，在啟用自訂功能變數名稱後 (或右邊移除 **所有** 資源私人端點時) 會重新布建語音資源的 CNAME 記錄，而現在會指向對應 [認知服務區域端點](../cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)的 IP 位址。

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
將它 [與本節的](#optional-check-dns-resolution-from-other-networks)輸出做比較。

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-rest-api"></a>沒有私人端點的自訂功能變數名稱的語音資源。 使用 REST API

##### <a name="speech-to-text-rest-api-v30"></a>語音轉換文字 REST API v3。0

語音轉換文字 REST API v3.0 使用方式完全等同于 [啟用私人端點語音資源](#speech-to-text-rest-api-v30)的情況。

##### <a name="speech-to-text-rest-api-for-short-audio-and-text-to-speech-rest-api"></a>簡短音訊和文字轉換語音 REST API 的語音轉換文字 REST API

在此情況下，語音轉換文字 REST API 針對短音訊和文字轉換語音 REST API 使用方式與一般案例沒有任何差異，因為語音轉換文字 REST API 的簡短音訊例外 (請參閱以下) 的附注。 這兩個 Api 都應該依照 [語音轉換文字 REST API 中的說明，以取得簡短的音訊](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio) 和 [文字轉換語音 REST API](rest-text-to-speech.md) 檔。

> [!NOTE]
> 針對自訂網域案例中 **的短音訊使用語音轉換文字 REST API** 時，您必須使用 [透過](rest-speech-to-text.md#request-headers) `Authorization` [標頭](rest-speech-to-text.md#request-headers)傳遞的授權權杖; 透過標頭將語音訂用帳戶金鑰傳遞給特殊端點 `Ocp-Apim-Subscription-Key` 將 **無法** 運作，而且會產生錯誤401。

#### <a name="speech-resource-with-custom-domain-name-without-private-endpoints-usage-with-speech-sdk"></a>沒有私人端點的自訂功能變數名稱的語音資源。 使用語音 SDK

使用具有已啟用自訂功能變數名稱之語音資源的語音 SDK 時，您的應用程式程式碼必須 **經過** 審核和可能變更。 請注意，相較于 [啟用私人端點的語音資源](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk)，這些變更與案例 **不同**。 我們正在努力更順暢地支援私人端點/自訂網域案例。

在本節中，我們將使用 `my-private-link-speech.cognitiveservices.azure.com` (自訂網域) 範例語音資源 DNS 名稱。

在 [啟用私人端點的語音資源](#speech-resource-with-custom-domain-name-and-private-endpoint-usage-with-speech-sdk) 一節中，我們說明了如何判斷所使用的端點 URL、加以修改，並透過「從端點」/「端點」/「端點」初始化類別實例來進行工作 `SpeechConfig` 。

但是，如果您嘗試在移除所有私人端點後執行相同的應用程式 (允許一些時間到對應的 DNS 記錄重新布建) 您將會收到 (404) 的內部服務錯誤。 原因是現在會指向區域認知服務端點（而不是 VNet proxy）的 [DNS 記錄](#dns-configuration) ，並在該處找不到類似的 URL 路徑 `/stt/speech/recognition/conversation/cognitiveservices/v1?language=en-US` ，因此 (404) 中找不到「找不到」錯誤。

如果您將應用程式「向前復原」至的「標準」具現化，則為的 `SpeechConfig` 樣式
```csharp
var config = SpeechConfig.FromSubscription(subscriptionKey, azureRegion);
```
您的應用程式將會終止，並出現驗證錯誤 (401) 。

##### <a name="modifying-applications"></a>修改應用程式

若要讓您的應用程式使用自訂功能變數名稱（沒有私人端點）的語音資源案例，您需要執行下列動作：
- 透過認知服務 REST API 要求授權權杖
- `SpeechConfig`使用「從授權權杖」/「授權權杖」方法來具現化類別 

###### <a name="request-authorization-token"></a>要求授權權杖

請參閱 [這篇文章](../authentication.md#authenticate-with-an-authentication-token) ，瞭解如何透過認知服務 REST API 取得權杖。 

在端點 URL 中使用您的自訂功能變數名稱，在我們的範例中，此 URL 為：
```http
https://my-private-link-speech.cognitiveservices.azure.com/sts/v1.0/issueToken
```
> [!TIP]
> 您可以在 Azure 入口網站中語音資源的 *金鑰和端點* (*資源管理* 群組) 區段中找到此 URL。

###### <a name="create-speechconfig-instance-using-authorization-token"></a>`SpeechConfig`使用授權權杖建立實例

您必須 `SpeechConfig` 使用您在上一節中取得的授權權杖來具現化類別。 假設我們已定義下列變數：

- `token` 包含在上一節中取得的授權權杖
- `azureRegion` 包含語音資源 [區域](regions.md) 的名稱 (範例： `westeurope`) 
- `outError` (僅適用于 [目標 C](/objectivec/cognitive-services/speech/spxspeechconfiguration#initwithauthorizationtokenregionerror) 案例) 

接著，我們需要具現化 `SpeechConfig` 類別，如下所示：
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
> 呼叫端必須確定授權權杖是否有效。 在授權權杖到期之前，呼叫端必須使用新的有效權杖來呼叫這個 setter，以重新整理它。 當建立新的辨識器/合成器時，會複製設定值，新的權杖值將不會套用至已建立的辨識器。 針對之前建立的辨識器/合成器，您必須設定對應辨識器/合成器的授權權杖，以重新整理權杖。 否則，辨識器/合成器會在辨識/合成期間遇到錯誤。

修改之後，您的應用程式應該使用自訂的功能變數名稱啟用的語音資源，而不需要私人端點。 我們正在努力更順暢地支援自訂網域/私人端點案例。

## <a name="pricing"></a>定價

如需定價詳細資料，請參閱 [Azure Private Link 定價](https://azure.microsoft.com/pricing/details/private-link)。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure Private Link](../../private-link/private-link-overview.md)
* 深入瞭解 [語音 SDK](speech-sdk.md)
* 深入瞭解 [語音轉換文字 REST API](rest-speech-to-text.md)
* 深入瞭解 [文字轉換語音 REST API](rest-text-to-speech.md)
