---
title: 使用 powershell 安裝 Azure AD Connect 雲端布建代理程式
description: 瞭解如何使用 powershell Cmdlet 安裝 Azure AD Connect 雲端布建代理程式。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20cfb96b5cd6e1d05e332fa7157fe6e0cde8656
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613329"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>使用 powershell Cmdlet 安裝 Azure AD Connect 布建代理程式 
下列檔將說明如何使用 PowerShell Cmdlet 安裝 Azure AD Connect 布建代理程式。
 

## <a name="prerequisite"></a>必要條件： 


>[!IMPORTANT]
>下列安裝指示假設您已符合所有[必要條件](how-to-prerequisites.md)。
>
> 在您使用 powershell Cmdlet 安裝 Azure AD Connect 布建代理程式之前，windows server 必須先啟用 TLS 1.2。 若要啟用 TLS 1.2，您可以使用 [這裡](how-to-prerequisites.md#tls-requirements)找到的步驟。

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>使用 powershell Cmdlet 安裝 Azure AD Connect 布建代理程式 


 1. 登入 Azure 入口網站，然後移至 **Azure Active Directory**。
 2. 在左側功能表中，選取 [ **Azure AD Connect**]。
 3. 選取 [管理佈建 (預覽)] > [檢閱所有代理程式]。
 4. 從 Azure 入口網站將 Azure AD Connect 布建代理程式下載到本機。  

   ![下載內部部署代理程式](media/how-to-install/install-9.png)</br>
 5. 基於這些指示的目的，會將代理程式下載到下列資料夾： "C:\ProvisioningSetup" 資料夾。 
 6. 以無訊息模式安裝 ProvisioningAgent

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. 匯入布建代理程式 PS 模組 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll" 
   ```
 8. 使用全域管理員認證連接到 AzureAD，您可以自訂此區段以從安全存放區提取密碼。 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   Connect-AADCloudSyncAzureAD-認證 $globalAdminCreds 

 9. 新增 gMSA 帳戶，並提供網域系統管理員的認證，以建立預設 gMSA 帳戶 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. 或使用上述 Cmdlet 來提供預先建立的 gMSA 帳戶 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. 新增網域 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. 或使用上述 Cmdlet 來設定慣用的網域控制站 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. 重複上述步驟來新增更多網域，請提供個別網域的帳戶名稱和功能變數名稱 
 14. 重新啟動服務 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  移至 azure 入口網站以建立雲端同步設定。

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>布建代理程式 gMSA PowerShell Cmdlet
現在您已安裝代理程式，您可以對 gMSA 套用更細微的許可權。  如需有關設定許可權的詳細資訊和逐步指示，請參閱 [Azure AD Connect 雲端布建代理程式 GMSA PowerShell Cmdlet](how-to-gmsa-cmdlets.md) 。

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [Azure AD Connect 雲端布建代理程式 gMSA PowerShell Cmdlet](how-to-gmsa-cmdlets.md)
- [什麼是雲端同步 Azure AD Connect？](what-is-cloud-sync.md)