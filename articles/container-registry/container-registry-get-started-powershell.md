---
title: 快速入門 - 建立登錄 - PowerShell
description: 快速了解如何使用 PowerShell 在 Azure Container Registry 中建立私人 Docker 登錄
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: ed0790f79ef4a83f42519ce6f89ee36f54558f4b
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752176"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立私人容器登錄

Azure Container Registry 是受控的私用 Docker 容器登錄服務，用於建立、儲存及提供 Docker 容器映像。 在此快速入門中，您會學到如何使用 PowerShell 建立 Azure 容器登錄。 然後，使用 Docker 命令將容器映像推送到登錄中，最後從您的登錄中提取映像並加以執行。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本快速入門需要 Azure PowerShell 模組。 執行 `Get-Module -ListAvailable Az` 來判斷您安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

您也必須在本機上安裝 Docker。 Docker 提供 [macOS][docker-mac]、[Windows][docker-windows] 及 [Linux][docker-linux] 系統適用的套件。

由於 Azure Cloud Shell 未包含所有必要的 Docker 元件 (`dockerd` 精靈)，因此您無法使用本快速入門中的 Cloud Shell。

## <a name="sign-in-to-azure"></a>登入 Azure

使用 [Connect-AzAccount][Connect-AzAccount] 命令登入 Azure 訂用帳戶，並依照畫面上的指示操作。

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>建立資源群組

通過 Azure 的驗證後，請使用 [New-AzResourceGroup][New-AzResourceGroup] 建立資源群組。 資源群組是一種邏輯容器，您可在其中部署與管理 Azure 資源。

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>建立容器登錄

接著，使用 [New-AzContainerRegistry][New-AzContainerRegistry] 命令在新的資源群組中建立容器登錄。

登錄名稱在 Azure 內必須是唯一的，且包含 5-50 個英數字元。 下列範例會建立名為「myContainerRegistry007」的登錄。 在下列命令中取代 *myContainerRegistry007*，然後執行命令建立登錄：

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

您在本快速入門中會建立「基本」登錄，這是正在學習 Azure Container Registry 的開發人員所適用的成本最佳化選項。 如需可用服務層級的詳細資訊，請參閱[容器登錄服務層][container-registry-skus]。

## <a name="log-in-to-registry"></a>登入登錄

推送和提取容器映像之前，您必須先登入登錄。 在生產案例中，您應使用個別的身分識別或服務主體進行容器登錄存取，但若要盡快完成本快速入門，請使用 [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] 命令在您的登錄上啟用管理使用者：

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

接下來，執行 [docker login][docker-login] 以進行登入：

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

完成後，此命令會傳回 `Login Succeeded`。

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>清除資源

當您使用在本快速入門中建立的資源完成工作後，請使用 [Remove-AzResourceGroup][Remove-AzResourceGroup] 命令移除資源群組、容器登錄，以及儲存於該處的容器映像：

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已使用 Azure PowerShell 建立 Azure Container Registry、推送容器映像，以及從登錄中提取映像並加以執行。 請繼續進行 Azure 容器登錄教學課程，以深入了解 ACR。

> [!div class="nextstepaction"]
> [Azure Container Registry 教學課程][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure Container Registry 工作教學課程][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
