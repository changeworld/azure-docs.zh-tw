---
title: 訂購 Azure 資料箱的教學課程 | Microsoft Docs
description: 在本教學課程中，了解 Azure 資料箱 (這是一種混合式解決方案，可讓您將內部部署資料匯入至 Azure)，以及如何訂購 Azure 資料箱。
services: databox
author: twooley
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/21/2020
ms.author: twooley
ms.openlocfilehash: 2000ecc84a92bef5ad6b80fecde4aee0157e4bc5
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783564"
---
# <a name="tutorial-order-azure-data-box"></a>教學課程：訂購 Azure 資料箱

Azure 資料箱是一項混合式解決方案，可讓您以快速、簡便而可靠的方式，將內部部署資料匯入 Azure 中。 您可將資料傳輸至 Microsoft 提供的 80 TB (可用容量) 儲存裝置，然後將裝置寄回。 這項資料隨後會上傳至 Azure。

本教學課程說明如何訂購 Azure 資料箱。 在本教學課程中，您會了解：

> [!div class="checklist"]
>
> * 部署資料箱的必要條件
> * 訂購資料箱
> * 追蹤訂單狀態
> * 取消訂單

## <a name="prerequisites"></a>必要條件

# <a name="portal"></a>[入口網站](#tab/portal)

在您部署裝置之前，請完成下列資料箱服務與裝置的組態必要條件：

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

您可以登入 Azure，並且以下列兩種方式之一執行 Azure CLI 命令：

* 您可以安裝 CLI，並在本機執行 CLI 命令。
* 您可以從 Azure 入口網站，在 Azure Cloud Shell 中執行 CLI 命令。

我們會燒教學課程中透過 Windows PowerShell 使用 Azure CLI，但您可以隨意選擇任一選項。

### <a name="for-azure-cli"></a>對於 Azure CLI

在您開始前，請確定：

#### <a name="install-the-cli-locally"></a>在本機安裝 CLI

