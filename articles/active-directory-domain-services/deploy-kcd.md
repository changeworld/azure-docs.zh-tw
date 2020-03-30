---
title: Azure AD 域服務的 Kerberos 受限委派 |微軟文檔
description: 瞭解如何在 Azure 活動目錄域服務託管域中啟用基於資源的 Kerberos 限制委派 （KCD）。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 216fdeca9893f4e290474512617f13382d22890f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614006"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>在 Azure 活動目錄域服務中配置 Kerberos 限制委派 （KCD）

運行應用程式時，可能需要這些應用程式在其他使用者的上下文中訪問資源。 活動目錄域服務 （AD DS） 支援一種稱為*Kerberos 委派*的機制，該機制啟用此用例。 然後，Kerberos*約束*委派 （KCD） 在此基礎上構建，以定義可在使用者上下文中訪問的特定資源。 Azure 活動目錄域服務 （Azure AD DS） 託管域比傳統的本地 AD DS 環境更安全地鎖定，因此使用更安全的*基於資源的*KCD。

本文介紹如何在 Azure AD DS 託管域中配置基於資源的 Kerberos 限制委派。

## <a name="prerequisites"></a>Prerequisites

要完成本文，您需要以下資源：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance]。
* 加入到 Azure AD DS 託管域的 Windows 伺服器管理 VM。
    * 如果需要，請完成[創建 Windows 伺服器 VM 的教程並將其加入託管域，][create-join-windows-vm]然後[安裝 AD DS 管理工具][tutorial-create-management-vm]。
* 屬於您 Azure AD 租用戶中 Azure AD DC 系統管理員** 群組成員的使用者帳戶。

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos 限制委派概述

Kerberos 委派允許一個帳戶類比另一個帳戶以訪問資源。 例如，訪問後端 Web 元件的 Web 應用程式在建立後端連接時可以將自己類比為不同的使用者帳戶。 Kerberos 委派不安全，因為它不限制類比帳戶可以訪問的資源。

Kerberos 限制委派 （KCD） 限制指定伺服器或應用程式在類比其他標識時可以連接的服務或資源。 傳統的 KCD 需要域管理員許可權來佈建服務的域帳戶，並且它將帳戶限制在單個域上運行。

傳統的 KCD 也有一些問題。 例如，在早期的作業系統中，服務管理員沒有有用的方法來知道委託給其擁有的資源服務的前端服務。 任何可以委派給資源服務的前端服務都可能是攻擊點。 如果託管配置為委派為資源服務的前端服務的伺服器受到威脅，則資源服務也可能受到威脅。

在 Azure AD DS 託管域中，您沒有域管理員許可權。 因此，無法在 Azure AD DS 託管域中配置基於帳戶的傳統 KCD。 可改為使用基於資源的 KCD，這也更安全。

### <a name="resource-based-kcd"></a>資源型 KCD

Windows Server 2012 及更高版本使服務管理員能夠為其服務配置受約束的委派。 此模型稱為資源型 KCD。 使用此方法，後端服務管理員可以允許或拒絕使用 KCD 的特定前端服務。

資源型 KCD 是使用 PowerShell 所設定的。 您可以使用[Set-ADComputer][Set-ADComputer]或[Set-ADUser][Set-ADUser] Cmdlet，具體取決於類比帳戶是電腦帳戶還是使用者帳戶/服務帳戶。

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>為電腦帳戶配置基於資源的 KCD

在這種情況下，假設您有一個在名為*contoso-webapp.aaddscontoso.com*的電腦上運行的 Web 應用。 Web 應用需要訪問在域使用者上下文中名為*contoso-api.aaddscontoso.com*的電腦上運行的 Web API。 完成以下步驟以配置此方案：

1. [建立自訂 OU](create-ou.md)。 您可以將管理此自訂 OU 的許可權委派給 Azure AD DS 託管域中的使用者。
1. [域將虛擬機器][create-join-windows-vm]（運行 Web 應用的虛擬機器）和運行 Web API 的虛擬機器連接到 Azure AD DS 託管域。 從上一步在自訂 OU 中創建這些電腦帳戶。

    > [!NOTE]
    > Web 應用的電腦帳戶和 Web API 必須位於自訂 OU 中，您可以在其中配置基於資源的 KCD 的許可權。 無法為內置*AAD DC 電腦*容器中的電腦帳戶配置基於資源的 KCD。

1. 最後，使用[Set-AD 電腦][Set-ADComputer]電源 Shell Cmdlet 配置基於資源的 KCD。 從加入域的管理 VM 中作為*Azure AD DC 管理員*組的成員登錄的使用者帳戶，運行以下 Cmdlet。 根據需要提供您自己的電腦名稱稱：
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.aaddscontoso.com
    Set-ADComputer contoso-api.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>為使用者帳戶配置基於資源的 KCD

在這種情況下，假設您有一個 Web 應用，該應用作為名為*appvc*的服務帳戶運行。 Web 應用需要訪問在域使用者的上下文中作為名為*backendsvc*的服務帳戶運行的 Web API。 完成以下步驟以配置此方案：

1. [建立自訂 OU](create-ou.md)。 您可以將管理此自訂 OU 的許可權委派給 Azure AD DS 託管域中的使用者。
1. [域將][create-join-windows-vm]運行後端 Web API/資源的虛擬機器加入 Azure AD DS 託管域。 請在自訂 OU 內建立其電腦帳戶。
1. 建立用來執行自訂 OU 內 Web 應用程式的服務帳戶 (例如 'appsvc')。

    > [!NOTE]
    > 同樣，Web API VM 的電腦帳戶和 Web 應用的服務帳戶必須位於自訂 OU 中，您有權配置基於資源的 KCD。 不能為內置*AAD DC 電腦*或*AAD DC 使用者*容器中的帳戶配置基於資源的 KCD。 這也意味著不能使用從 Azure AD 同步的使用者帳戶來設置基於資源的 KCD。 您必須創建和使用在 Azure AD DS 中專門創建的服務帳戶。

1. 最後，使用[Set-ADUser][Set-ADUser] PowerShell Cmdlet 配置基於資源的 KCD。 從加入域的管理 VM 中作為*Azure AD DC 管理員*組的成員登錄的使用者帳戶，運行以下 Cmdlet。 根據需要提供您自己的服務名稱：

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>後續步驟

要瞭解有關委派在活動目錄域服務中的工作方式的詳細資訊，請參閱[Kerberos 限制委派概述][kcd-technet]。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
