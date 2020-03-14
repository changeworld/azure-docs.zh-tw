---
title: 設定連接字串
titleSuffix: Azure Storage
description: 設定 Azure 儲存體帳戶的連接字串。 連接字串包含在執行時間使用共用金鑰授權，從您的應用程式授權存取儲存體帳戶所需的資訊。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f617beec8a53570ede7755040cfbb92a7d1712b7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268349"
---
# <a name="configure-azure-storage-connection-strings"></a>設定 Azure 儲存體連接字串

連接字串包含您的應用程式在執行時間使用共用金鑰授權存取 Azure 儲存體帳戶中資料所需的授權資訊。 您可以設定連接字串，以進行下列動作：

* 連線至 Azure 儲存體模擬器
* 在 Azure 中存取儲存體帳戶。
* 透過共用存取簽章 (SAS) 存取 Azure 中的指定資源。

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-and-copy-a-connection-string"></a>查看並複製連接字串

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="store-a-connection-string"></a>儲存連接字串

您的應用程式需要在執行階段存取連接字串，才能授權給對 Azure 儲存體進行的要求。 您有幾種選項可用來儲存連接字串：

* 您可以將連接字串儲存在環境變數中。
* 在桌面上或裝置上執行的應用程式可將連接字串儲存在 **app.config** 或 **web.config** 檔案。 將連接字串新增至這些檔案中的 **AppSettings** 區段。
* 在 Azure 雲端服務中執行的應用程式可將連接字串儲存在 [Azure 服務組態結構描述 (.cscfg) 檔](https://msdn.microsoft.com/library/ee758710.aspx)中。 將此連接字串加入服務組態檔的 [ConfigurationSettings] 區段。

在組態檔內儲存連接字串，可讓您更容易更新連接字串，藉此在儲存體模擬器和雲端中的 Azure 儲存體帳戶之間切換。 您只需要編輯連接字串以指向您的目標環境。

您可以使用 [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)，在執行階段存取連接字串，而不論應用程式執行所在的地方為何。

## <a name="configure-a-connection-string-for-the-storage-emulator"></a>設定儲存體模擬器的連接字串

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

如需儲存體模擬器的詳細資訊，請參閱 [使用 Azure 儲存體模擬器進行開發和測試](storage-use-emulator.md) 。

## <a name="configure-a-connection-string-for-an-azure-storage-account"></a>設定 Azure 儲存體帳戶的連接字串

若要建立 Azure 儲存體帳戶的連接字串，請使用以下格式。 指出您是否要透過 HTTPS (建議選項) 或 HTTP 連線至儲存體帳戶、使用您的儲存體帳戶名稱來取代 `myAccountName`，以及使用您的帳戶存取金鑰來取代 `myAccountKey`：

`DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey`

例如，您的連接字串看起來可能如下所示︰

`DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>`

雖然 Azure 儲存體可同時支援連接字串中的 HTTP 和 HTTPS，但*強烈建議您使用 HTTPS*。

> [!TIP]
> 您可以在 [Azure 入口網站](https://portal.azure.com)中找到儲存體帳戶的連接字串。 若要查看這主要和次要存取金鑰的連接字串，請在儲存體帳戶的功能表刀鋒視窗中，瀏覽至 [設定] > [存取金鑰]。
>

## <a name="create-a-connection-string-using-a-shared-access-signature"></a>使用共用存取簽章建立連接字串

[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

## <a name="create-a-connection-string-for-an-explicit-storage-endpoint"></a>建立明確儲存體端點的連接字串

您可以在連接字串中指定明確的服務端點，而不使用預設端點。 若要建立指定明確端點的連接字串，請使用下列格式來指定每個服務的完整服務端點，包括通訊協定規格 (HTTPS (建議選項) 或 HTTP)：

```
DefaultEndpointsProtocol=[http|https];
BlobEndpoint=myBlobEndpoint;
FileEndpoint=myFileEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
AccountName=myAccountName;
AccountKey=myAccountKey
```

您可能想要指定明確端點的一個案例為是否您已將 Blob 儲存體端點對應至[自訂網域](../blobs/storage-custom-domain-name.md)。 在此情況下，您可以在連接字串中指定 Blob 儲存體的自訂端點。 您可以選擇性地指派其他服務的預設端點 (如果應用程式有使用這些服務) 。

以下是針對 Blob 服務指定明確端點的連接字串範例︰

```
# Blob endpoint only
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
AccountName=storagesample;
AccountKey=<account-key>
```

此範例會指定所有服務的明確端點，包括 Blob 服務的自訂網域︰

```
# All service endpoints
DefaultEndpointsProtocol=https;
BlobEndpoint=http://www.mydomain.com;
FileEndpoint=https://myaccount.file.core.windows.net;
QueueEndpoint=https://myaccount.queue.core.windows.net;
TableEndpoint=https://myaccount.table.core.windows.net;
AccountName=storagesample;
AccountKey=<account-key>
```

連接字串中的端點值可用來建構連線至儲存體服務的要求 URI，並要求任何傳回到您程式碼的 URI 形式。

如果你已將儲存體端點對應至自訂網域，且從連接字串中省略該端點，則無法使用該連接字串，從程式碼中存取該服務中的資料。

> [!IMPORTANT]
> 連接字串中的服務端點值必須是格式正確的 URI，包括 `https://`(建議) 或 `http://`。 因為 Azure 儲存體還不支援自訂網域的 HTTPS，您必須針對任何指向自訂網域的端點 URI，指定 `http://`。
>

### <a name="create-a-connection-string-with-an-endpoint-suffix"></a>建立包含端點尾碼的連接字串

若要在區域或具有不同端點尾碼的實例中建立儲存體服務的連接字串（例如，針對 Azure 中國世紀或 Azure Government，請使用下列連接字串格式）。 指出您是否要透過 HTTPS (建議) 或 HTTP 連線至儲存體帳戶、使用您的儲存體帳戶名稱來取代 `myAccountName`、使用您的帳戶存取金鑰來取代 `myAccountKey`，以及使用 URI 尾碼來取代 `mySuffix`：

```
DefaultEndpointsProtocol=[http|https];
AccountName=myAccountName;
AccountKey=myAccountKey;
EndpointSuffix=mySuffix;
```

以下是 Azure 中國世紀儲存體服務的範例連接字串：

```
DefaultEndpointsProtocol=https;
AccountName=storagesample;
AccountKey=<account-key>;
EndpointSuffix=core.chinacloudapi.cn;
```

## <a name="parsing-a-connection-string"></a>剖析連接字串

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="next-steps"></a>後續步驟

* [使用 Azure 儲存體模擬器進行開發和測試](storage-use-emulator.md)
* [Azure 儲存體總管](storage-explorers.md)
* [使用共用存取簽章 (SAS)](storage-sas-overview.md)
