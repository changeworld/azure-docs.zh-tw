---
title: 解決 Azure AD Domain Services 中的服務主體警示 |Microsoft Docs
description: 瞭解如何針對 Azure Active Directory Domain Services 的服務主體設定警示進行疑難排解
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "71257970"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>已知問題： Azure Active Directory Domain Services 中的服務主體警示

[服務主體](../active-directory/develop/app-objects-and-service-principals.md)是 Azure 平臺用來管理、更新和維護 Azure AD DS 受控網域的應用程式。 如果刪除服務主體，Azure AD DS 受控網域中的功能會受到影響。

本文可協助您疑難排解和解決與服務主體相關的設定警示。

## <a name="alert-aadds102-service-principal-not-found"></a>警示 AADDS102：找不到服務主體

### <a name="alert-message"></a>警示訊息

*已從您的 Azure AD 目錄中刪除 Azure AD Domain Services 正常運作所需的服務主體。此設定會影響 Microsoft 監視、管理、修補及同步處理受控網域的能力。*

如果已刪除必要的服務主體，Azure 平臺就無法執行自動化管理工作。 Azure AD DS 受控網域可能無法正確套用更新或進行備份。

### <a name="check-for-missing-service-principals"></a>檢查是否有遺失的服務主體

若要檢查遺失且需要重新建立的服務主體，請完成下列步驟：

1. 在 [Azure 入口網站中，從左側導覽功能表中選取 [ **Azure Active Directory** ]。
1. 選取 [企業應用程式]****。 從 [**應用程式類型**] 下拉式功能表**中選擇 [***所有應用程式*]，然後選取 [套用]。
1. 搜尋每個應用程式識別碼。 如果找不到任何現有的應用程式，請遵循*解決*步驟來建立服務主體，或重新註冊命名空間。

    | 應用程式識別碼 | 解決方法 |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [重新建立遺失的服務主體](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [重新註冊 Microsoft AAD 命名空間](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [重新註冊 Microsoft AAD 命名空間](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [重新註冊 Microsoft AAD 命名空間](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>重新建立遺失的服務主體

如果 Azure AD 目錄中缺少應用程式識別碼*2565bd9d-da50-47d4-8b85-4c97f669dc36* ，請使用 Azure AD PowerShell 來完成下列步驟。 如需詳細資訊，請參閱[install Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)。

1. 安裝 Azure AD PowerShell 模組並匯入它，如下所示：

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. 現在，使用[get-azureadserviceprincipal][New-AzureAdServicePrincipal] Cmdlet 重新建立服務主體：

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Azure AD DS 受控網域的健康狀態會在兩小時內自動更新，並移除警示。

### <a name="re-register-the-microsoft-aad-namespace"></a>重新註冊 Microsoft AAD 命名空間

如果 Azure AD 目錄中缺少應用程式識別碼*443155a6-77f3-45e3-882b-22b3a8d431fb*、 *abba844e-bc0e-44b0-947a-dc74e5d09022*或*d87dcbc6-a371-462e-88e3-28ad15ec4e64* ，請完成下列步驟以重新註冊*Microsoft AAD*資源提供者：

1. 在 [Azure 入口網站中，搜尋並選取 [**訂閱**]。
1. 選擇與您的 Azure AD DS 受控網域相關聯的訂用帳戶。
1. 從左側導覽中，選擇 [**資源提供者**]。
1. 搜尋 [ *MICROSOFT AAD*]，然後選取 [**重新註冊**]。

Azure AD DS 受控網域的健康狀態會在兩小時內自動更新，並移除警示。

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>警示 AADDS105：密碼同步處理應用程式已過期

### <a name="alert-message"></a>警示訊息

*已刪除應用程式識別碼為 "d87dcbc6-a371-462e-88e3-28ad15ec4e64" 的服務主體，然後重新建立。此重新產生會在服務受控網域所需的 Azure AD Domain Services 資源上留下不一致的許可權。您受控網域上的密碼同步處理可能會受到影響。*

Azure AD DS 會自動從 Azure AD 同步處理使用者帳戶和認證。 如果此程式所使用的 Azure AD 應用程式發生問題，Azure AD DS 與 Azure AD 之間的認證同步處理將會失敗。

### <a name="resolution"></a>解決方法

若要重新建立用於認證同步處理的 Azure AD 應用程式，請使用 Azure AD PowerShell 來完成下列步驟。 如需詳細資訊，請參閱[install Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)。

1. 安裝 Azure AD PowerShell 模組並匯入它，如下所示：

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. 現在，使用下列 PowerShell Cmdlet 來刪除舊的應用程式和物件：

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```

在您刪除這兩個應用程式之後，Azure 平臺會自動重新建立它們，並嘗試繼續密碼同步處理。 Azure AD DS 受控網域的健康狀態會在兩小時內自動更新，並移除警示。

## <a name="next-steps"></a>後續步驟

如果您仍有問題，請[開啟 Azure 支援要求][azure-support]以取得額外的疑難排解協助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
