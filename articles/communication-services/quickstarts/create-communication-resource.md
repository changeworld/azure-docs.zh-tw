---
title: 快速入門 - 建立和管理 Azure 通訊服務中的資源
titleSuffix: An Azure Communication Services quickstart
description: 在本快速入門中，您將了解如何建立及管理您的第一個 Azure 通訊服務資源。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-net
ms.openlocfilehash: a93ac3b5d988be33c0f27726a75b1006f990d1da
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886090"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>快速入門：建立和管理通訊服務資源

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

藉由佈建您的第一個通訊服務資源，開始使用 Azure 通訊服務。 您可以透過 Azure 入口網站或使用 .NET 管理用戶端程式庫來佈建通訊服務資源。 管理用戶端程式庫可讓您使用 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)、Azure 的部署和管理服務，來建立、設定、更新和刪除您的資源和介面。 用戶端程式庫中提供的所有功能都可在 Azure 入口網站中取得。 

> [!WARNING]
> 請注意，在公開預覽期間，通訊服務可用性僅限於美國地理位置。 另請注意，在公開預覽期間，通訊資源無法轉移至不同的訂用帳戶。

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

## <a name="access-your-connection-strings-and-service-endpoints"></a>存取您的連接字串和服務端點

連接字串可讓通訊服務用戶端程式庫與 Azure 連線並進行驗證。 您可以從 Azure 入口網站或以程式設計方式使用 Azure Resource Manager API，存取您的通訊服務連接字串和服務端點。 

瀏覽至您的通訊服務資源之後，從導覽功能表選取 [金鑰] 並複製 **連接字串** 或 **端點** 值，供通訊服務用戶端程式庫使用。 請注意，您可以存取主要和次要金鑰。 當您想要將您的通訊服務資源暫時存取權提供給第三方或預備環境時，這會很有用。

:::image type="content" source="./media/key.png" alt-text="通訊服務金鑰頁面的螢幕擷取畫面。":::

## <a name="store-your-connection-string"></a>儲存您的連接字串

通訊服務用戶端程式庫會使用連接字串來授權對通訊服務提出的要求。 您有幾種選項可用來儲存連接字串：

* 在桌面上或裝置上執行的應用程式可將連接字串儲存在 **app.config** 或 **web.config** 檔案。 將連接字串新增至這些檔案中的 **AppSettings** 區段。
* 在 Azure App Service 中執行的應用程式可以將連接字串儲存在 [App Service 應用程式設定](../../app-service/configure-common.md)中。 將連接字串新增至入口網站中 [應用程式設定] 索引標籤的 [連接字串] 區段。
* 您可以將連接字串儲存在 [Azure Key Vault](../../data-factory/store-credentials-in-key-vault.md) 中。
* 如果您是在本機執行應用程式，您可能會想要將連接字串儲存在環境變數中。

### <a name="store-your-connection-string-in-an-environment-variable"></a>將連接字串儲存在環境變數中

若要設定環境變數，請開啟主控台視窗，然後從下列索引標籤選取您的作業系統。 將 `<yourconnectionstring>` 用實際的連接字串取代。

#### <a name="windows"></a>[Windows](#tab/windows)

開啟主控台視窗並輸入下列命令：

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

新增環境變數之後，您可能需要重新啟動任何需要讀取環境變數的執行中程式，包括主控台視窗。 例如，如果您使用 Visual Studio 做為編輯器，請在執行範例前重新啟動 Visual Studio。

#### <a name="macos"></a>[macOS](#tab/unix)

編輯 **.zshrc**，然後新增環境變數：

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

新增環境變數之後，從主控台視窗執行 `source ~/.zshrc`，讓變更生效。 如果您在 IDE 開啟時建立環境變數，則必須關閉編輯器、IDE 或殼層，再重新開啟，才能存取該變數。

#### <a name="linux"></a>[Linux](#tab/linux)

編輯 **.bash_profile**，然後新增環境變數：

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

新增環境變數之後，從主控台視窗執行 `source ~/.bash_profile`，讓變更生效。 如果您在 IDE 開啟時建立環境變數，則必須關閉編輯器、IDE 或殼層，再重新開啟，才能存取該變數。

---

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除通訊服務訂閱，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

如果您在刪除資源時有任何指派給資源的電話號碼，則會同時自動從您的資源釋放電話號碼。 

## <a name="next-steps"></a>下一步

在此快速入門中，您已了解如何：

> [!div class="checklist"]
> * 建立通訊服務資源
> * 設定資源地理位置和標記
> * 存取該資源的金鑰
> * 刪除資源

> [!div class="nextstepaction"]
> [建立您的第一個使用者存取權杖](access-tokens.md)