* 安裝 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.67 版或更新版本)。 或者，您可以[使用 MSI 安裝](https://aka.ms/installazurecliwindows)。

**登入 Azure**

開啟 Windows PowerShell 命令視窗，並使用 [az login](/cli/azure/reference-index#az-login) 命令登入 Azure：

```azurecli
PS C:\Windows> az login
```

以下是成功登入的輸出：

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**安裝 Azure 資料箱 CLI 延伸模組**

您必須先安裝延伸模組，才可以使用 Azure 資料箱 CLI 命令。 Azure CLI 延伸模組可讓您存取核心 CLI 尚未隨附的實驗性與發行前版本命令。 如需延伸模組詳細資訊，請參閱[使用 Azure CLI 延伸模組](/cli/azure/azure-cli-extensions-overview)。

若要安裝 Azure 資料箱延伸模組，請執行下列命令：`az extension add --name databox`：

```azurecli

    PS C:\Windows> az extension add --name databox
```

如果延伸模組已安裝成功，您會看到下列輸出：

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>使用 Azure Cloud Shell

您可以使用 [Azure Cloud Shell](https://shell.azure.com/)，這是裝載於 Azure 中的互動式殼層環境，在瀏覽器中執行 CLI 命令。 Azure Cloud Shell 支援 Bash 或 Windows PowerShell 搭配 Azure 服務。 Azure CLI 可預先安裝和設定，以便與您的帳戶搭配使用。 選取 Azure 入口網站右上方功能表上的 [Cloud Shell] 按鈕：

![Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

按鈕會啟動互動式殼層，可讓您用來執行本操作說明文章中所述的步驟。

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>對於 Azure PowerShell

在您開始前，請確定您已經：

* 安裝 Windows PowerShell 6.2.4 或更新版本。
* 安裝 Azure PowerShell (AZ) 模組。
* 安裝 Azure 資料箱 (Az.DataBox) 模組。
* 登入 Azure。

#### <a name="install-azure-powershell-and-modules-locally"></a>在本機安裝 Azure PowerShell 和模組

**安裝或升級 Windows PowerShell**

您將需要安裝 Windows PowerShell 6.2.4 版或更新版本。 若要找出您已安裝的 PowerShell 版本，請執行：`$PSVersionTable`。

您會看到下列輸出︰

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

如果您的版本低於 6.2.4，則需要升級您的 Windows PowerShell 版本。 如需安裝最新版的 Windows PowerShell，請參閱[安裝 Azure PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7)。

**安裝 Azure PowerShell 和資料箱模組**

您將需要安裝 Azure PowerShell 模組，才能使用 Azure PowerShell 來訂購 Azure 資料箱。 安裝 Azure PowerShell 模組：

1. 安裝 [Azure PowerShell Az 模組](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)。
2. 然後，使用 `Install-Module -Name Az.DataBox` 命令安裝 Az.DataBox。

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>登入 Azure

開啟 Windows PowerShell 命令視窗，並使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount) \(英文\) 命令登入 Azure：

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

以下是成功登入的輸出：

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

如需如何使用 Windows PowerShell 登入 Azure 的詳細資訊，請參閱[使用 Azure PowerShell 登入](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。

---

## <a name="order-data-box"></a>訂購資料箱

# <a name="portal"></a>[入口網站](#tab/portal)

請在 Azure 入口網站中執行下列步驟，以訂購裝置。

1. 使用您的 Microsoft Azure 認證在以下 URL 登入：[https://portal.azure.com](https://portal.azure.com)。
2. 選取 [+ 建立資源]，然後搜尋「Azure 資料箱」。 選取 [Azure 資料箱]。

   ![選取 [Azure 資料箱]](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. 選取 [建立]。

   ![選取 [Azure 資料箱]](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. 確認您的區域是否適用資料箱服務。 輸入或選取下列資訊，然後選取 [套用]。

    |設定  |值  |
    |---------|---------|
    |傳輸類型     | 選取 [匯入至 Azure]。        |
    |訂用帳戶     | 為資料箱服務選取 EA、CSP 或 Azure 贊助訂用帳戶。 <br> 訂用帳戶會連結到您的帳單帳戶。       |
    |資源群組 | 選取現有的資源群組。 資源群組是適用於資源而可一併管理或部署的邏輯容器： |
    |來源國家/區域    |    選取您的資料目前所在的國家/地區。         |
    |目的地 Azure 區域     |     選取要傳輸資料的 Azure 區域。 <br> 如需詳細資訊，請移至[區域可用性](data-box-overview.md#region-availability)。            |

    [![Azure 資料箱匯入訂單](media/data-box-deploy-ordered/select-data-box-import-04b.png)](media/data-box-deploy-ordered/select-data-box-import-04b.png#lightbox)

5. 選取 [資料箱]。 單一訂單的最大可用容量是 80 TB。 您可以建立多份訂單以訂購更大的資料大小。

    ![選取資料箱選項 1](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. 在 [訂購] 中，移至 [基本] 索引標籤。輸入或選取下列資訊，然後選取 [下一步:資料目的地>]。

    |設定  |值  |
    |---------|---------|
    |訂用帳戶      | 訂用帳戶會根據您稍早的選取項目自動填入。|
    |資源群組    | 您先前選取的資源群組。 |
    |匯入訂單名稱 | 提供用來追蹤訂單的易記名稱。 <br> 名稱長度可介於 3 到 24 個字元之間，且可以是字母、數字和連字號。 <br> 名稱必須以字母或數字為開頭或結尾。    |

    ![選取資料箱選項 1](media/data-box-deploy-ordered/select-data-box-import-06.png)

    根據預設，裝置解除鎖定密碼會使用 Microsoft 管理的金鑰進行加密。 完成訂單之後，您可以新增客戶自控金鑰。 客戶自控金鑰可讓您使用來自 Azure Key Vault、屬於您自己的金鑰，以保護您的裝置解除鎖定密碼。 如需詳細資訊，請參閱[針對 Azure 資料箱使用 Azure Key Vault 中的客戶自控金鑰](data-box-customer-managed-encryption-key-portal.md)。

7. 在 [資料目的地] 索引標籤中，選取 [資料目的地]。

    如果使用**儲存體帳戶**作為儲存體目的地，就會看到下列螢幕擷取畫面：

    ![Azure 資料箱資料目的地](media/data-box-deploy-ordered/select-data-box-import-07.png)

    根據指定的 Azure 區域，從現有儲存體帳戶的篩選清單中選取一或多個儲存體帳戶。 資料箱可以與最多 10 個儲存體帳戶連結。 您也可以建立新的**一般用途 v1**、**一般用途 v2** 或 **Blob 儲存體帳戶**。

    支援具有虛擬網路的儲存體帳戶。 若要允許資料箱服務使用受保護的儲存體帳戶來運作，請在儲存體帳戶網路防火牆設定內啟用受信任的服務。 如需詳細資訊，請參閱如何[新增 Azure 資料箱作為受信任的服務](../storage/common/storage-network-security.md#exceptions)。

    如果使用資料箱以從內部部署虛擬硬碟 (VHD) 建立**受控磁碟**，您也必須提供下列資訊：

    |設定  |值  |
    |---------|---------|
    |資源群組     | 如果您想要從內部部署 VHD 建立受控磁碟，請建立新的資源群組。 只有當資源群組是在資料箱服務建立受控磁碟的資料箱訂單之前建立的，您才能使用現有的資源群組。 <br> 指定以分號分隔的多個資源群組。 最多支援 10 個資源群組。|

    ![受控磁碟的資料箱訂單](media/data-box-deploy-ordered/select-data-box-import-07b.png)

    針對受控磁碟指定的儲存體帳戶不能當成暫存的儲存體帳戶來使用。 資料箱服務會先將 VHD 以分頁 Blob 形式上傳至暫存的儲存體帳戶，然後再將它轉換為受控磁碟並移至資源群組。 如需詳細資訊，請參閱[確認資料上傳至 Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure)。

    完成時，選取 [下一步:連絡人詳細資料] 以繼續進行。

8. 在 [連絡人詳細資料] 中，選取 [+ 新增交貨位址]。

    ![受控磁碟的資料箱訂單](media/data-box-deploy-ordered/select-data-box-import-08a.png)

9. 在 [交貨地址] 中，提供您的姓名、公司的名稱和郵寄地址，以及有效的電話號碼。 選取 [驗證地址]。 服務會驗證交貨地址以確認服務可用性。 如果服務可提供至指定的交貨地址，您將會收到該項通知。

   ![受控磁碟的資料箱訂單](media/data-box-deploy-ordered/select-data-box-import-10.png)

   成功下達訂單後，如果選取自我管理運送，您會收到電子郵件通知。 如需有關自我管理運送的詳細資訊，請參閱[使用自我管理運送](data-box-portal-customer-managed-shipping.md)。

10. 成功驗證出貨詳細資料之後，選取 [新增交貨位址]。 您將返回 [連絡人詳細資料] 索引標籤。

11. 當您返回 [連絡人詳細資料] 之後，新增一或多個電子郵件地址。 服務會將關於任何訂單狀態更新的電子郵件通知傳送至指定的電子郵件地址。

    建議您使用群組電子郵件，以便在群組中的管理員離開時繼續接收通知。

    ![受控磁碟的資料箱訂單](media/data-box-deploy-ordered/select-data-box-import-08c.png)

12. 在 [檢閱 + 訂購] 中，檢閱與訂單、連絡人、通知和隱私權條款相關的資訊。 請勾選隱私權條款合約的對應方塊。

13. 選取 [訂單]。 建立訂單需要幾分鐘的時間。

    ![受控磁碟的資料箱訂單](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

請使用 Azure CLI 執行下列步驟，以訂購裝置：

1. 記下您資料箱訂單的設定。 這些設定包括您的個人/商務資訊、訂用帳戶名稱、裝置資訊和出貨資訊。 執行 CLI 命令建立資料箱訂單時，您將需要使用這些設定作為參數。 下表顯示用於 `az databox job create` 的參數設定：

   | 設定 (參數) | 描述 |  範例值 |
   |---|---|---|
   |resource-group| 使用現有的群組或建立新群組。 資源群組是適用於資源而可一併管理或部署的邏輯容器： | "myresourcegroup"|
   |NAME| 您建立的訂單名稱。 | "mydataboxorder"|
   |contact-name| 與出貨地址相關聯的名稱。 | "Gus Poland"|
   |電話| 將接收訂單之人員或公司的電話號碼。| "14255551234"
   |location| 將寄送裝置的最接近 Azure 區域。| "US West"|
   |sku| 您訂購的特定資料箱裝置。 有效值為："DataBox"、"DataBoxDisk" 和 "DataBoxHeavy"| "DataBox" |
   |email-list| 與訂單相關聯的電子郵件地址。| "gusp@contoso.com" |
   |street-address1| 訂單送達的街道位址。 | "15700 NE 39th St" |
   |street-address2| 次要位址資訊，例如公寓號碼或建築物編號。 | "Bld 123" |
   |city| 裝置送達的城市。 | "Redmond" |
   |state-or-province| 裝置送達的州/省。| "WA" |
   |country| 裝置送達的國家/地區。 | "United States" |
   |postal-code| 與寄送地址相關聯的郵遞區號。| "98052"|
   |company-name| 您工作的公司名稱。| "Contoso, LTD" |
   |storage account| 您要匯入資料的 Azure 儲存體帳戶。| "mystorageaccount"|
   |debug| 包含要詳細記錄的偵錯資訊  | --debug |
   |help| 顯示此命令的說明資訊。 | --help -h |
   |only-show-errors| 只顯示錯誤，隱藏警告。 | --only-show-errors |
   |output -o| 設定輸出格式。  允許的值：json、jsonc、none、table、tsv、yaml、 yamlc。 預設值為 json。 | --output "json" |
   |查詢| JMESPath 查詢字串。 如需詳細資訊，請參閱 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括詳細資訊記錄。 | --verbose |

2. 在您選擇的命令提示字元或終端機中，使用 [az databox job create](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-create) \(英文\) 來建立您的 Azure 資料箱訂單。

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   以下是命令使用方式的範例：

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   以下是執行此命令的輸出：

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. 除非您變更，否則所有 Azure CLI 命令預設都會使用 json 作為輸出格式。 您可以使用全域參數 `--output <output-format>` 來變更輸出格式。 將格式變更為 "table" 可改善輸出可讀性。

   以下是我們剛才執行的相同命令，內容已稍微調整以變更格式：

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   以下是執行此命令的輸出：

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

使用 Azure PowerShell 執行下列步驟，以訂購裝置：

1. 建立匯入訂單之前，您必須先取得儲存體帳戶，並將儲存體帳戶物件儲存於變數中。

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. 記下您資料箱訂單的設定。 這些設定包括您的個人/商務資訊、訂用帳戶名稱、裝置資訊和出貨資訊。 執行 PowerShell 命令以建立資料箱訂單時，您將需要使用這些設定作為參數。 下表顯示用於 [New-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/New-AzDataBoxJob) \(英文\) 的參數設定。

    | 設定 (參數) | 描述 |  範例值 |
    |---|---|---|
    |ResourceGroupName [必要]| 使用現有的資源群組。 資源群組是適用於資源而可一併管理或部署的邏輯容器： | "myresourcegroup"|
    |Name [必要]| 您建立的訂單名稱。 | "mydataboxorder"|
    |ContactName [必要]| 與出貨地址相關聯的名稱。 | "Gus Poland"|
    |PhoneNumber [必要]| 將接收訂單之人員或公司的電話號碼。| "14255551234"
    |Location [必要]| 將寄送裝置的最接近 Azure 區域。| "WestUS"|
    |DataBoxType [必要]| 您訂購的特定資料箱裝置。 有效值為："DataBox"、"DataBoxDisk" 和 "DataBoxHeavy"| "DataBox" |
    |EmailId [必要]| 與訂單相關聯的電子郵件地址。| "gusp@contoso.com" |
    |StreetAddress1 [必要]| 訂單送達的街道位址。 | "15700 NE 39th St" |
    |StreetAddress2| 次要位址資訊，例如公寓號碼或建築物編號。 | "Bld 123" |
    |StreetAddress3| 第三個地址資訊。 | |
    |City [必要]| 裝置送達的城市。 | "Redmond" |
    |StateOrProvinceCode [必要]| 裝置送達的州/省。| "WA" |
    |CountryCode [必要]| 裝置送達的國家/地區。 | "United States" |
    |PostalCode [必要]| 與寄送地址相關聯的郵遞區號。| "98052"|
    |CompanyName| 您工作的公司名稱。| "Contoso, LTD" |
    |StorageAccountResourceId [必要]| 您想要匯入資料的 Azure 儲存體帳戶識別碼。| <AzStorageAccount>.id |

3. 在您選擇的命令提示字元或終端機中，使用 [New-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/New-AzDataBoxJob) \(英文\) 來建立您的 Azure 資料箱訂單。

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   以下是執行此命令的輸出：

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>追蹤訂單狀態

# <a name="portal"></a>[入口網站](#tab/portal)

在您下訂單之後，可以從 Azure 入口網站來追蹤訂單狀態。 請移至您的資料箱訂單，然後移至 [概觀] 以檢視狀態。 入口網站會顯示處於 [已訂購] 狀態的訂單。

如果裝置無法使用，您會收到通知。 如果可使用服務，Microsoft 會識別要寄送的裝置，並準備出貨。 在裝置準備期間，會執行下列動作：

* 系統會針對與裝置相關聯的每個儲存體帳戶建立 SMB 共用。
* 針對每個共用，會產生例如使用者名稱和密碼的存取認證。
* 也會產生可協助將裝置解除鎖定的裝置密碼。
* 資料箱會鎖定，防止任何對裝置的未經授權存取。

裝置準備完成後，入口網站會顯示訂單處於 [已處理] 狀態。

![資料箱訂單已處理](media/data-box-overview/data-box-order-status-processed.png)

接著，Microsoft 會準備裝置，並透過區域貨運公司派送給您。 在裝置送達後，您會收到追蹤號碼。 入口網站會顯示訂單處於 [已分派] 狀態。

![資料箱訂單分派](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="track-a-single-order"></a>追蹤單一訂單

若要取得單一現有 Azure 資料箱訂單的追蹤資訊，請執行 [az databox job show](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-show)。 此命令會顯示訂單的相關資訊，例如 (但不限於)：名稱、資源群組、追蹤資訊、訂用帳戶識別碼、連絡人資訊、出貨類型，以及裝置 SKU。

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   下表顯示 `az databox job show` 的參數資訊：

   | 參數 | 描述 |  範例值 |
   |---|---|---|
   |resource-group [Required]| 與訂單相關聯的資源群組名稱。 資源群組是適用於資源而可一併管理或部署的邏輯容器： | "myresourcegroup"|
   |name [Required]| 要顯示的訂單名稱。 | "mydataboxorder"|
   |debug| 包含要詳細記錄的偵錯資訊 | --debug |
   |help| 顯示此命令的說明資訊。 | --help -h |
   |only-show-errors| 只顯示錯誤，隱藏警告。 | --only-show-errors |
   |output -o| 設定輸出格式。  允許的值：json、jsonc、none、table、tsv、yaml、 yamlc。 預設值為 json。 | --output "json" |
   |查詢| JMESPath 查詢字串。 如需詳細資訊，請參閱 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括詳細資訊記錄。 | --verbose |

   以下是輸出格式設定為 "table" 的命令範例：

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   以下是執行此命令的輸出：

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> 訂用帳戶層級可以支援列出訂單，使資源群組成為選用參數 (而不是必要參數)。

### <a name="list-all-orders"></a>列出所有訂單

如果您已訂購多個裝置，可以執行 [az databox job list](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-list) 來檢視所有 Azure 資料箱訂單。 此命令會列出屬於特定資源群組的所有訂單。 同時也會顯示在輸出中顯示以下資訊：訂單名稱、運送狀態、Azure 區域、交貨類型、訂單狀態。 已取消的訂單也會包含在清單中。
此命令也會顯示每個訂單的時間戳記。

```azurecli
az databox job list --resource-group <resource-group>
```

下表顯示 `az databox job list` 的參數資訊：

   | 參數 | 描述 |  範例值 |
   |---|---|---|
   |resource-group [Required]| 包含訂單的資源群組名稱。 資源群組是適用於資源而可一併管理或部署的邏輯容器： | "myresourcegroup"|
   |debug| 包含要詳細記錄的偵錯資訊 | --debug |
   |help| 顯示此命令的說明資訊。 | --help -h |
   |only-show-errors| 只顯示錯誤，隱藏警告。 | --only-show-errors |
   |output -o| 設定輸出格式。  允許的值：json、jsonc、none、table、tsv、yaml、 yamlc。 預設值為 json。 | --output "json" |
   |查詢| JMESPath 查詢字串。 如需詳細資訊，請參閱 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括詳細資訊記錄。 | --verbose |

   以下是輸出格式設定為 "table" 的命令範例：

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   以下是執行此命令的輸出：

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>追蹤單一訂單

若要取得有關單一現有 Azure 資料箱訂單的追蹤資訊，請執行 [Get-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/Get-AzDataBoxJob) \(英文\)。 此命令會顯示訂單的相關資訊，例如 (但不限於)：名稱、資源群組、追蹤資訊、訂用帳戶識別碼、連絡人資訊、出貨類型，以及裝置 SKU。

> [!NOTE]
> `Get-AzDataBoxJob` 用於顯示單一和多張訂單。 不同之處在於您可以針對單一訂單指定訂單名稱。

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   下表顯示 `Get-AzDataBoxJob` 的參數資訊：

   | 參數 | 描述 |  範例值 |
   |---|---|---|
   |ResourceGroup [必要]| 與訂單相關聯的資源群組名稱。 資源群組是適用於資源而可一併管理或部署的邏輯容器： | "myresourcegroup"|
   |Name [必要]| 要取得資訊的訂單名稱。 | "mydataboxorder"|
   |ResourceId| 與訂單相關聯的資源識別碼。 |  |

   以下是具有輸出的命令範例：

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   以下是執行此命令的輸出：

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>列出所有訂單

如果您已訂購多個裝置，則可以執行 [Get-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/Get-AzDataBoxJob) \(英文\) 來檢視您的所有 Azure 資料箱訂單。 此命令會列出屬於特定資源群組的所有訂單。 同時也會顯示在輸出中顯示以下資訊：訂單名稱、運送狀態、Azure 區域、交貨類型、訂單狀態。 已取消的訂單也會包含在清單中。
此命令也會顯示每個訂單的時間戳記。

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

以下是命令的範例：

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

以下是執行此命令的輸出：

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>取消訂單

# <a name="portal"></a>[入口網站](#tab/portal)

若要取消此訂單，請在 Azure 入口網站中移至 [概觀]，然後從命令列選取 [取消]。

下訂單之後，您可以在訂單狀態標示為已處理之前的任何時間點，取消訂單。

若要刪除已取消的訂單，請移至 [概觀]，然後從命令列選取 [刪除]。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cancel-an-order"></a>取消訂單

若要取消 Azure 資料箱訂單，請執行 [az databox job cancel](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-cancel)。 您必須指定取消訂單的原因。

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   下表顯示 `az databox job cancel` 的參數資訊：

   | 參數 | 描述 |  範例值 |
   |---|---|---|
   |resource-group [Required]| 與要刪除之訂單相關聯的資源群組名稱。 資源群組是適用於資源而可一併管理或部署的邏輯容器： | "myresourcegroup"|
   |name [Required]| 要刪除的訂單名稱。 | "mydataboxorder"|
   |reason [Required]| 取消訂單的原因。 | 「我輸入了錯誤的資訊，而且需要取消訂單。」 |
   |是| 不提示確認。 | --yes (-y)| --yes -y |
   |debug| 包含要詳細記錄的偵錯資訊 | --debug |
   |help| 顯示此命令的說明資訊。 | --help -h |
   |only-show-errors| 只顯示錯誤，隱藏警告。 | --only-show-errors |
   |output -o| 設定輸出格式。  允許的值：json、jsonc、none、table、tsv、yaml、 yamlc。 預設值為 json。 | --output "json" |
   |查詢| JMESPath 查詢字串。 如需詳細資訊，請參閱 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括詳細資訊記錄。 | --verbose |

   以下是具有輸出的命令範例：

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   以下是執行此命令的輸出：

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>順序訂單

如果您已取消 Azure 資料箱訂單，可以執行 [az databox job delete](https://docs.microsoft.com/cli/azure/ext/databox/databox/job?view=azure-cli-latest#ext-databox-az-databox-job-delete) 以刪除訂單。

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   下表顯示 `az databox job delete` 的參數資訊：

   | 參數 | 描述 |  範例值 |
   |---|---|---|
   |resource-group [Required]| 與要刪除之訂單相關聯的資源群組名稱。 資源群組是適用於資源而可一併管理或部署的邏輯容器： | "myresourcegroup"|
   |name [Required]| 要刪除的訂單名稱。 | "mydataboxorder"|
   |訂用帳戶| 您 Azure 訂用帳戶的名稱或識別碼 (GUID)。 | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |是| 不提示確認。 | --yes (-y)| --yes -y |
   |debug| 包含要詳細記錄的偵錯資訊 | --debug |
   |help| 顯示此命令的說明資訊。 | --help -h |
   |only-show-errors| 只顯示錯誤，隱藏警告。 | --only-show-errors |
   |output -o| 設定輸出格式。  允許的值：json、jsonc、none、table、tsv、yaml、 yamlc。 預設值為 json。 | --output "json" |
   |查詢| JMESPath 查詢字串。 如需詳細資訊，請參閱 [JMESPath](http://jmespath.org/)。 | --query <string>|
   |verbose| 包括詳細資訊記錄。 | --verbose |

以下是具有輸出的命令範例：

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   以下是執行此命令的輸出：

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>取消訂單

若要取消 Azure 資料箱訂單，請執行 [Stop-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/stop-azdataboxjob) \(英文\)。 您必須指定取消訂單的原因。

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

下表顯示 `Stop-AzDataBoxJob` 的參數資訊：

| 參數 | 描述 |  範例值 |
|---|---|---|
|ResourceGroup [必要]| 與要取消之訂單相關聯的資源群組名稱。 資源群組是適用於資源而可一併管理或部署的邏輯容器： | "myresourcegroup"|
|Name [必要]| 要刪除的訂單名稱。 | "mydataboxorder"|
|Reason [必要]| 取消訂單的原因。 | 「我輸入了錯誤的資訊，而且需要取消訂單。」 |
|Force | 強制 Cmdlet 執行，而不需使用者確認。 | -Force |

以下是具有輸出的命令範例：

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

以下是執行此命令的輸出：

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>順序訂單

如果您已取消 Azure 資料箱訂單，則可以執行 [Remove-AzDataBoxJob](https://docs.microsoft.com/powershell/module/az.databox/remove-azdataboxjob) \(英文\) 以刪除訂單。

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

下表顯示 `Remove-AzDataBoxJob` 的參數資訊：

| 參數 | 描述 |  範例值 |
|---|---|---|
|ResourceGroup [必要]| 與要刪除之訂單相關聯的資源群組名稱。 資源群組是適用於資源而可一併管理或部署的邏輯容器： | "myresourcegroup"|
|Name [必要]| 要刪除的訂單名稱。 | "mydataboxorder"|
|Force | 強制 Cmdlet 執行，而不需使用者確認。 | -Force |

以下是具有輸出的命令範例：

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

以下是執行此命令的輸出：

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解 Azure 資料箱的相關文章，像是：

> [!div class="checklist"]
>
> * 部署資料箱的必要條件
> * 訂購資料箱
> * 追蹤訂單狀態
> * 取消訂單

請繼續進行下一個教學課程，以了解如何設定資料箱。

> [!div class="nextstepaction"]
> [設定您的 Azure 資料箱](./data-box-deploy-set-up.md)
