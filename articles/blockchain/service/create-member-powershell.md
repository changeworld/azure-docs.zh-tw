---
title: 建立 Azure 區塊鏈服務成員 - Azure PowerShell
description: 使用 Azure PowerShell 建立區塊鏈聯盟的 Azure 區塊鏈服務成員。
ms.date: 9/22/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 899778d60c32de7b0079e3858407c3e9fbed6f54
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91347968"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立 Azure 區塊鏈服務的區塊鏈成員

在本快速入門中，您會使用 Azure PowerShell 在 Azure 區塊鏈服務中部署新的區塊鏈成員和聯盟。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

如果您選擇在本機使用 PowerShell，本文會要求您安裝 Az PowerShell 模組，並使用 [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) Cmdlet 連線到您的 Azure 帳戶。 如需安裝 Az PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

> [!IMPORTANT]
> 雖然 **Az.Blockchain** PowerShell 模組處於預覽狀態，但您仍必須使用 `Install-Module` Cmdlet，將其與 Az PowerShell 模組分開安裝。 在此 PowerShell 模組正式推出後，會成為未來 Az PowerShell 模組版本的一部分，並可從 Azure Cloud Shell 內以原生方式使用。

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>註冊資源提供者

如果這是您第一次使用 Azure 區塊鏈服務，則必須註冊 **Microsoft.Blockchain** 資源提供者。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>選擇特定的 Azure 訂用帳戶

如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>定義變數

您將會重複使用數項資訊。 建立變數來儲存資訊。

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) Cmdlet 來建立 [Azure 資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 資源群組是一個邏輯容器，Azure 資源會在其中以群組方式部署及管理。

下列範例會根據 `$location` 變數所指定區域中 `$resourceGroupName` 變數中的名稱，建立資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>建立區塊鏈成員

Azure 區塊鏈服務成員是私人聯盟區塊鏈網路中的區塊鏈節點。
在佈建成員時，您可以建立或加入聯盟網路。 一個聯盟網路至少需要一個成員。 參與者所需的區塊鏈成員數目視您的案例而定。 聯盟參與者可以有一或多個區塊鏈成員，也可以與其他參與者共用成員。 如需聯盟的詳細資訊，請參閱 [Azure 區塊鏈服務聯盟](consortium.md)。

您必須傳入數個參數和屬性。 請將範例參數取代為您自己的值。

```azurepowershell-interactive
$passwd = Read-Host -Prompt 'Enter the members default transaction node password' -AsSecureString
$csPasswd = Read-Host -Prompt 'Enter  the consortium account password' -AsSecureString

$memberParams = @{
  Name = 'myblockchainmember'
  ResourceGroupName = $resourceGroupName
  Consortium = 'myconsortium'
  ConsortiumManagementAccountPassword = $csPasswd
  Location = $location
  Password = $passwd
  Protocol = 'Quorum'
  Sku = 'S0'
}
New-AzBlockchainMember @memberParams
```

| 參數 | 描述 |
|---------|-------------|
| **resourceGroupName** | Azure 區塊鏈服務資源建立所在的資源群組名稱。 請使用您在上一節中建立的資源群組。
| **名稱** | 可識別 Azure 區塊鏈服務區塊鏈成員的唯一名稱。 用於公開端點位址的名稱。 例如： `myblockchainmember.blockchain.azure.com` 。
| **位置** | 區塊鏈成員建立所在的 Azure 區域。 例如： `westus2` 。 選擇最靠近您的使用者或其他 Azure 應用程式的位置。 某些區域可能無法使用這些功能。 下列 Azure 區域提供 Azure 區塊鏈資料管理員服務：美國東部和西歐。
| **密碼** | 成員預設交易節點的密碼。 連線至區塊鏈成員的預設交易節點公用端點時，請使用此密碼進行基本驗證。
| **通訊協定** | 區塊鏈通訊協定。 目前支援 Quorum 通訊協定。
| **Consortium** | 要加入或建立的聯盟名稱。 如需聯盟的詳細資訊，請參閱 [Azure 區塊鏈服務聯盟](consortium.md)。
| **ConsortiumManagementAccountPassword** | 聯盟帳戶密碼也稱為成員帳戶密碼。 成員帳戶密碼可用來對為您的成員建立的 Ethereum 帳戶進行私密金鑰加密。 您可以使用帳戶成員和成員帳戶密碼進行聯盟管理。
| **Sku** | 服務層級類型。 **S0** 表示標準，**B0** 表示基本。 使用「基本」層來進行開發、測試和概念證明。 使用「標準」層來進行生產等級的部署。 如果您使用區塊鏈資料管理員或傳送大量的私人交易，也應該使用_標準_層。 不支援在建立成員之後變更基本和標準間的定價層。

建立區塊鏈成員和支援的資源需要約 10 分鐘的時間。

## <a name="clean-up-resources"></a>清除資源

您可以使用您建立的區塊鏈成員來進行下一個快速入門或教學課程。 當不再需要資源時，您可藉由刪除您為本快速入門建立的 `myResourceGroup` 資源群組來刪除資源。

> [!CAUTION]
> 下列範例會刪除指定的資源群組和其中包含的所有資源。
> 如果本文章範圍以外的資源存在於指定的資源群組中，則也會一併刪除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 Azure 區塊鏈服務成員和新的聯盟。 請嘗試進行下一個快速入門，使用適用於 Ethereum 的 Azure 區塊鏈開發套件連結至 Azure 區塊鏈服務成員。

> [!div class="nextstepaction"]
> [使用 Visual Studio Code 連線至聯盟網路](connect-vscode.md)
