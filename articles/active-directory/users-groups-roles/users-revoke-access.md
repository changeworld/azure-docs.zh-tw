---
title: 在 Azure Active Directory 的緊急情況下撤銷使用者存取權 |Microsoft Docs
description: 在 Azure AD 系統管理中心的 Azure Active Directory 中大量新增使用者
services: active-directory
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.topic: how-to
author: curtand
ms.author: curtand
manager: daveba
ms.reviewer: krbain
ms.date: 07/15/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb2ad8e6d37190d0473f3f9f4af7738edd3b27f
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475204"
---
# <a name="revoke-user-access-in-azure-active-directory"></a>撤銷 Azure Active Directory 中的使用者存取

在可能需要系統管理員撤銷使用者所有存取權的案例中，包括遭入侵的帳戶、員工終止和其他內部威脅。 視環境的複雜度而定，系統管理員可以採取幾個步驟，以確保存取被撤銷。 在某些情況下，存取撤銷的初始時間與存取權有效撤銷之間可能會有一段期間。

若要降低風險，您必須瞭解權杖的工作方式。 有許多類型的權杖，屬於下列各節所述的其中一種模式。

## <a name="access-tokens-and-refresh-tokens"></a>存取權杖和重新整理權杖

存取權杖和重新整理權杖經常用於豐富的用戶端應用程式，也用於以瀏覽器為基礎的應用程式，例如單一頁面應用程式。

- 當使用者向 Azure AD 進行驗證時，系統會評估授權原則，以判斷使用者是否可以被授與特定資源的存取權。  

- 若已獲得授權，Azure AD 會發出資源的存取權杖和重新整理權杖。  

- Azure AD 預設發行的權杖為1小時。 如果驗證通訊協定允許，應用程式可以在存取權杖過期時，將重新整理權杖傳遞至 Azure AD，以無訊息方式重新驗證使用者。

Azure AD 然後重新評估其授權原則。 如果使用者仍獲得授權，Azure AD 會發出新的存取權杖和重新整理權杖。

如果存取權必須在比權杖存留期短的時間內撤銷，則存取權杖可能會是安全性考慮，這通常會在一小時左右。 基於這個理由，Microsoft 正積極將[持續存取評估](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)帶入 Office 365 應用程式，這有助於確保存取權杖以近乎即時的方式運作。  

## <a name="session-tokens-cookies"></a>會話權杖（cookie）

大部分以瀏覽器為基礎的應用程式都使用會話權杖，而不是存取和重新整理權杖。  

- 當使用者開啟瀏覽器，並透過 Azure AD 向應用程式驗證時，使用者會收到兩個會話權杖。 一個來自 Azure AD，另一個來自應用程式。  

- 一旦應用程式發出自己的會話權杖，應用程式的存取權就會受到應用程式的會話所控制。 此時，只有應用程式知道的授權原則會影響使用者。

- Azure AD 的授權原則會在應用程式將使用者傳回 Azure AD 時經常重新評估。 重新評估通常會以無訊息模式進行，不過頻率取決於應用程式的設定方式而定。 只要會話權杖有效，應用程式可能永遠不會將使用者傳回 Azure AD。

- 若要撤銷會話權杖，應用程式必須根據自己的授權原則來撤銷存取權。 Azure AD 無法直接撤銷應用程式所發出的會話權杖。  

## <a name="revoke-access-for-a-user-in-the-hybrid-environment"></a>在混合式環境中撤銷使用者的存取權

針對內部部署 Active Directory 與 Azure Active Directory 同步處理的混合式環境，Microsoft 建議 IT 系統管理員採取下列動作。  

### <a name="on-premises-active-directory-environment"></a>內部部署 Active Directory 環境

身為 Active Directory 的系統管理員，請連接到您的內部部署網路，開啟 PowerShell，並採取下列動作：

1. 停用 Active Directory 中的使用者。 請參閱[ADAccount](https://docs.microsoft.com/powershell/module/addsadministration/disable-adaccount?view=win10-ps)。

    ```PowerShell
    Disable-ADAccount -Identity johndoe  
    ```

1. 在 Active Directory 中，將使用者的密碼重設兩次。 請參閱[set-adaccountpassword](https://docs.microsoft.com/powershell/module/addsadministration/set-adaccountpassword?view=win10-ps)。

    > [!NOTE]
    > 變更使用者密碼兩次的原因是要降低傳遞雜湊的風險，特別是當內部部署密碼複寫延遲時。 如果您可以安全地假設此帳戶不會遭到入侵，您可以只重設密碼一次。

    > [!IMPORTANT] 
    > 請勿使用下列 Cmdlet 中的範例密碼。 請務必將密碼變更為隨機字串。

    ```PowerShell
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd1" -Force)
    Set-ADAccountPassword -Identity johndoe -Reset -NewPassword (ConvertTo-SecureString -AsPlainText "p@ssw0rd2" -Force)
    ```

### <a name="azure-active-directory-environment"></a>Azure Active Directory 環境

身為 Azure Active Directory 的系統管理員，請開啟 PowerShell，執行 ``Connect-AzureAD`` ，並採取下列動作：

1. 停用 Azure AD 中的使用者。 請參閱[new-azureaduser](https://docs.microsoft.com/powershell/module/azuread/Set-AzureADUser?view=azureadps-2.0)。

    ```PowerShell
    Set-AzureADUser -ObjectId johndoe@contoso.com -AccountEnabled $false
    ```
1. 撤銷使用者的 Azure AD 重新整理權杖。 請參閱[Revoke-AzureADUserAllRefreshToken](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。

    ```PowerShell
    Revoke-AzureADUserAllRefreshToken -ObjectId johndoe@contoso.com
    ```

1. 停用使用者的裝置。 請參閱[AzureADUserRegisteredDevice](https://docs.microsoft.com/powershell/module/azuread/get-azureaduserregistereddevice?view=azureadps-2.0)。

    ```PowerShell
    Get-AzureADUserRegisteredDevice -ObjectId johndoe@contoso.com | Set-AzureADDevice -AccountEnabled $false
    ```

## <a name="optional-steps"></a>選擇性步驟

- [從 Intune 管理的應用程式抹除公司資料](https://docs.microsoft.com/mem/intune/apps/apps-selective-wipe)。

- 抹除[公司擁有的裝置會將裝置重設為原廠預設設定](https://docs.microsoft.com/mem/intune/remote-actions/devices-wipe)。

> [!NOTE]
> 抹除之後，裝置上的資料就無法復原。

## <a name="when-access-is-revoked"></a>撤銷存取權時

一旦系統管理員採取上述步驟，使用者就無法為任何系結至 Azure Active Directory 的應用程式取得新的權杖。 撤銷和使用者失去存取權之間經過的時間，取決於應用程式如何授與存取權：

- 對於**使用存取權杖的應用程式**，當存取權杖到期時，使用者會失去存取權。

- 對於**使用會話權杖的應用程式**，現有的會話會在權杖到期時立即結束。 如果使用者的停用狀態已同步處理至應用程式，應用程式可以自動撤銷使用者現有的會話（如果已設定的話）。  所需的時間取決於應用程式和 Azure AD 之間的同步處理頻率。

## <a name="next-steps"></a>後續步驟

- [Azure AD 系統管理員的安全存取作法](directory-admin-roles-secure.md)
- [新增或更新使用者設定檔資訊](../fundamentals/active-directory-users-profile-azure-portal.md)
