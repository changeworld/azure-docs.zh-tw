---
title: 快速入門 - 使用 CLI 建立 Azure APIM 執行個體 (預覽)
description: 使用 Azure CLI 建立新的 Azure APIM 服務執行個體。
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 863cb6cd10664e14966898045cb2b1861ef75b9f
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887445"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli-preview"></a>快速入門：使用 Azure CLI 建立新的 Azure APIM 服務執行個體 (預覽)

「Azure API 管理 (APIM)」可協助組織將 API 發佈給外部、合作夥伴及內部開發人員，以發揮其資料與服務的潛力。 「API 管理」提供的核心專長認證，透過開發人員參與、商務洞察力、分析、安全性和保護，可確保 API 程式獲致成功。 APIM 可讓您為裝載於任何位置的現有後端服務，建立和管理新式 API 閘道。 如需詳細資訊，請參閱[概觀](api-management-key-concepts.md)。

此快速入門描述在 Azure CLI 中使用 [az apim](/cli/azure/apim) 命令建立新 APIM 執行個體的步驟。 `az apim` 命令群組中的命令目前為預覽狀態，而且可能會在未來的版本中變更或移除。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 2.11.1 版或更新版本的 Azure CLI。 如果您是使用 Azure Cloud Shell，就已安裝最新版本。

## <a name="create-a-resource-group"></a>建立資源群組

Azure APIM 執行個體和所有 Azure 資源相同，都必須部署到資源群組中。 資源群組可讓您組織和管理相關的 Azure 資源。

首先，使用下列 [az group create](/cli/azure/group#az-group-create) 命令，在「美國中部」位置中建立一個名為 *myResourceGroup* 的資源群組：

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>建立新的服務

現在您已有資源群組，您可以接著建立 APIM 服務執行個體。 使用 [az apim create](/cli/azure/apim#az-apim-create) 命令建立一個執行個體，並提供服務名稱和發行者詳細資料。 服務名稱必須是 Azure 中唯一的。 

在下列範例中，服務名稱會使用 *myapim*。 將名稱更新為唯一值。 也請更新 API 發行者的組織名稱，以及用來接收通知的電子郵件地址。 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

根據預設，此命令會在開發人員層中建立執行個體，以此經濟實惠的選項評估 Azure APIM。 此階層不適用於生產環境。 如需有關調整 API 管理層規模的詳細資訊，請參閱[升級和調整規模](upgrade-and-scale.md)。 

> [!TIP]
> 這可能需要 30 到 40 分鐘的時間，才能在此階層中建立及啟用 APIM 服務。 先前的命令會使用 `--no-wait` 選項，讓此命令在服務建立時立即返回。

執行 [az apim show](/cli/azure/apim#az-apim-show) 命令來檢查部署的狀態：

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

一開始，輸出會如下所示，並顯示 `Activating` 狀態：

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

啟用之後，狀態會是 `Online`，而服務執行個體會有閘道位址和公用 IP 位址。 目前，這些位址不會公開任何內容。 例如：\

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

當您的 APIM 服務執行個體上線時，您就可以開始使用了。 從[匯入和發佈第一個 API](import-and-publish.md) 教學課程開始。

## <a name="clean-up-resources"></a>清除資源

若不再需要資源群組和 APIM 服務執行個體，您可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令來移除這些項目。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [匯入和發佈您的第一個 API](import-and-publish.md)
