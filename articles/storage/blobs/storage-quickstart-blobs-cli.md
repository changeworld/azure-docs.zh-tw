---
title: 快速入門 - 使用 Azure CLI 建立 Blob
titleSuffix: Azure Storage
description: 在本快速入門中，您將了解如何使用 Azure CLI 將 Blob 上傳至 Azure 儲存體、下載 Blob，以及列出容器中的 Blob。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 04/23/2020
ms.author: tamram
ms.openlocfilehash: 333d9f12ff817a5264183666cd1b858075a93077
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82176680"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>快速入門：使用 Azure CLI 上傳、下載及列出 Blob

Azure CLI 是管理 Azure 資源的 Azure 命令列體驗。 您可以在瀏覽器中使用它搭配 Azure Cloud Shell。 您也可以將它安裝在 macOS、Linux 或 Windows 上，並從命令列執行。 在本快速入門中，您會了解如何使用 Azure CLI 從 Azure Blob 儲存體上傳和下載資料。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>在本機安裝 Azure CLI

如果您選擇在本機安裝和使用 Azure CLI，本快速入門會要求您執行 Azure CLI 2.0.46 版或更新版本。 執行 `az --version` 來判斷您的版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

如果您要在本機執行 Azure CLI，則必須登入並進行驗證。 如果您要使用 Azure Cloud Shell，則不需要執行此步驟。 若要登入 Azure CLI，請在瀏覽器視窗中執行 `az login` 並進行驗證：

```azurecli
az login
```

如需關於使用 Azure CLI 進行驗證的詳細資訊，請參閱[使用 Azure CLI 登入](/cli/azure/authenticate-azure-cli)。

## <a name="authorize-access-to-blob-storage"></a>授與 Blob 儲存體的存取權

您可以使用 Azure AD 認證或使用儲存體帳戶存取金鑰，來授權從 Azure CLI 存取 Blob 儲存體。 建議您使用 Azure AD 認證。 本文說明如何使用 Azure AD 來授權 Blob 儲存體作業。

針對 Blob 儲存體的資料作業 Azure CLI 命令可支援 `--auth-mode` 參數，這可讓您指定如何授權指定的作業。 將 `--auth-mode` 參數設定為 `login`，以使用 Azure AD 認證進行授權。 如需詳細資訊，請參閱[使用 Azure CLI 授與 Blob 或佇列資料的存取權](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

只有 Blob 儲存體資料作業可支援 `--auth-mode` 參數。 管理作業 (例如建立資源群組或儲存體帳戶) 會自動使用 Azure AD 認證來進行授權。

## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group) 命令來建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

請記得以您自己的值取代角括號中的預留位置值：

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶

使用 [az storage account create](/cli/azure/storage/account) 命令建立一般用途的儲存體帳戶。 一般用途的儲存體帳戶可以用於所有四個服務：Blob、檔案、資料表和佇列。

請記得以您自己的值取代角括號中的預留位置值：

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>建立容器

Blob 一律會上傳到容器中。 您可以在容器中組織 Blob 群組，方式如同在電腦的資料夾中組織檔案。

使用 [az storage container create](/cli/azure/storage/container) 命令，建立用於儲存 Blob 的容器。 請記得以您自己的值取代角括號中的預留位置值：

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

## <a name="upload-a-blob"></a>上傳 Blob

Blob 儲存體支援區塊 Blob、附加 Blob 和分頁 Blob。 本快速入門中的範例示範如何使用區塊 Blob。

首先，建立一個要上傳至區塊 Blob 的檔案。 如果您使用的是 Azure Cloud Shell，請使用下列命令建立檔案：

```bash
vi helloworld
```

當檔案開啟時，請按 **insert**。 輸入 *Hello world*，然後按 **Esc**。接著，輸入 *:x*，然後按 **Enter**。

在此範例中，您要將 Blob 上傳到使用 [az storage blob upload](/cli/azure/storage/blob) 命令最後一個步驟所建立的容器。 您不需要指定檔案路徑，因為檔案是在根目錄建立的。 請記得以您自己的值取代角括號中的預留位置值：

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

如果 Blob 不存在，此作業會建立 Blob，若已存在，則會加以覆寫。 請上傳您所需的檔案數量，再繼續進行。

若要同時上傳多個檔案，您可以使用 [az storage blob upload-batch](/cli/azure/storage/blob) 命令。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

使用 [az storage blob list](/cli/azure/storage/blob) 命令列出容器中的 Blob。 請記得以您自己的值取代角括號中的預留位置值：

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>下載 Blob

使用 [az storage blob download](/cli/azure/storage/blob) 命令下載稍早所上傳的 Blob。 請記得以您自己的值取代角括號中的預留位置值：

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>使用 AzCopy 進行資料轉送

AzCopy 命令列公用程式可為 Azure 儲存體提供高效能、可編寫指令碼的資料傳輸。 您可以使用 AzCopy 將資料傳輸到 Blob 儲存體和 Azure 檔案儲存體，以及從 Blob 儲存體和 Azure 檔案儲存體傳出資料。 如需 AzCopy v10 (AzCopy 的最新版本) 的詳細資訊，請參閱[開始使用 AzCopy](../common/storage-use-azcopy-v10.md)。 若要了解如何搭配使用 AzCopy v10 與 Blob 儲存體，請參閱[使用 AzCopy 和 Blob 儲存體傳輸資料](../common/storage-use-azcopy-blobs.md)。

下列範例會使用 AzCopy 將本機檔案上傳至 Blob。 請記得要以您自己的值取代範例值：

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>清除資源

如果您想要刪除您在本快速入門中建立的資源 (括儲存體帳戶)，請使用 [az group delete](/cli/azure/group) 命令刪除資源群組。 請記得以您自己的值取代角括號中的預留位置值：

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已經了解如何在本機檔案系統和 Azure Blob 儲存體中的容器之間傳輸檔案。 若要深入了解如何透過 Azure CLI 來使用 Blob 儲存體，請探索 Blob 儲存體的 Azure CLI 範例。

> [!div class="nextstepaction"]
> [適用於 Blob 儲存體的 Azure CLI 範例](/azure/storage/blobs/storage-samples-blobs-cli?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
