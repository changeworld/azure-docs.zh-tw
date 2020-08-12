---
title: 針對索引鍵/值使用 JSON 內容類型
titleSuffix: Azure App Configuration
description: 瞭解如何使用索引鍵/值的 JSON 內容類型
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: 725beb50e55852e35ee4434539ff158f082059df
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121991"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>利用 content-type 將 JSON 索引鍵/值儲存在應用程式組態

資料會以索引鍵/值的形式儲存在應用程式組態中，其中的值預設會視為字串類型。 不過，您可以利用與每個索引鍵值相關聯的 content-type 屬性來指定自訂類型，讓您可以保留資料的原始類型，或讓您的應用程式根據內容類型而有不同的行為。


## <a name="overview"></a>總覽

在應用程式組態中，您可以使用 JSON 媒體類型做為金鑰值的內容類型，以獲得如下的優點：
- **較簡單的資料管理**：管理索引鍵/值（例如陣列）在 Azure 入口網站中會變得很容易。
- **增強的資料匯出**：在資料匯出期間，將會保留基本型別、陣列和 JSON 物件。
- **應用程式組態提供者的原生支援**：在應用程式中應用程式組態提供者程式庫取用時，JSON 內容類型的索引鍵/值將可正常使用。

#### <a name="valid-json-content-type"></a>有效的 JSON 內容類型

如[這裡](https://www.iana.org/assignments/media-types/media-types.xhtml)所定義的媒體類型，可以指派給與每個索引鍵/值相關聯的內容類型。
媒體類型包含類型和子類型。 如果類型為， `application` 且子類型 (或尾碼) 為 `json` ，則媒體類型會被視為有效的 JSON 內容類型。
有效 JSON 內容類型的一些範例如下：

- application/json
- 應用程式/活動 + json
- application/application. foobar + json; 字元集 = utf-8

#### <a name="valid-json-values"></a>有效的 JSON 值

當索引鍵/值具有 JSON 內容類型時，其值必須是有效的 JSON 格式，用戶端才能正確地處理它。 否則，用戶端可能會失敗或切換回，並將其視為字串格式。
有效 JSON 值的一些範例如下：

- "John Doe"
- 723
- false
- null
- "2020-01-01T12：34： 56.789 Z"
- [1, 2, 3, 4]
- {"ObjectSetting"： {"目標"： {"Default"： true，"Level"： "Information"}}}

> [!NOTE]
> 在本文的其餘部分，應用程式組態中具有有效 JSON 內容類型和有效 JSON 值的索引鍵/值，將稱為**JSON 索引鍵/值**。 

您將在本教學課程中了解如何：
> [!div class="checklist"]
> * 在應用程式組態中建立 JSON 索引鍵/值。
> * 從 JSON 檔案匯入 JSON 索引鍵/值。
> * 將 JSON 索引鍵/值匯出至 JSON 檔案。
> * 在您的應用程式中使用 JSON 索引鍵/值。


## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)。
- 最新版的 Azure CLI (2.10.0 或更新版本) 。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 如果您使用 Azure CLI，則必須先使用登入 `az login` 。 您可以選擇性地使用 Azure Cloud Shell。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-an-app-configuration-store"></a>建立應用程式組態存放區

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>在應用程式組態中建立 JSON 索引鍵/值

JSON 索引鍵-值可以使用 Azure 入口網站、Azure CLI 或從 JSON 檔案匯入來建立。 在本節中，您會找到使用這三種方法來建立相同 JSON 索引鍵/值的指示。

### <a name="create-json-key-values-using-azure-portal"></a>使用 Azure 入口網站建立 JSON 索引鍵/值

流覽至您的應用程式組態存放區，**然後選取 [** 設定] [  >  **建立**索引  >  **鍵-值**] 來新增下列索引鍵/值組：

| 機碼 | 值 | 內容類型 |
|---|---|---|
| 設定： BackgroundColor | 環保 | application/json |
| 設定： FontSize | 24 | application/json |
| 設定： UseDefaultRouting | false | application/json |
| 設定： BlockedUsers | null | application/json |
| 設定： ReleaseDate | "2020-08-04T12：34： 56.789 Z" | application/json |
| 設定： RolloutPercentage | [25，50，75100] | application/json |
| 設定：記錄 | {"Test"： {"Level"： "Debug"}，"生產"： {"Level"： "Warning"}} | application/json |

將 [**標籤**] 保留空白，**然後選取 [** 套用]。

### <a name="create-json-key-values-using-azure-cli"></a>使用 Azure CLI 建立 JSON 索引鍵/值

下列命令會在您的應用程式組態存放區中建立 JSON 索引鍵/值。 `<appconfig_name>`以您應用程式組態存放區的名稱取代。

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

使用下列內容建立名為的 JSON 檔案 `Import.json` ，並將索引鍵/值匯入應用程式組態：

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
> `--depth`引數是用來從檔案將階層式資料簡維至索引鍵/值組。 在本教學課程中，會指定深度以示範您也可以將 JSON 物件儲存為應用程式組態中的值。 如果未指定深度，JSON 物件預設會壓平合併至最深的層級。

您所建立的 JSON 索引鍵-值在應用程式組態中看起來應該像這樣：

![包含 JSON 索引鍵/值的設定存放區](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>將 JSON 索引鍵/值匯出至檔案

使用 JSON 索引鍵/值的其中一個主要優點是，可以在匯出時保留值的原始資料類型。 如果應用程式組態中的索引鍵/值沒有 JSON content-type，則會將其值視為字串。 

請考慮下列沒有 JSON 內容類型的索引鍵-值：

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

不過，當您將 JSON 索引鍵/值匯出至檔案時，所有的值都會保留其原始資料類型。 若要確認這一點，請將索引鍵/值從您的應用程式組態匯出至 JSON 檔案。 您會看到匯出的檔案與您先前匯入的檔案具有相同的內容 `Import.json` 。

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> 如果您的應用程式組態存放區具有某些索引鍵/值，而沒有 JSON 內容類型，它們也會以字串格式匯出至相同的檔案。 如果您只想匯出 JSON 索引鍵/值，請將唯一的標籤或前置詞指派給 JSON 索引鍵值，並在匯出期間使用標籤或前置詞篩選。


## <a name="consuming-json-key-values-in-applications"></a>使用應用程式中的 JSON 索引鍵/值

在您的應用程式中取用 JSON 索引鍵/值的最簡單方式是透過應用程式組態提供者程式庫。 有了提供者程式庫，您就不需要在應用程式中執行 JSON 索引鍵/值的特殊處理。 它們一律會以其他 JSON 設定提供者程式庫執行的相同方式還原序列化應用程式。 

> [!Important]
> JSON 索引鍵/值的原生支援可在 .NET 設定提供者版本4.0.0 中取得 (或更新版本的) 。 如需詳細資訊，請參閱[*後續步驟*](#next-steps)一節。

如果您使用 SDK 或 REST API 從應用程式組態讀取索引鍵/值（根據 content-type），您的應用程式會負責使用任何標準 JSON 還原序列化程式，將 JSON 索引鍵值的值還原序列化。


## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

既然您已經知道如何使用應用程式組態存放區中的 JSON 索引鍵/值，請建立應用程式來使用這些索引鍵/值：

* [ASP.NET Core 快速入門](./quickstart-aspnet-core-app.md)
    * 先決條件： [AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) package v 4.0.0 或更新版本。

* [.NET Core 快速入門](./quickstart-dotnet-core-app.md)
    * 必要條件： [Microsoft.Extensions.Configuration。AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) package v 4.0.0 或更新版本。
