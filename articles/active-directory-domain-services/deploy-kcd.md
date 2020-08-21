---
title: Azure AD Domain Services 的 Kerberos 限制委派 |Microsoft Docs
description: 瞭解如何在 Azure Active Directory Domain Services 受控網域中 (KCD) 啟用以資源為基礎的 Kerberos 限制委派。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 7f1640f74c3fca27e1d992f3e2cc538f6e2c7fef
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722988"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>在 Azure Active Directory Domain Services 中設定 Kerberos 限制委派 (KCD) 

當您執行應用程式時，這些應用程式可能需要存取不同使用者的內容中的資源。 Active Directory Domain Services (AD DS) 支援稱為 *Kerberos 委派* 的機制，可啟用此使用案例。 Kerberos *限制* 委派 (KCD) 然後以此機制為基礎，以定義可在使用者內容中存取的特定資源。

Azure Active Directory Domain Services (Azure AD DS) 受控網域會比傳統內部部署 AD DS 環境更安全地鎖定，因此請使用更安全的 *資源型* KCD。

本文說明如何在 Azure AD DS 受控網域中設定以資源為基礎的 Kerberos 限制委派。

## <a name="prerequisites"></a>Prerequisites

若要完成本文，您需要下列資源：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
* 已加入 Azure AD DS 受控網域的 Windows Server 管理 VM。
    * 如有需要，請完成教學課程以 [建立 Windows SERVER VM，並將它加入受控網域][create-join-windows-vm] ，然後 [安裝 AD DS 管理工具][tutorial-create-management-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員群組成員的使用者帳戶。

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos 限制委派總覽

Kerberos 委派可讓一個帳戶模擬另一個帳戶以存取資源。 例如，存取後端 web 元件的 web 應用程式可以在建立後端連接時，以不同的使用者帳戶來模擬本身。 Kerberos 委派並不安全，因為它不會限制模擬帳戶可以存取的資源。

Kerberos *限制* 委派 (KCD) 會限制指定的伺服器或應用程式在模擬另一個身分識別時可連接的服務或資源。 傳統 KCD 需要有網域系統管理員許可權，才能為服務設定網域帳戶，而且會將帳戶限制在單一網域上執行。

傳統 KCD 也有一些問題。 例如，在舊版作業系統中，服務系統管理員沒有實用的方式可知道哪些前端服務被委派給他們擁有的資源服務。 任何可以委派給資源服務的前端服務都是潛在的攻擊點。 如果裝載設定為委派給資源服務之前端服務的伺服器遭到入侵，則資源服務也可能受到危害。

在受控網域中，您沒有網域系統管理員許可權。 因此，傳統以帳戶為基礎的 KCD 無法在受控網域中設定。 您可以改為使用以資源為基礎的 KCD，也就是更安全的。

### <a name="resource-based-kcd"></a>資源型 KCD

Windows Server 2012 和更新版本讓服務系統管理員能夠設定其服務的限制委派。 此模型稱為資源型 KCD。 使用此方法時，後端服務系統管理員可以允許或拒絕特定前端服務使用 KCD。

按資源 KCD 使用 PowerShell 來設定。 視模擬帳戶是電腦帳戶或使用者帳戶/服務帳戶而定，您可以使用 [Set-ADComputer][Set-ADComputer] 或 [Set-ADUser][Set-ADUser] Cmdlet。

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>為電腦帳戶設定以資源為基礎的 KCD

在此案例中，假設您有一個在名為 *contoso-webapp.aaddscontoso.com*的電腦上執行的 web 應用程式。

Web 應用程式必須存取在網域使用者內容中名為 *contoso-api.aaddscontoso.com* 的電腦上執行的 web API。

請完成下列步驟來設定此案例：

1. [建立自訂 OU](create-ou.md)。 您可以將管理這個自訂 OU 的權限委派給受控網域內的使用者。
1. 將虛擬機器（執行 web 應用程式的虛擬機器）和執行 web API 的[虛擬機器加入網域][create-join-windows-vm]，以將其加入受控網域。 從上一個步驟的自訂 OU 建立這些電腦帳戶。

    > [!NOTE]
    > Web 應用程式和 web API 的電腦帳戶必須位於您有權設定以資源為基礎之 KCD 的自訂 OU 內。 您無法在內建的 *AAD DC 電腦* 容器中，為電腦帳戶設定以資源為基礎的 KCD。

1. 最後，使用 [Get-adcomputer][Set-ADComputer] PowerShell Cmdlet 來設定以資源為基礎的 KCD。

    從已加入網域的管理 VM，並以屬於 *AZURE AD DC 系統管理員* 群組成員身分的使用者帳戶登入，執行下列 Cmdlet。 視需要提供您自己的電腦名稱稱：
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>為使用者帳戶設定以資源為基礎的 KCD

在此案例中，讓我們假設您有一個以名為 *appsvc*的服務帳戶執行的 web 應用程式。 Web 應用程式必須存取在網域使用者的環境中，以名為 *backendsvc* 的服務帳戶執行的 web API。 請完成下列步驟來設定此案例：

1. [建立自訂 OU](create-ou.md)。 您可以將管理這個自訂 OU 的權限委派給受控網域內的使用者。
1. 將執行後端 web API/資源[的虛擬機器網域加入][create-join-windows-vm]至受控網域。 請在自訂 OU 內建立其電腦帳戶。
1. 建立服務帳戶 (例如，用來在自訂 OU 內執行 web 應用程式的 *appsvc*) 。

    > [!NOTE]
    > 同樣地，web API VM 的電腦帳戶和 web 應用程式的服務帳戶，都必須位於您有權設定以資源為基礎之 KCD 的自訂 OU 中。 您無法針對內建 *AAD Dc 電腦* 或 *AAD dc 使用者* 容器中的帳戶設定以資源為基礎的 KCD。 這也表示您無法使用從 Azure AD 同步處理的使用者帳戶來設定以資源為基礎的 KCD。 您必須建立並使用特別在 Azure AD DS 中建立的服務帳戶。

1. 最後，使用 [Set-aduser][Set-ADUser] PowerShell Cmdlet 來設定以資源為基礎的 KCD。

    從已加入網域的管理 VM，並以屬於 *AZURE AD DC 系統管理員* 群組成員身分的使用者帳戶登入，執行下列 Cmdlet。 視需要提供您自己的服務名稱：

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>後續步驟

若要深入瞭解委派在 Active Directory Domain Services 中的運作方式，請參閱 [Kerberos 限制委派總覽][kcd-technet]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)