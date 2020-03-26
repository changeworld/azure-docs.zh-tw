---
title: 快速入門 - 將 Docker 容器部署至容器執行個體 - PowerShell
description: 在本快速入門中，您可以使用 Azure PowerShell 快速地部署在隔離式 Azure 容器執行個體中執行的容器化的 Web 應用程式
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 9901b3f18973365dc9ceb8c85ff8587b6c2ea894
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "74533613"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 部署容器執行個體

使用 Azure 容器執行個體在 Azure 中簡潔且快速地執行無伺服器 Docker 容器。 當您不需要像 Azure Kubernetes Service 的完整容器協調流程平台時，請視需要將應用程式部署至容器執行個體。

在本快速入門中，您會使用 Azure PowerShell 部署隔離式 Windows 容器，並使用完整網域名稱 (FQDN) 讓其應用程式可供使用。 執行單一部署命令的幾秒之後，您可以瀏覽至容器中執行的應用程式：

![在瀏覽器中檢視部署至 Azure Container Instances 的應用程式][qs-powershell-01]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 PowerShell，本教學課程需要 Azure PowerShell 模組。 執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 容器執行個體和所有 Azure 資源相同，都必須部署到資源群組中。 資源群組可讓您組織和管理相關的 Azure 資源。

首先，使用下列 [New-AzResourceGroup][New-AzResourceGroup] 命令，在 *eastus* 位置中建立名為 *myResourceGroup* 的資源群組：

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>建立容器

有了資源群組之後，現在您可以在 Azure 中執行容器。 若要使用 Azure PowerShell 建立容器執行個體，請在 [New-AzContainerGroup][New-AzContainerGroup] Cmdlet 中提供資源群組名稱、容器執行個體名稱和 Docker 容器映像。 在此快速入門中，您可以使用公用 `mcr.microsoft.com/windows/servercore/iis:nanoserver` 映像。 此映像會封裝 Microsoft Internet Information Services (IIS) 以在 Nano Server 中執行。

您可以指定一或多個要開啟的連接埠和 (或) DNS 名稱標籤，以將您的容器公開至網際網路。 在本快速入門中，您會部署附有 DNS 名稱標籤的一個容器，以便公開觸達 IIS。

執行和下列類似的命令，以啟動容器執行個體。 設定 `-DnsNameLabel` 值，其在您建立執行個體所在的 Azure 區域中必須是唯一的。 如果出現「DNS 名稱標籤無法使用」錯誤訊息，請嘗試使用不同的 DNS 名稱標籤。

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

在幾秒內，您應該會從 Azure 收到回應。 一開始，容器會 `ProvisioningState` 會處於 [建立中]  狀態，但應該會在一兩分鐘內變成 [成功]  。 請使用 [Get-AzContainerGroup][Get-AzContainerGroup] Cmdlet 來查看部署狀態：

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

容器的佈建狀態、完整網域名稱 (FQDN) 和 IP 位址會顯示在 Cmdlet 的輸出中：

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

如果容器的 `ProvisioningState` 為 [成功]  ，請在瀏覽器中瀏覽至其 `Fqdn`。 如果您看到如下的網頁，恭喜您！ 您已將 Docker 容器中執行的應用程式成功部署至 Azure。

![在瀏覽器中檢視使用 Azure Container Instances 所部署的 IIS][qs-powershell-01]

## <a name="clean-up-resources"></a>清除資源

當容器使用完畢後，請使用 [Remove-AzContainerGroup][Remove-AzContainerGroup] Cmdlet 加以移除：

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已透過來自公用 Docker Hub 登錄中的映像建立 Azure 容器執行個體。 如果您想要建置容器映像，並從私人的 Azure 容器登錄進行部署，請繼續進行 Azure Container Instances 教學課程。

> [!div class="nextstepaction"]
> [Azure 容器執行個體教學課程](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
