---
title: 使用維修結構網格維修結構可靠的磁片卷
description: 了解如何使用 Azure CLI 在容器內裝載以 Service Fabric 可靠磁碟為基礎的磁碟區，以在 Azure Service Fabric Mesh 應用程式中儲存狀態。
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: f26fe70afe7d9e2872f06ac6da7143556278b1b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497961"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>在 Service Fabric Mesh 應用程式中裝載以 Service Fabric 可靠磁碟為基礎的高可用性磁碟區 
保存容器應用程式狀態的常見方法是使用遠端儲存體 (例如 Azure 檔案儲存體) 或資料庫 (例如 Azure Cosmos DB)。 這會對遠端存放區產生大量的讀取和寫入網路延遲。

本文會說明如何在 Service Fabric Mesh 應用程式的容器中裝載磁碟區，以儲存高可用性 Service Fabric 可靠磁碟中的狀態。
Service Fabric 可靠磁碟會提供磁碟區來用於本機讀取，並在 Service Fabric 叢集內複寫寫入項目以達到高可用性。 這會移除讀取的網路呼叫，並降低寫入的網路延遲。 如果容器重新啟動或移到另一個節點，新的容器執行個體會看到與舊磁碟區相同的磁碟區。 既有效率又有高可用性。

在此範例中，計數器應用程式會透過 ASP.NET Core 服務建立網頁，以在瀏覽器中顯示計數器的值。

`counterService` 會定期從檔案讀取計數器的值、增加該值並將其寫回檔案。 儲存檔案的資料夾會裝載在由 Service Fabric 可靠磁碟支援的磁碟區中。

## <a name="prerequisites"></a>Prerequisites

您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成此工作。 若要依照本文的指示使用 Azure CLI，請確認 `az --version` 至少傳回 `azure-cli (2.0.43)`。  請依照下列[指示](service-fabric-mesh-howto-setup-cli.md)安裝 (或更新) Azure Service Fabric Mesh CLI 擴充功能模組。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 並設定您的訂用帳戶。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>建立資源群組

建立要部署應用程式的資源群組。 下列命令會在美國東部的位置中建立名為 `myResourceGroup` 的資源群組。 如果您變更下列命令中的資源群組名稱，請記得在後續的所有命令中進行變更。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>部署範本

下列命令會使用 [counter.sfreliablevolume.linux.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json) 範本來部署 Linux 應用程式。 若要部署 Windows 應用程式，請使用 [counter.sfreliablevolume.windows.json 範本](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json)。 請注意，較大的容器映像可能需要較長的部署時間。

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

您也可以使用命令查看部署的狀態

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

請記下資源類型為 `Microsoft.ServiceFabricMesh/gateways` 的閘道資源名稱。 這將用於取得應用程式的公用 IP 位址。

## <a name="open-the-application"></a>開啟應用程式

應用程式成功部署後，即可取得應用程式閘道資源的 ipAddress。 使用您在上節中記下的閘道名稱。
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

輸出應該會有 `ipAddress` 屬性，這是服務端點的公用 IP 位址。 從瀏覽器將其開啟。 網頁會隨即出現，其中包含每秒更新的計數器值。

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>確認應用程式都可以使用磁碟區

應用程式會在 `counter/counterService` 資料夾的磁碟區內建立名為 `counter.txt` 的檔案。 此檔案的內容就是正在網頁上顯示計的數器值。

## <a name="delete-the-resources"></a>刪除資源

請勤加刪除您已不會在 Azure 中使用的資源。 若要刪除此範例的相關資源，請使用下列命令，將資源部署所在的資源群組刪除 (這會刪除所有與該資源群組相關聯的項目)：

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>後續步驟

- 在 [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) 上檢視 Service Fabric 可靠磁碟區的應用程式範例。
- 若要深入了解 Service Fabric 資源模型，請參閱 [Service Fabric Mesh 資源模型](service-fabric-mesh-service-fabric-resources.md)。
- 若要深入了解 Service Fabric Mesh，請閱讀 [Service Fabric Mesh 概觀](service-fabric-mesh-overview.md)。
