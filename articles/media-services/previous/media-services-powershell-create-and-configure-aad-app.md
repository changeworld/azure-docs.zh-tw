---
title: 使用 PowerShell 建立 Azure AD 應用程式，以存取 Azure 媒體服務 API | Microsoft Docs
description: 了解如何使用 PowerShell 建立 Azure Active Directory (Azure AD) 應用程式，並設定它以存取 Azure 媒體服務 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: ff7f8bc27d358c667b10c0bd3383e78b20494303
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "64680125"
---
# <a name="use-powershell-to-create-an-azure-ad-app-to-use-with-the-azure-media-services-api"></a>使用 PowerShell 建立 Azure AD 應用程式，以搭配 Azure 媒體服務 API 使用

> [!NOTE]
> 媒體服務 v2 不會再新增任何新的特性或功能。 <br/>請查看最新版本，[媒體服務 v3](https://docs.microsoft.com/azure/media-services/latest/)。 另請參閱[從 v2 到 v3 的遷移指引](../latest/migrate-from-v2-to-v3.md)

了解如何使用 PowerShell 指令碼建立 Azure Active Directory (Azure AD) 應用程式和服務主體，以存取 Azure 媒體服務資源。  

## <a name="prerequisites"></a>Prerequisites

- 一個 Azure 帳戶。 如果您沒有帳戶，請先從 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 
- 媒體服務帳戶。 如需詳細資訊，請參閱[在 Azure 入口網站建立 Azure 媒體服務帳戶](media-services-portal-create-account.md)。

- Azure PowerShell。 如需詳細資訊，請參閱[如何使用 Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) \(英文\)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-an-azure-ad-app-by-using-powershell"></a>使用 PowerShell 建立 Azure AD 應用程式  

```powershell
Connect-AzAccount
Import-Module Az.Resources
Set-AzContext -SubscriptionId $SubscriptionId
$ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password

Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 
$NewRole = $null
$Scope = "/subscriptions/your subscription id/resourceGroups/userresourcegroup/providers/microsoft.media/mediaservices/your media account"

$Retries = 0;While ($NewRole -eq $null -and $Retries -le 6)
{
    # Sleep here for a few seconds to allow the service principal application to become active (usually, it will take only a couple of seconds)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ServicePrincipalName $ServicePrincipal.ApplicationId -ErrorAction SilentlyContinue
    $Retries++;
}
```

如需詳細資訊，請參閱下列文章：

- [使用 Azure PowerShell 建立用來存取資源的服務主體](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
- [使用 Azure PowerShell 管理角色型存取控制](../../role-based-access-control/role-assignments-powershell.md)
- [如何使用憑證手動設定精靈應用程式](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md#add-the-certificate-as-a-key-for-the-todolistdaemonwithcert-application-in-azure-ad) \(英文\)

## <a name="next-steps"></a>後續步驟

開始使用[上傳檔案至您的帳戶](media-services-portal-upload-files.md)。
