---
title: Azure AD Domain Services 的群組受管理的服務帳戶 |Microsoft Docs
description: 瞭解如何建立群組受管理的服務帳戶（gMSA），以與 Azure Active Directory Domain Services 受控網域搭配使用
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 5955f52cda73630f371a46f83ac0fb9a252b80e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655492"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>在 Azure AD Domain Services 中建立群組受管理的服務帳戶（gMSA）

應用程式和服務通常需要身分識別，才能向其他資源進行驗證。 例如，web 服務可能需要向資料庫服務進行驗證。 如果應用程式或服務有多個實例（例如 web 伺服器陣列），則手動建立及設定這些資源的身分識別會耗費大量時間。

相反地，您可以在 Azure Active Directory Domain Services （Azure AD DS）受控網域中建立群組受管理的服務帳戶（gMSA）。 Windows 作業系統會自動管理 gMSA 的認證，這可簡化大型資源群組的管理。

本文說明如何使用 Azure PowerShell 在 Azure AD DS 受控網域中建立 gMSA。

## <a name="before-you-begin"></a>開始之前

若要完成本文，您需要下列資源和許可權：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請完成教學課程，以[建立及設定 Azure Active Directory Domain Services 實例][create-azure-ad-ds-instance]。
* 已加入 Azure AD DS 受控網域的 Windows Server 管理 VM。
    * 如有需要，請完成教學課程以[建立管理 VM][tutorial-create-management-vm]。

## <a name="managed-service-accounts-overview"></a>受管理的服務帳戶總覽

獨立受管理的服務帳戶（sMSA）是自動管理其密碼的網域帳戶。 這種方法可簡化服務主體名稱（SPN）管理，並為其他系統管理員提供委派管理。 您不需要手動建立及輪替帳戶的認證。

群組受管理的服務帳戶（gMSA）提供相同的管理簡化，但適用于網域中的多部伺服器。 GMSA 讓裝載在伺服器陣列上的服務的所有實例都使用相同的服務主體來進行相互驗證通訊協定。 當使用 gMSA 做為服務主體時，Windows 作業系統會再次管理帳戶的密碼，而不是依賴系統管理員。

如需詳細資訊，請參閱[群組受管理的服務帳戶（gMSA）總覽][gmsa-overview]。

## <a name="using-service-accounts-in-azure-ad-ds"></a>使用 Azure AD DS 中的服務帳戶

由於 Azure AD DS 受控網域已鎖定且由 Microsoft 管理，因此在使用服務帳戶時，會有一些考慮：

* 在受控網域上的自訂群組織單位（OU）中建立服務帳戶。
    * 您無法在內建的*AADDC Users*或*AADDC 電腦*ou 中建立服務帳戶。
    * 相反地，請在 Azure AD DS 受控網域中[建立自訂 ou][create-custom-ou] ，然後在該自訂 ou 中建立服務帳戶。
* 金鑰發佈服務（KDS 根金鑰）的根金鑰是預先建立的。
    * KDS 根金鑰根金鑰是用來產生和取出 Gmsa 的密碼。 在 Azure AD DS 中，系統會為您建立 KDS 根金鑰根目錄。
    * 您沒有許可權可建立另一個，或看 KDS 根金鑰的根機碼。

## <a name="create-a-gmsa"></a>建立群組 gMSA

首先，使用[ADOrganizationalUnit][New-AdOrganizationalUnit] Cmdlet 來建立自訂 OU。 如需建立和管理自訂 Ou 的詳細資訊，請參閱[AZURE AD DS 中的自訂 ou][create-custom-ou]。

> [!TIP]
> 若要完成這些步驟來建立 gMSA，請[使用您的管理 VM][tutorial-create-management-vm]。 此管理 VM 應該已有必要的 AD PowerShell Cmdlet 和與受控網域的連線。

下列範例會在名為*aaddscontoso.com*的 Azure AD DS 受控網域中建立名為*myNewOU*的自訂 OU。 使用您自己的 OU 和受控功能變數名稱：

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

現在使用[uninstall-adserviceaccount][New-ADServiceAccount] Cmdlet 來建立 gMSA。 定義的範例參數如下：

* **-Name**設為*WebFarmSvc*
* **-Path**參數會針對在上一個步驟中建立的 gMSA 指定自訂 OU。
* 已針對*WebFarmSvc.aaddscontoso.com*設定 DNS 專案和服務主體名稱
* *AADDSCONTOSO-SERVER $* 中的主體可讓您取得密碼，使用身分識別。

指定您自己的名稱和功能變數名稱。

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.aaddscontoso.com `
    -Path "OU=MYNEWOU,DC=aaddscontoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.aaddscontoso.com/aaddscontoso.com, `
        http/WebFarmSvc.aaddscontoso.com/aaddscontoso, `
        http/WebFarmSvc/aaddscontoso.com, `
        http/WebFarmSvc/aaddscontoso `
    -PrincipalsAllowedToRetrieveManagedPassword AADDSCONTOSO-SERVER$
```

應用程式和服務現在可以設定為視需要使用 gMSA。

## <a name="next-steps"></a>後續步驟

如需 Gmsa 的詳細資訊，請參閱[開始使用群組受管理的服務帳戶][gmsa-start]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
