---
title: 針對索引鍵/值使用 JSON 內容類型
titleSuffix: Azure App Configuration
description: 瞭解如何使用 JSON content-type 來輸入索引鍵/值
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: 725beb50e55852e35ee4434539ff158f082059df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121991"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>利用 content-type 將 JSON 索引鍵/值儲存在應用程式設定中

資料會儲存在應用程式設定中做為索引鍵/值，預設會將值視為字串類型。 不過，您可以利用與每個索引鍵/值相關聯的 content-type 屬性來指定自訂類型，如此您就可以保留資料的原始類型，或讓您的應用程式根據內容類型而有不同的行為。


## <a name="overview"></a>概觀

在 [應用程式設定] 中，您可以使用 JSON 媒體類型作為索引鍵-值的內容類型，以使用如下的優點：
- **更簡單的資料管理**：管理索引鍵/值（例如陣列）在 Azure 入口網站中會變得很簡單。
- **增強的資料匯出**：資料匯出期間將會保留基本類型、陣列和 JSON 物件。
- **使用應用程式設定提供者的原生支援**：當應用程式中的應用程式設定提供者程式庫取用時，具有 JSON content-type 的索引鍵/值將可正常運作。

#### <a name="valid-json-content-type"></a>有效的 JSON 內容類型

您可以在 [此處](https://www.iana.org/assignments/media-types/media-types.xhtml)定義的媒體類型，指派給與每個索引鍵/值相關聯的 content-type。
媒體類型包含類型和子類型。 如果類型為， `application` 且子類型 (或尾碼) 為 `json` ，則媒體類型會被視為有效的 JSON 內容類型。
有效 JSON 內容類型的一些範例如下：

- application/json
- 應用程式/活動 + json
- application/vnd.openxmlformats-officedocument.spreadsheetml.sheet. foobar + json; 字元集 = utf-8

#### <a name="valid-json-values"></a>有效的 JSON 值

當索引鍵-值具有 JSON 內容類型時，其值必須是有效的 JSON 格式，用戶端才能正確地進行處理。 否則，用戶端可能會失敗或回復，並將其視為字串格式。
有效 JSON 值的一些範例如下：

- "John Doe"
- 723
- false
- null
- "2020-01-01T12：34： 56.789 Z"
- [1, 2, 3, 4]
- {"ObjectSetting"： {"目標"： {"Default"： true，"Level"： "Information"}}}

> [!NOTE]
> 在本文的其餘部分中，應用程式設定中具有有效 JSON 內容類型和有效 JSON 值的任何索引鍵/值，都將稱為 JSON 索引 **鍵/值**。 

在本教學課程中，您將了解如何：
> [!div class="checklist"]
> * 在應用程式設定中建立 JSON 索引鍵/值。
> * 從 JSON 檔案匯入 JSON 索引鍵/值。
> * 將 JSON 索引鍵/值匯出至 JSON 檔案。
> * 在您的應用程式中使用 JSON 索引鍵/值。


## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)。
- Azure CLI (2.10.0 或更新版本) 的最新版本。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 如果您使用 Azure CLI，則必須先使用登入 `az login` 。 您可以選擇性地使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-an-app-configuration-store"></a>建立應用程式組態存放區

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>在應用程式設定中建立 JSON 索引鍵/值

您可以使用 Azure 入口網站、Azure CLI 或從 JSON 檔案匯入來建立 JSON 索引鍵/值。 在本節中，您會找到使用這三種方法建立相同 JSON 索引鍵值的指示。

### <a name="create-json-key-values-using-azure-portal"></a>使用 Azure 入口網站建立 JSON 索引鍵/值

流覽至您的應用程式設定存放區，然後選取 [ **Configuration Explorer**  >  **建立**索引  >  **鍵/值**]，以新增下列索引鍵/值組：

| 機碼 | 值 | 內容類型 |
|---|---|---|
| 設定： BackgroundColor | 亮起 | application/json |
| 設定： FontSize | 24 | application/json |
| 設定： UseDefaultRouting | false | application/json |
| 設定： BlockedUsers | null | application/json |
| 設定： ReleaseDate | "2020-08-04T12：34： 56.789 Z" | application/json |
| 設定： RolloutPercentage | [25，50，75100] | application/json |
| 設定：記錄 | {"Test"： {"Level"： "Debug"}，"生產"： {"Level"： "Warning"}} | application/json |

將 [ **標籤** ] 保留空白， **然後選取 [** 套用]。

### <a name="create-json-key-values-using-azure-cli"></a>使用 Azure CLI 建立 JSON 索引鍵/值

下列命令會在您的應用程式設定存放區中建立 JSON 索引鍵/值。 `<appconfig_name>`以您的應用程式設定存放區名稱取代。

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> 如果您使用 Azure CLI 或 Azure Cloud Shell 來建立 JSON 索引鍵/值，則提供的值必須是已轉義的 JSON 字串。

### <a name="import-json-key-values-from-a-file"></a>從檔案匯入 JSON 索引鍵/值

使用下列內容建立名為的 JSON 檔案 `Import.json` ，並以索引鍵/值形式匯入至應用程式設定：

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> 自 `--depth` 變數是用來將檔案中的階層式資料簡維化成成對的索引鍵/值組。 在本教學課程中，會指定深度來示範您也可以將 JSON 物件儲存為應用程式設定中的值。 如果未指定 depth，預設會將 JSON 物件壓平合併為最深的層級。

您在應用程式設定中所建立的 JSON 索引鍵/值看起來應該像這樣：

![包含 JSON 索引鍵/值的 Config 存放區](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>將 JSON 索引鍵/值匯出至檔案

使用 JSON 索引鍵/值的其中一個主要優點是，可以在匯出時保留值的原始資料類型。 如果應用程式設定中的索引鍵/值沒有 JSON 內容類型，則會將其值視為字串。 

請考慮這些索引鍵/值，但不要輸入 JSON content-type：

| 機碼 | 值 | 內容類型 |
|---|---|---|
| 設定： FontSize | 24 | |
| 設定： UseDefaultRouting | false | |

當您將這些索引鍵/值匯出至 JSON 檔案時，這些值將會匯出為字串：
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

但是，當您將 JSON 索引鍵/值匯出至檔案時，所有值都會保留其原始資料類型。 若要確認這一點，請將索引鍵/值從您的應用程式設定匯出至 JSON 檔案。 您會看到匯出的檔案與您先前匯入的檔案具有相同的內容 `Import.json` 。

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> 如果您的應用程式設定存放區有一些沒有 JSON content-type 的索引鍵/值，則它們也會以字串格式匯出至相同檔案。 如果您只想匯出 JSON 索引鍵/值，請在 JSON 索引鍵/值中指派唯一的標籤或前置詞，並在匯出期間使用標籤或前置詞篩選。


## <a name="consuming-json-key-values-in-applications"></a>使用應用程式中的 JSON 索引鍵/值

在應用程式中取用 JSON 索引鍵/值最簡單的方式是透過應用程式設定提供者程式庫。 使用提供者程式庫時，您不需要在應用程式中執行 JSON 索引鍵/值的特殊處理。 它們一律會針對您的應用程式還原序列化，就像其他 JSON 設定提供者程式庫一樣。 

> [!Important]
> JSON 索引鍵/值的原生支援可在 .NET 設定提供者版本 4.0.0 (或更新版本) 中取得。 請參閱 [*後續步驟*](#next-steps) 一節，以取得詳細資料。

如果您使用 SDK 或 REST API 從應用程式設定讀取索引鍵/值，則根據內容類型，您的應用程式會負責使用任何標準 JSON 還原序列化程式來還原序列化 JSON 索引鍵/值的值。


## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何在您的應用程式設定存放區中使用 JSON 索引鍵/值，請建立應用程式來使用這些索引鍵/值：

* [ASP.NET Core 快速入門](./quickstart-aspnet-core-app.md)
    * 必要條件： [AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) package v 4.0.0 或更新版本。

* [.NET Core 快速入門](./quickstart-dotnet-core-app.md)
    * 先決條件： [Microsoft.Extensions.Configuration。AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) 套件 v 4.0.0 或更新版本。
