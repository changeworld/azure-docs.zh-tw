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
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c565be3aea43fc4dc86fd793e0dc2dea0b08d4c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563744"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>使用 Azure CLI 建立 Azure 通知中樞

Azure 通知中樞提供易於使用且相應放大的推播引擎，可讓您從任何後端 (雲端或內部部署) 傳送通知到任何平台 (iOS、Android、Windows、Kindle、Baidu 等)。 如需該服務的詳細資訊，請參閱[什麼是 Azure 通知中樞？](notification-hubs-push-notification-overview.md)。

在本快速入門中，您會使用 Azure CLI 建立通知中樞。 第一節會提供讓您建立通知中樞命名空間的相關步驟。 第二節會提供相關步驟讓您在現有命名空間中建立通知中樞。 您也會了解如何建立自訂存取原則。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> 通知中樞需要 Azure CLI 的 2.0.67 版或更新版本。 執行 [az version](/cli/azure/reference-index#az_version) 以尋找已安裝的版本和相依程式庫。 若要升級至最新版本，請執行 [az upgrade](/cli/azure/reference-index#az_upgrade)。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 通知中樞與所有 Azure 資源一樣都必須部署到資源群組中。  資源群組可讓您組織和管理相關的 Azure 資源。  若要深入了解資源群組，請參閱[什麼是 Azure Resource Manager](../azure-resource-manager/management/overview.md)。

針對此快速入門，請使用下列 [az group create](/cli/azure/group#az-group-create) 命令，在 eastus 位置中建立一個名為 spnhubrg 的資源群組。

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>建立通知中樞命名空間

1. 建立通知中樞的命名空間。

   命名空間會包含一或多個中樞，此名稱在所有 Azure 訂用帳戶中必須是唯一的，並且長度至少為 6 個字元。 若要檢查名稱的可用性，請使用 [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) 命令。

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Azure CLI 會顯示下列主控台輸出，以回應您的可用性要求：

   ```shell
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   請注意 Azure CLI 回應中的第二行，`"isAvailable": true`。 若無法使用您為命名空間所指定的名稱，則此行將會顯示為 `false`。 一旦您確認名稱的可用性之後，請執行 [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) 命令來建立您的命名空間。  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   若您提供給 `az notification-hub namespace create` 命令的 `--name` 無法使用，或不符合 [Azure 資源的命名規則與限制](../azure-resource-manager/management/resource-name-rules.md)，Azure CLI 會以下列主控台輸出回應：

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   若您嘗試的第一個名字不成功，請為新的命名空間選取不同的名稱，然後再次執行 `az notification-hub namespace create` 命令。

   > [!NOTE]
   > 在此步驟中，您必須在您從本快速入門複製的每個 Azure CLI 命令中，取代 `--namespace` 參數的值。

2. 取得命名空間的清單。

   若要查看新命名空間的詳細資料，請使用 [az notification-hub namespace list](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list) 命令。 若您想要查看訂用帳戶的所有命名空間，可選擇 `--resource-group` 參數。

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>建立通知中樞

1. 建立您的第一個通知中樞。

   現在您可以在新的命名空間中，建立一或多個通知中樞。 執行 [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) 命令來建立通知中樞。

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. 建立第二個通知中樞。

   您可以在單一命名空間中建立多個通知中樞。 若要在相同的命名空間中建立第二個通知中樞，請使用不同的中樞名稱再次執行 `az notification-hub create` 命令。

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. 取得通知中樞的清單。

   Azure CLI 會傳回每個已執行命令的成功或錯誤訊息；然而，也保證可以查詢通知中樞清單。 [az notification-hub list](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list) 命令是針對這個用途所設計。

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>使用存取原則

1. Azure 通知中樞透過使用存取原則，使用[共用存取簽章安全性](./notification-hubs-push-notification-security.md)。 當您建立通知中樞時，會自動建立兩個原則。 必須有這些原則的連接字串，才能設定推播通知。 [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) 命令會提供原則名稱與其各自資源群組的清單。

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > 請勿在應用程式中使用 _DefaultFullSharedAccessSignature_ 原則。 此原則只能在後端使用。 用戶端應用程式中應只使用 `Listen` 存取原則。

2. 若您想要使用有意義的名稱建立其他授權規則，可以使用 [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) 命令來建立及自訂您自己的存取原則。 `--rights` 參數是您想要指派的權限清單 (以空格分隔)。

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. 每個存取原則都有兩組索引鍵和連接字串。 您稍後需要用到這些連接字串來[設定通知中樞](./configure-notification-hub-portal-pns-settings.md)。 若要列出通知中樞存取原則的索引鍵和連接字串，請執行 [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) 命令。

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [通知中樞命名空間](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys)和[通知中樞](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)有個別的存取原則。 查詢索引鍵和連接字串時，請確定您使用的是正確的 Azure CLI 參考。

## <a name="clean-up-resources"></a>清除資源

若不再需要，請使用 [az group delete](/cli/azure/group) 命令來移除資源群組和所有相關資源：

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>後續步驟

* 在本快速入門中，您已建立通知中樞。 若要了解如何使用平台通知系統 (PNS) 設定來設定中樞，請參閱[在通知中樞內設定推播通知](configure-notification-hub-portal-pns-settings.md)

* 探索使用 Azure CLI 管理通知中樞的大量功能：

  [通知中樞的完整參考清單](/cli/azure/ext/notification-hub/notification-hub)

  [通知中樞命名空間的參考清單](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [通知中樞授權規則的參考清單](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [通知中樞認證認證的參考清單](/cli/azure/ext/notification-hub/notification-hub/credential)