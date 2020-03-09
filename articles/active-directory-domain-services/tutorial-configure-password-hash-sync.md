---
title: 為 Azure AD Domain Services 啟用密碼雜湊同步 | Microsoft Docs
description: 在本教學課程中，請了解如何啟用使用 Azure AD Connect 將密碼雜湊同步處理至 Azure Active Directory Domain Services 受控網域的功能。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 93e5ee9b46fb3387b70dd5092f72efcaa8a2bc19
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78669391"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>教學課程：在混合式環境的 Azure Active Directory Domain Services 中啟用密碼同步處理

在混合式環境中，您可以將 Azure Active Directory (Azure AD) 租用戶設定為可使用 Azure AD Connect 與內部部署 Active Directory Domain Services (AD DS) 環境進行同步處理。 根據預設，Azure AD Connect 不會同步處理 Azure Active Directory Domain Services (Azure AD DS) 所需的舊版 NT LAN Manager (NTLM) 和 Kerberos 密碼雜湊。

若要搭配使用 Azure AD DS 與從內部部署 AD DS 環境同步處理的帳戶，您必須設定 Azure AD Connect，以同步處理 NTLM 和 Kerberos 驗證所需的密碼雜湊。 設定 Azure AD Connect 之後，內部部署帳戶的建立或密碼變更事件後續也會將舊版密碼雜湊同步處理至 Azure AD。

如果您使用沒有內部部署 AD DS 環境的僅限雲端帳戶，則不需要執行這些步驟。

在此教學課程中，您將會學到：

> [!div class="checklist"]
> * 為何需要舊版 NTLM 和 Kerberos 密碼雜湊
> * 如何設定 Azure AD Connect 的舊版密碼雜湊同步處理

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)再開始。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列資源：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 一個與您的訂用帳戶相關聯，且使用 Azure AD Connect 與內部部署目錄同步處理的 Azure Active Directory 租用戶。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
    * 如有需要，請[啟用 Azure AD Connect 以進行密碼雜湊同步處理][enable-azure-ad-connect]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance]。

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>使用 Azure AD Connect 的密碼雜湊同步處理

Azure AD Connect 可用來將內部部署 AD DS 環境中的物件 (例如使用者帳戶和群組) 同步處理至 Azure AD 租用戶。 在此過程中，密碼雜湊同步處理可讓帳戶在內部部署 AD DS 環境和 Azure AD 中使用相同的密碼。

若要在受控網域上驗證使用者，Azure AD DS 需要格式適用於 NTLM 和 Kerberos 驗證的密碼雜湊。 除非您為租用戶啟用 Azure AD DS，否則 Azure AD 不會以 NTLM 或 Kerberos 驗證所需的格式儲存密碼雜湊。 基於安全性考量，Azure AD 也不會儲存任何純文字形式的密碼認證。 因此，Azure AD 無法根據使用者的現有認證自動產生這些 NTLM 或 Kerberos 密碼雜湊。

Azure AD Connect 可以設定成會同步處理 Azure AD DS 所需的 NTLM 或 Kerberos 密碼雜湊。 請確定您已完成[啟用 Azure AD Connect 以進行密碼雜湊同步處理][enable-azure-ad-connect]的步驟。 如果您有 Azure AD Connect 的現有執行個體，請[下載並更新至最新版本][azure-ad-connect-download]，以確定您可以同步處理 NTLM 和 Kerberos 的舊版密碼雜湊。 此功能無法在舊版的 Azure AD Connect 中使用，或與舊版 DirSync 工具搭配使用。 您必須要有 Azure AD Connect *1.1.614.0* 版或更新版本。

> [!IMPORTANT]
> Azure AD Connect 應該只會為了與內部部署 AD DS 環境同步處理而安裝和設定。 不支援在 Azure AD DS 受控網域中安裝 Azure AD Connect，以將物件同步處理回 Azure AD。

## <a name="enable-synchronization-of-password-hashes"></a>啟用密碼雜湊的同步處理

現在，Azure AD Connect 已安裝並設定為與 Azure AD 同步處理，接下來請設定 NTLM 和 Kerberos 的舊版密碼雜湊同步。 PowerShell 指令碼可用來設定必要的設定，然後開始對 Azure AD 進行完整的密碼同步處理。 當 Azure AD Connect 密碼雜湊同步處理程序完成後，使用者將可透過使用舊版 NTLM 或 Kerberos 密碼雜湊的 Azure AD DS 來登入應用程式。

1. 在已安裝 Azure AD Connect 的電腦上，從 [開始] 功能表開啟 [Azure AD Connect] > [同步處理服務]。
1. 選取 [**連接器**] 索引標籤。會列出用來建立內部部署 AD DS 環境與 Azure AD 之間同步處理的連接資訊。

    [類型] 會顯示為 [Windows Azure Active Directory (Microsoft)] (用於 Azure AD 連接器) 或 [Active Directory Domain Services] (用於內部部署 AD DS 連接器)。 請記下連接器名稱，以便在下一個步驟的 PowerShell 指令碼中使用。

    ![在 Sync Service Manager 中列出連接器名稱](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    在此範例螢幕擷取畫面中，會使用下列連接器：

    * Azure AD 連接器名為*aaddscontoso.onmicrosoft.com-AAD*
    * 內部部署 AD DS 連接器的名稱為 *onprem.contoso.com*

1. 將下列 PowerShell 指令碼複製並貼到已安裝 Azure AD Connect 的電腦。 指令碼會觸發包含舊版密碼雜湊的完整密碼同步作業。 使用上一個步驟中的連接器名稱來更新 `$azureadConnector` 和 `$adConnector` 變數。

    在每個 AD 樹系上執行此指令碼，以將內部部署帳戶 NTLM 和 Kerberos 密碼雜湊同步處理至 Azure AD。

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    將舊版密碼雜湊同步處理至 Azure AD 可能需要一些時間，視您的目錄大小而定 (取決於帳戶和群組的數目)。 密碼在同步處理至 Azure AD 之後，就會同步處理至 Azure AD DS 受控網域。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解：

> [!div class="checklist"]
> * 為何需要舊版 NTLM 和 Kerberos 密碼雜湊
> * 如何設定 Azure AD Connect 的舊版密碼雜湊同步處理

> [!div class="nextstepaction"]
> [了解 Azure AD Domain Services 受控網域中的同步如何運作](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
