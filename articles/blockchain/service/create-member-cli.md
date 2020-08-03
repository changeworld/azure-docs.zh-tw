---
title: 建立 Azure 區塊鏈服務成員 - Azure CLI
description: 使用 Azure CLI 建立區塊鏈聯盟的 Azure 區塊鏈服務成員。
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: 2514447eaceb83da0bee81c1475a3137f0d1af07
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170653"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>快速入門：使用 Azure CLI 建立 Azure 區塊鏈服務的區塊鏈成員

在本快速入門中，您會使用 Azure CLI 在 Azure 區塊鏈服務中部署新的區塊鏈成員和聯盟。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

無。

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

若要開啟 Cloud Shell，只要選取程式碼區塊右上角的 [試試看]  即可。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

如果您偏好在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0.51 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prepare-your-environment"></a>準備您的環境

1. 登入。

    如果您使用的是 CLI 的本機安裝，請使用 [az login](/cli/azure/reference-index#az-login) 命令登入。

    ```azurecli
    az login
    ```

    請遵循您終端機上顯示的步驟，來完成驗證程序。

1. 安裝 Azure CLI 擴充功能。

    當搭配 Azure CLI 的延伸模組參考一起使用時，您必須先安裝延伸模組。  Azure CLI 延伸模組可讓您存取核心 CLI 尚未隨附的實驗性與發行前版本命令。  若要深入了解延伸模組 (包括更新及解除安裝)，請參閱[使用 Azure CLI 延伸模組](/cli/azure/azure-cli-extensions-overview) (英文)。

    執行下列命令，以安裝[適用於 Azure 區塊鏈服務的擴充功能](/cli/azure/ext/blockchain/blockchain)：

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. 建立資源群組。

    Azure 區塊鏈服務與所有 Azure 資源一樣都必須部署到資源群組中。 資源群組可讓您組織和管理相關的 Azure 資源。

    在此快速入門中，使用下列 [az group create](/cli/azure/group#az-group-create) 命令，在 _eastus_ 位置中建立一個名為 _myResourceGroup_ 的資源群組：

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>建立區塊鏈成員

Azure 區塊鏈服務成員是私人聯盟區塊鏈網路中的區塊鏈節點。 在佈建成員時，您可以建立或加入聯盟網路。 一個聯盟網路至少需要一個成員。 參與者所需的區塊鏈成員數目視您的案例而定。 聯盟參與者可以有一或多個區塊鏈成員，也可以與其他參與者共用成員。 如需聯盟的詳細資訊，請參閱 [Azure 區塊鏈服務聯盟](consortium.md)。

您必須傳入數個參數和屬性。 請將範例參數取代為您自己的值。

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| 參數 | 描述 |
|---------|-------------|
| **resource-group** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 請使用您在上一節中建立的資源群組。
| **name** | 可識別 Azure 區塊鏈服務區塊鏈成員的唯一名稱。 用於公開端點位址的名稱。 例如： `myblockchainmember.blockchain.azure.com` 。
| **location** | 區塊鏈成員建立所在的 Azure 區域。 例如： `westus2` 。 選擇最靠近您的使用者或其他 Azure 應用程式的位置。 某些區域可能無法使用這些功能。 下列 Azure 區域提供 Azure 區塊鏈資料管理員服務：美國東部和西歐。
| **password** | 成員預設交易節點的密碼。 連線至區塊鏈成員的預設交易節點公用端點時，請使用此密碼進行基本驗證。
| **protocol** | 區塊鏈通訊協定。 目前支援 Quorum 通訊協定。
| **consortium** | 要加入或建立的聯盟名稱。 如需聯盟的詳細資訊，請參閱 [Azure 區塊鏈服務聯盟](consortium.md)。
| **consortium-management-account-password** | 聯盟帳戶密碼也稱為成員帳戶密碼。 成員帳戶密碼可用來對為您的成員建立的 Ethereum 帳戶進行私密金鑰加密。 您可以使用帳戶成員和成員帳戶密碼進行聯盟管理。
| **sku** | 服務層級類型。 「標準」或「基本」。 使用「基本」層來進行開發、測試和概念證明。 使用「標準」層來進行生產等級的部署。 如果您使用區塊鏈資料管理員或傳送大量的私人交易，也應該使用*標準*層。 不支援在建立成員之後變更基本和標準間的定價層。

建立區塊鏈成員和支援的資源需要約 10 分鐘的時間。

## <a name="clean-up-resources"></a>清除資源

您可以使用您建立的區塊鏈成員來進行下一個快速入門或教學課程。 當不再需要資源時，您可藉由刪除您為本快速入門建立的 `myResourceGroup` 資源群組來刪除資源。

執行下列命令，以移除資源群組和所有相關資源。

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 Azure 區塊鏈服務成員和新的聯盟。 請嘗試進行下一個快速入門，使用適用於 Ethereum 的 Azure 區塊鏈開發套件連結至 Azure 區塊鏈服務成員。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 連線至聯盟網路](connect-vscode.md)
