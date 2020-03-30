---
title: 解決 Azure AD 域服務中的服務主體警報 |微軟文檔
description: 瞭解如何對 Azure 活動目錄域服務的服務主體配置警報進行故障排除
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: iainfou
ms.openlocfilehash: 175bfe63176b78c5aeafc7147c46dd5ab1110325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71257970"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>已知問題：Azure 活動目錄域服務中的服務主體警報

[服務主體](../active-directory/develop/app-objects-and-service-principals.md)是 Azure 平臺用於管理、更新和維護 Azure AD DS 託管域的應用程式。 如果刪除了服務主體，則 Azure AD DS 託管域中的功能將受到影響。

本文可説明您疑難排解並解決與服務主體相關的配置警報。

## <a name="alert-aadds102-service-principal-not-found"></a>警報 AADDS102：未找到服務主體

### <a name="alert-message"></a>警報消息

*Azure AD 域服務正常運行所需的服務主體已從 Azure AD 目錄中刪除。此配置會影響 Microsoft 監視、管理、修補和同步託管域的能力。*

如果刪除了所需的服務主體，Azure 平臺無法執行自動化管理工作。 Azure AD DS 託管域可能無法正確應用更新或進行備份。

### <a name="check-for-missing-service-principals"></a>檢查是否有遺失的服務主體

要檢查缺少哪些服務主體並需要重新創建，請完成以下步驟：

1. 在 Azure 門戶中，從左側導航功能表中選擇**Azure 活動目錄**。
1. 選取 [企業應用程式]****。 從 **"應用程式類型"** 下拉式功能表中選擇*所有應用程式*，然後選擇 **"應用**"。
1. 搜索每個應用程式指示。 如果未找到現有應用程式，請按照*解決方法*步驟創建服務主體或重新註冊命名空間。

    | 應用程式識別碼 | 解決方案 |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [重新創建缺少的服務主體](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [重新註冊 Microsoft.AAD 命名空間](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [重新註冊 Microsoft.AAD 命名空間](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [重新註冊 Microsoft.AAD 命名空間](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>重新創建缺少的服務主體

如果 Azure AD 目錄中缺少應用程式 ID *2565bd9d-da50-47d4-8b85-4c97f669dc36，* 請使用 Azure AD PowerShell 完成以下步驟。 有關詳細資訊，請參閱安裝[Azure AD 電源外殼](/powershell/azure/active-directory/install-adv2)。

1. 安裝 Azure AD PowerShell 模組並按照如下方式導入它：

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. 現在使用[New-AzureAdService 主體][New-AzureAdServicePrincipal]Cmdlet 重新創建服務主體：

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Azure AD DS 託管域的運行狀況會在兩小時內自動更新自身並刪除警報。

### <a name="re-register-the-microsoft-aad-namespace"></a>重新註冊微軟 AAD 命名空間

如果申請 ID *443155a6-77f3-45e3-882b-22b3a8d431fb* *，abba844e-bc0e-44b0-94 Azure*AD 目錄中缺少*d87dcbc6-a371-462e-88e3-28ad15ec4e64，* 完成以下步驟以重新註冊*Microsoft.AAD*資來源提供者：

1. 在 Azure 門戶中，搜索並選擇 **"訂閱**"。
1. 選擇與 Azure AD DS 託管域關聯的訂閱。
1. 從左側導航中，選擇**資來源提供者**。
1. 搜索*微軟.AAD*，然後選擇**重新註冊**。

Azure AD DS 託管域的運行狀況會在兩小時內自動更新自身並刪除警報。

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>警示 AADDS105：密碼同步處理應用程式已過期

### <a name="alert-message"></a>警報消息

*應用程式 ID"d87dcbc6-a371-462e-88e3-28ad15ec4e64"的服務主體被刪除，然後重新創建。娛樂會在 Azure AD 域服務資源上留下不一致的許可權，這些資源需要為託管域提供服務。託管域上的密碼同步可能會受到影響。*

Azure AD DS 會自動同步來自 Azure AD 的使用者帳戶和憑據。 如果用於此過程的 Azure AD 應用程式出現問題，則 Azure AD DS 和 Azure AD 之間的憑據同步將失敗。

### <a name="resolution"></a>解決方案

要重新創建用於憑據同步的 Azure AD 應用程式，請使用 Azure AD PowerShell 完成以下步驟。 有關詳細資訊，請參閱安裝[Azure AD 電源外殼](/powershell/azure/active-directory/install-adv2)。

1. 安裝 Azure AD PowerShell 模組並按照如下方式導入它：

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. 現在使用以下 PowerShell Cmdlet 刪除舊應用程式和物件：

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

刪除這兩個應用程式後，Azure 平臺會自動重新創建它們並嘗試恢復密碼同步。 Azure AD DS 託管域的運行狀況會在兩小時內自動更新自身並刪除警報。

## <a name="next-steps"></a>後續步驟

如果仍有問題，[請打開 Azure 支援請求][azure-support]以獲取其他故障排除説明。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
