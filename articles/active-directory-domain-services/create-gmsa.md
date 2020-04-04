---
title: Azure AD 網域服務的託管服務帳戶組 |微軟文件
description: 瞭解如何建立群組託管服務帳號 (gMSA) 以與 Azure 活動目錄域服務託管域一起使用
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
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655492"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>在 Azure AD 網域服務中建立群組服務帳號 (gMSA)

應用程式和服務通常需要一個標識來與其他資源進行身份驗證。 例如,Web 服務可能需要使用資料庫服務進行身份驗證。 如果應用程式或服務有多個實例(如 Web 伺服器場),則手動創建和配置這些資源的標識會非常耗時。

相反,可以在 Azure 活動目錄域服務 (Azure AD DS) 託管域中創建組託管服務帳戶 (gMSA)。 Windows 操作系統自動管理 gMSA 的認證,從而簡化了大型資源組的管理。

本文介紹如何使用 Azure PowerShell 在 Azure AD DS 託管域中創建 gMSA。

## <a name="before-you-begin"></a>開始之前

要完成本文,您需要以下資源和特權:

* 有效的 Azure 訂用帳戶。
    * 如果沒有 Azure 訂閱,[請建立帳號](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如果需要,請完成創建[和配置 Azure 活動目錄域服務實例][create-azure-ad-ds-instance]的教程。
* 加入到 Azure AD DS 託管域的 Windows 伺服器管理 VM。
    * 如果需要,請完成教學以建立[管理 VM][tutorial-create-management-vm]。

## <a name="managed-service-accounts-overview"></a>託管服務帳戶概述

獨立託管服務帳戶 (sMSA) 是自動管理其密碼的域帳戶。 此方法簡化了服務主體名稱 (SPN) 管理,並啟用委派管理給其他管理員。 您無需手動創建和輪換帳戶的憑據。

組託管服務帳戶 (gMSA) 提供相同的管理簡化,但為域中的多個伺服器提供相同的管理簡化。 gMSA 允許託管在伺服器場上的服務的所有實例使用相同的服務主體,以便相互身份驗證協定正常工作。 當 gMSA 用作服務主體時,Windows 操作系統將再次管理帳戶的密碼,而不是依賴管理員。

有關詳細資訊,請參閱[組託管服務帳戶 (gMSA) 概述][gmsa-overview]。

## <a name="using-service-accounts-in-azure-ad-ds"></a>在 Azure AD DS 中使用服務帳戶

由於 Azure AD DS 託管域由 Microsoft 鎖定和管理,因此在使用服務帳戶時需要考慮:

* 在託管域上的自定義組織單位 (OU) 中創建服務帳戶。
    * 不能在內置*的 AADDC 使用者*或*AADDC 計算機*O 中創建服務帳戶。
    * 而是在 Azure AD DS 託管域中[創建自訂 OU,][create-custom-ou]然後在該自定義 OU 中創建服務帳戶。
* 金鑰分發服務 (KDS) 根鍵是預先創建的。
    * KDS 根鍵用於生成和檢索 gMSA 的密碼。 在 Azure AD DS 中,將為您創建 KDS 根。
    * 您沒有創建另一個或查看預設 KDS 根鍵的許可權。

## <a name="create-a-gmsa"></a>建立群組 gMSA

首先,使用[新 AD 組織單位][New-AdOrganizationalUnit]cmdlet 創建自訂 OU。 有關創建和管理自定義 O 的詳細資訊,請參閱[Azure AD DS 中的自訂 O。][create-custom-ou]

> [!TIP]
> 要完成這些步驟以建立 gMSA,[請使用管理 VM][tutorial-create-management-vm]。 此管理 VM 應該已經具有所需的 AD PowerShell cmdlet 和與託管域的連接。

下面的範例在名為*aaddscontoso.com*的 Azure AD DS 託管域中創建名為*myNewOU*的自定義 OU。 使用您自己的 OU 和託管網域名稱:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=aaddscontoso,DC=COM"
```

現在使用[新 ADService 帳戶][New-ADServiceAccount]cmdlet 創建 gMSA。 定義以下範例參數:

* **-名稱**設定為*WebFarmSvc*
* **-路徑**參數為上一步中創建的 gMSA 指定自定義 OU。
* DNS 條目和服務主體名稱設置為*WebFarmSvc.aaddscontoso.com*
* 允許*AADDSCONTOSO-SERVER$* 中的主體使用識別檢索密碼。

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

應用程式和服務現在可以配置為根據需要使用 gMSA。

## <a name="next-steps"></a>後續步驟

有關 gMSA 的詳細資訊,請參閱[開始使用組託管服務帳戶][gmsa-start]。

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
