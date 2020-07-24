---
title: 使用 PowerShell 來部署和管理通知中樞
description: 如何使用 PowerShell 來建立和管理通知中樞以進行自動化
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: f000251009bda730b15458f3ab4d8b7d6ca1db6d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030295"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>使用 PowerShell 來部署和管理通知中樞

## <a name="overview"></a>概觀

本文將說明如何使用 PowerShell 來建立和管理 Azure 通知中樞。 本文示範下列一般自動化工作。

- 建立通知中樞
- 設定認證

如果您也需要為通知中樞建立新服務匯流排命名空間，請參閱 [使用 PowerShell 管理服務匯流排](../service-bus-messaging/service-bus-manage-with-ps.md)。

Azure PowerShell 隨附的 Cmdlet 無法直接支援「管理通知中樞」。 從 PowerShell 進行的最佳方法，是參考 Microsoft.Azure.NotificationHubs.dll 組件。 組件隨附於 [Microsoft Azure 通知中樞 NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 Azure 是訂閱型平台。 如需取得訂用帳戶的詳細資訊，請參閱[購買選項]、[成員供應項目]或[免費試用版]。
- 具備 Azure PowerShell 的電腦。 如需指示，請參閱 [安裝並設定 Azure PowerShell]。
- 大致了解 PowerShell 指令碼、NuGet 封裝和 .NET Framework。

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>包括對服務匯流排之 .NET 組件的參考

Azure PowerShell 中的 PowerShell Cmdlet 尚未提供「管理 Azure 通知中樞」。 若要佈建通知中樞，可以使用 [Microsoft Azure 通知中樞 NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)中所提供的 .NET 用戶端。

首先，請確定指令碼可以找到 **Microsoft.Azure.NotificationHubs.dll** 組件，其在 Visual Studio 專案中會以 NuGet 套件的形式安裝。 為了要有使用彈性，指令碼會執行這些步驟：

1. 判斷叫用的路徑。
2. 周遊路徑，直到找到名為 `packages` 的資料夾為止。 當您安裝 Visual Studio 專案的 NuGet 封裝時，會建立這個資料夾。
3. 以遞迴方式在 `packages` 資料夾中搜尋名為 `Microsoft.Azure.NotificationHubs.dll` 的組件。
4. 參考組件，以供稍後使用這些類型。

以下是如何使用 PowerShell 指令碼實作這些步驟的方式：

``` powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>建立 `NamespaceManager` 類別

若要佈建通知中樞，請從 SDK 建立 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager?view=azure-dotnet) 類別的執行個體。

您可以使用 Azure PowerShell 隨附的 [Get-AzureSBAuthorizationRule] Cmdlet 來擷取用來提供連接字串的授權規則。 `$NamespaceManager` 變數會儲存 `NamespaceManager` 執行個體的參照。 使用 `$NamespaceManager` 佈建通知中樞。

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>佈建新的通知中樞

若要佈建新的通知中樞，請使用 [通知中樞的 .NET API]。

您會在指令碼的這個部分設定四個區域變數。

1. `$Namespace`:將此變數設定為要在其中建立通知中樞的命名空間名稱。
2. `$Path`:將此路徑設定為新的通知中樞名稱。  例如，"MyHub"。
3. `$WnsPackageSid`:從 [Windows 開發人員中心](https://developer.microsoft.com/en-us/windows)，將此變數設定為 Windows 應用程式的套件 SID。
4. `$WnsSecretkey`:從 [Windows 開發人員中心](https://developer.microsoft.com/en-us/windows)，將此變數設定為 Windows 應用程式的祕密金鑰。

這些變數可用以連接命名空間，以及建立新的通知中樞，並將其設定為利用 WNS 認證，為 Windows 應用程式處理 Windows Notification Services (WNS) 通知。 如需取得封裝 SID 與祕密金鑰的相關資訊，請參閱 [開始使用通知中樞](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 教學課程。

- 指令碼片段使用 `NamespaceManager` 物件來查看 `$Path` 所識別的通知中樞是否存在。
- 如果不存在，指令碼會使用 WNS 認證建立 `NotificationHubDescription`，並將其傳遞至 `NamespaceManager` 類別 `CreateNotificationHub` 方法。

``` powershell
$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```

## <a name="additional-resources"></a>其他資源

- [使用 PowerShell 管理服務匯流排](../service-bus-messaging/service-bus-manage-with-ps.md)
- [如何使用 PowerShell 指令碼來建立服務匯流排佇列、主題及訂閱](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
- [如何使用 PowerShell 指令碼來建立服務匯流排命名空間與事件中樞](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)

也有一些現成的指令碼可供下載：

- [服務匯流排 PowerShell 指令碼](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[購買選項]: https://azure.microsoft.com/pricing/purchase-options/
[成員供應項目]: https://azure.microsoft.com/pricing/member-offers/
[免費試用版]: https://azure.microsoft.com/pricing/free-trial/
[安裝並設定 Azure PowerShell]: /powershell/azure/
[通知中樞的 .NET API]: /dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/get-azuresbnamespace
[New-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: /powershell/module/servicemanagement/azure.service/get-azuresbauthorizationrule
