---
title: 快速入門 - 將 Hello World 部署至 Azure Service Fabric Mesh
description: 本快速入門說明如何將 Service Fabric Mesh 應用程式部署至 Azure Service Fabric Mesh。
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: quickstart
ms.openlocfilehash: 5373cbf76b55b86e4851e1d7c6b53222871faa4c
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86254328"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>快速入門：將 Hello World 部署至 Service Fabric Mesh

[Service Fabric Mesh](service-fabric-mesh-overview.md) 可讓您在 Azure 中輕鬆建立及管理微服務應用程式，而無須佈建虛擬機器。 在本快速入門中，您將在 Azure 中建立 Hello World 應用程式，並將其公開至網際網路。 只需要一個命令就能完成這項作業。 只要短短幾分鐘，您就能在瀏覽器中看到下列檢視：

![瀏覽器中的 Hello World 應用程式][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

如果您還沒有 Azure 帳戶，請在開始之前[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="set-up-service-fabric-mesh-cli"></a>設定 Service Fabric Mesh CLI 
您可以使用 Azure Cloud Shell 或安裝在本機的 Azure CLI 來完成此快速入門。 請依照下列[指示](service-fabric-mesh-howto-setup-cli.md)安裝 Azure Service Fabric Mesh CLI 擴充功能模組。

## <a name="sign-in-to-azure"></a>登入 Azure
登入 Azure 並設定您的訂用帳戶。

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>建立資源群組
建立要部署應用程式的資源群組。 您可以使用現有的資源群組，並略過此步驟。 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>部署應用程式
使用 `az mesh deployment create` 命令在資源群組中建立您的應用程式。  執行下列命令：

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

上述命令會使用 [linux.json 範本](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json)來部署 Linux 應用程式。 如果您想要部署 Windows 應用程式，請使用 [windows.json 範本](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json)。 Windows 容器映像比 Linux 容器映像大，因此部署時可能需要較多時間。

此命令會產生如下所示的 JSON 程式碼片段。 在 JSON 輸出的 ```outputs``` 區段下方，複製 ```publicIPAddress``` 屬性。

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

這項資訊來自於 ARM 範本中的 ```outputs``` 區段。 如下所示，此區段會參考閘道資源以擷取公用 IP 位址。 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>開啟應用程式
在應用程式成功部署後，請從 CLI 輸出中複製服務端點的公用 IP 位址。 請在網頁瀏覽器中開啟該 IP 位址。 此時會顯示含有 Azure Service Fabric Mesh 標誌的網頁。

## <a name="check-the-application-details"></a>查看應用程式詳細資料
您可以使用 `az mesh app show` 命令來查看應用程式的狀態。 此命令會提供您可以追蹤的實用資訊。

本快速入門的應用程式名稱是 `helloWorldApp`，若要收集此應用程式的詳細資料，請執行下列命令：

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>檢視應用程式記錄

使用 `az mesh code-package-log get` 命令，查看已部署應用程式的記錄：

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>清除資源

當您準備好要刪除應用程式時，請執行 [az group delete][az-group-delete] 命令來移除資源群組及其包含的應用程式和網路資源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

若要深入了解如何建立和部署 Service Fabric Mesh 應用程式，請繼續進行本教學課程。
> [!div class="nextstepaction"]
> [建立及部署多服務的 Web 應用程式](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group
[azure-cli-install]: /cli/azure/install-azure-cli?view=azure-cli-latest
