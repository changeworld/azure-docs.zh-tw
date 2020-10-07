---
title: 快速入門 - 使用 Azure CLI 設定 Azure IoT 中樞裝置佈建服務
description: 快速入門 - 使用 Azure CLI 設定 Azure IoT 中樞裝置佈建服務 (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 157bd0a0a40ce6a935d13bb10a84c814ba1018bf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330242"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>快速入門：使用 Azure CLI 設定 IoT 中樞裝置佈建服務

Azure CLI 可用來從命令列或在指令碼中建立和管理 Azure 資源。 本快速入門詳細說明如何使用 Azure CLI 來建立 IoT 中樞和 IoT 中樞裝置佈建服務，以及將這兩項服務連結在一起。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!IMPORTANT]
> 您在本快速入門中建立的 IoT 中樞與佈建服務，均可作為 DNS 端點公開探索。 如果您決定變更這些資源所用的名稱，請務必避免使用任何敏感性資訊。
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-resource-group"></a>建立資源群組

使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

下列範例會在 westus  位置建立名為 my-sample-resource-group  的資源群組。

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> 此範例會建立位於美國西部位置的資源群組。 您可以執行命令 `az account list-locations -o table`，以檢視可用位置清單。
>
>

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令建立 IoT 中樞。

下列範例會在 westus  位置建立名為 my-sample-hub  的 IoT 中樞。 IoT 中樞名稱在 Azure 中必須是全域唯一的，因此您可以在範例名稱中新增唯一的首碼或尾碼，或選擇新的名稱。 請確定您的名稱遵循 IoT 中樞的適當命名慣例：長度應為 3-50 個字元，且只能包含大寫或小寫英數字元或連字號 ('-')。 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>建立裝置佈建服務

使用 [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) 命令建立裝置佈建服務。 

下列範例會在 westus  位置建立名為 my-sample-dps  的佈建服務。 您也必須為自己的佈建服務選擇全域唯一的名稱。 請確定名稱遵循 IoT 中樞裝置佈建服務的適當命名慣例：長度應為 3-64 個字元，且只能包含大寫或小寫英數字元或連字號 ('-')。

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> 此範例會在美國西部位置建立佈建服務。 執行 `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` 命令，或移至 [Azure 狀態](https://azure.microsoft.com/status/)頁面並搜尋「裝置佈建服務」，即可檢視可用的位置清單。 在命令中，可指定一個字或多字格式的位置；例如：uswest、West US、WEST US 等等。此值不區分大小寫。 如果您使用多字格式來指定位置，此值會用引號括住；例如 `--location "West US"`。
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>取得 IoT 中樞的連接字串

您需要 IoT 中樞的連接字串，才能將它與裝置佈建服務連結。 使用 [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) 命令取得連接字串，並使用其輸出來設定將在連結兩項資源時使用的變數。 

下列範例會將 hubConnectionString  變數設定為連接字串的值，作為中樞的 iothubowner  原則的主索引鍵 (`--policy-name` 參數可用來指定不同的原則)。 請將 my-sample-hub  取代為您先前選擇的唯一 IoT 中樞名稱。 此命令會使用 Azure CLI [查詢](/cli/azure/query-azure-cli)和[輸出](/cli/azure/format-output-azure-cli#tsv-output-format)選項，從命令輸出中擷取連接字串。

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

您可以使用 `echo` 命令來查看連接字串。

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> 這兩個命令均適用於在 Bash 下執行的主機。
> 
> 如果您是使用本機 Windows/CMD 殼層或 PowerShell 主機，請修改命令來使用該環境的正確語法。
>
> 如果您是使用 Azure Cloud Shell，請檢查殼層視窗左側的環境下拉式清單是否顯示 **Bash**。
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>連結 IoT 中樞與佈建服務

使用 [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create) 命令來連結 IoT 中樞與佈建服務。 

下列範例會在 westus 位置建立名為 my-sample-hub 的 IoT 中樞，以及建立名為 my-sample-dps 的裝置佈建服務。 請將這些名稱取代為您先前選擇的唯一 IoT 中樞和裝置佈建服務名稱。 此命令會使用在先前的步驟中儲存在 hubConnectionString** 變數中之 IoT 中樞的連接字串。

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

此命令可能需要數分鐘才能完成。

## <a name="verify-the-provisioning-service"></a>驗證佈建服務

使用 [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show) 命令取得佈建服務的詳細資料。

下列範例會取得名為 my-sample-dps** 的佈建服務詳細資料。 請將此名稱取代為您自己的裝置佈建服務名稱。

```azurecli-interactive
az iot dps show --name my-sample-dps
```
連結的 IoT 中樞會顯示在 properties.iotHubs** 集合中。

![驗證佈建服務](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>清除資源

此集合中的其他快速入門會以本快速入門為基礎。 如果您打算繼續進行後續的快速入門或教學課程，請勿清除在此快速入門中建立的資源。 如果您不打算繼續執行，可以使用下列命令來刪除佈建服務、IoT 中樞或資源群組及其所有資源。 請將下方列出的資源名稱取代為您自己的資源名稱。

若要刪除佈建服務，請執行 [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete) 命令：

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
若要刪除 IoT 中樞，請執行 [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete) 命令：

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

若要刪除資源群組和其所有資源，請執行 [az group delete](/cli/azure/group#az-group-delete) 命令：

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 IoT 中樞和裝置佈建服務執行個體，並已連結這兩個資源。 若要深入了解如何使用這項設定來佈建模擬裝置，請繼續進行建立模擬裝置的快速入門。

> [!div class="nextstepaction"]
> [建立模擬裝置的快速入門](./quick-create-simulated-device.md)
