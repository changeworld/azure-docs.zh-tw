---
title: 快速入門 - 使用 Azure CLI 建立 Azure 通知中樞 | Microsoft Docs
description: 在本教學課程中，您會了解如何使用 Azure CLI 建立 Azure 通知中樞。
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80069481"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>快速入門：使用 Azure CLI 建立 Azure 通知中樞

Azure 通知中樞提供易於使用且相應放大的推播引擎，可讓您從任何後端 (雲端或內部部署) 傳送通知到任何平台 (iOS、Android、Windows、Kindle、Baidu 等)。 如需該服務的詳細資訊，請參閱[什麼是 Azure 通知中樞？](notification-hubs-push-notification-overview.md)。

在本快速入門中，您會使用 Azure CLI 建立通知中樞。 第一節會提供建立通知中樞命名空間的相關步驟，以及該命名空間的查詢存取原則。 第二節會提供相關步驟讓您在現有命名空間中建立通知中樞。  您也會了解如何建立自訂存取原則。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

通知中樞需要 Azure CLI 的 2.0.67 版或更新版本。 執行 `az --version` 以尋找已安裝的版本和相依程式庫。 若要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prepare-your-environment"></a>準備您的環境

1. 登入。

   如果您使用的是 CLI 的本機安裝，請使用 [az login](/cli/azure/reference-index#az-login) 命令登入。

    ```azurecli-interactive
    az login
    ```

    請遵循您終端機上顯示的步驟，來完成驗證程序。

2. 安裝 Azure CLI 擴充功能。

   若要針對通知中樞執行 Azure CLI 命令，請安裝 Azure CLI 的[通知中樞擴充功能](/cli/azure/ext/notification-hub/notification-hub)。  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. 建立資源群組。

   Azure 通知中樞和所有 Azure 資源相同，都必須部署到資源群組中。 資源群組可讓您組織和管理相關的 Azure 資源。

   針對此快速入門，請使用下列 [az group create](/cli/azure/group#az-group-create) 命令，在 eastus  位置中建立一個名為 spnhubrg  的資源群組：

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>建立通知中樞命名空間

1. 建立通知中樞的命名空間

   命名空間會包含一個或多個中樞，此名稱在所有 Azure 訂用帳戶中必須是唯一的。  若要檢查指定服務命名空間的可用性，請使用 [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) 命令。  執行 [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) 命令來建立命名空間。  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. 列出命名空間存取原則的索引鍵和連接字串。

   系統會自動為新的命名空間建立名為 **RootManageSharedAccessKey** 的存取原則。  每個存取原則都有兩組索引鍵和連接字串。  若要列出命名空間的索引鍵和連接字串，請執行 [az notification-hub namespace authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) 命令。

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>建立通知中樞

1. 建立您的第一個通知中樞。

   現在您可以在新的命名空間中建立通知中樞。  執行 [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) 命令來建立通知中樞。

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. 建立第二個通知中樞。

   您可以在單一命名空間中建立多個通知中樞。  若要在相同的命名空間中建立第二個通知中樞，請使用不同的中樞名稱再次執行 `az notification-hub create` 命令。

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>使用存取原則

1. 為通知中樞建立新的授權規則。

   系統會自動為每個新的通知中樞建立存取原則。  若要建立和自訂您自己的存取原則，請使用 [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) 命令。

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. 列出通知中樞的存取原則。

   若要查詢通知中樞有哪些存取原則，請使用 [az  notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) 命令。

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > 請勿在應用程式中使用 **DefaultFullSharedAccessSignature** 原則。 這只能在後端使用。  用戶端應用程式中應只使用 **Listen** 存取原則。

3. 列出通知中樞存取原則的索引鍵和連接字串

   每個存取原則都有兩組索引鍵和連接字串。  您稍後需要用到這些連接字串來處理推播通知。  若要列出通知中樞存取原則的索引鍵和連接字串，請執行 [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)命令。

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [通知中樞命名空間](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys)和[通知中樞](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)有個別的存取原則。  查詢索引鍵和連接字串時，請確定您使用的是正確的 Azure CLI 參考。

## <a name="clean-up-resources"></a>清除資源

若不再需要，請使用 [az group delete](/cli/azure/group) 命令來移除資源群組和所有相關資源。

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>另請參閱

探索使用 Azure CLI 管理通知中樞的完整功能。

* [通知中樞的完整 Azure CLI 參考清單](/cli/azure/ext/notification-hub/notification-hub)
* [通知中樞命名空間的 Azure CLI 參考清單](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [通知中樞授權規則的 Azure CLI 參考清單](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [通知中樞認證的 Azure CLI 參考清單](/cli/azure/ext/notification-hub/notification-hub/credential)
