---
title: 部署 Azure AD Domain Services 的 Azure AD 應用程式 Proxy |Microsoft Docs
description: 瞭解如何藉由在 Azure Active Directory Domain Services 受控網域中部署和設定 Azure Active Directory 應用程式 Proxy，為遠端工作者提供內部應用程式的安全存取
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: c1dc5216f758c2dda263e2f61b043dbde5f76604
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80655494"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>部署 Azure AD 應用程式 Proxy，以安全存取 Azure AD Domain Services 受控網域中的內部應用程式

有了 Azure AD Domain Services （Azure AD DS），您就可以將在內部部署執行的繼承應用程式隨即轉移至 Azure。 Azure Active Directory （AD）應用程式 Proxy 接著會透過安全地發佈 Azure AD DS 受控網域中的內部應用程式部分，協助您支援遠端工作者，以便透過網際網路存取。

如果您不熟悉 Azure AD 應用程式 Proxy，而且想要深入瞭解，請參閱[如何提供內部應用程式的安全遠端存取](../active-directory/manage-apps/application-proxy.md)。

本文說明如何建立和設定 Azure AD 應用程式 Proxy 連接器，以提供 Azure AD DS 受控網域中應用程式的安全存取。

## <a name="before-you-begin"></a>開始之前

若要完成本文，您需要下列資源和許可權：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
    * 需要**Azure AD Premium 授權**才能使用 Azure AD 應用程式 Proxy。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance]。

## <a name="create-a-domain-joined-windows-vm"></a>建立已加入網域的 Windows VM

若要將流量路由傳送至在您的環境中執行的應用程式，您必須安裝 Azure AD 應用程式 Proxy 連接器元件。 此 Azure AD 應用程式 Proxy 連接器必須安裝在已加入 Azure AD DS 受控網域的 Windows Server 虛擬機器（VM）上。 針對某些應用程式，您可以部署多部已安裝連接器的伺服器。 這個部署選項為您提供更高的可用性，並協助處理更大量的驗證負載。

執行 Azure AD 應用程式 Proxy 連接器的 VM 必須位於已啟用 Azure AD DS 的相同或對等互連虛擬網路上。 然後，裝載您使用應用程式 Proxy 發佈之應用程式的 Vm 也必須部署在相同的 Azure 虛擬網路上。

若要建立 Azure AD 應用程式 Proxy 連接器的 VM，請完成下列步驟：

1. [建立自訂 OU](create-ou.md)。 您可以將管理這個自訂 OU 的許可權委派給 Azure AD DS 受控網域中的使用者。 Azure AD 應用程式 Proxy 以及執行應用程式的 Vm，都必須是自訂 OU 的一部分，而不是預設的*AAD DC 電腦*OU。
1. [將虛擬機器][create-join-windows-vm]（執行 Azure AD 應用程式 Proxy 連接器，以及執行應用程式的電腦）加入至 Azure AD DS 受控網域。 在上一個步驟的自訂 OU 中建立這些電腦帳戶。

## <a name="download-the-azure-ad-application-proxy-connector"></a>下載 Azure AD 應用程式 Proxy 連接器

請執行下列步驟來下載 Azure AD 應用程式 Proxy 連接器。 您下載的安裝檔會在下一節中複製到您的應用程式 Proxy VM。

1. 使用在 Azure AD 中具有*企業系統管理員*許可權的使用者帳戶登入[Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取入口網站頂端的 [ **Azure Active Directory** ]，然後選擇 [**企業應用程式**]。
1. 從左側功能表中選取 [**應用程式 proxy** ]。 若要建立您的第一個連接器並啟用應用程式 Proxy，請選取連結以**下載連接器**。
1. 在 [下載] 頁面上，接受授權條款和隱私權合約，然後選取 [**接受條款 & 下載**]。

    ![下載 Azure AD App Proxy 連接器](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>安裝並註冊 Azure AD 應用程式 Proxy 連接器

準備好 VM 作為 Azure AD 應用程式 Proxy 連接器使用之後，現在請複製並執行從 Azure 入口網站下載的安裝程式檔案。

1. 將 Azure AD 應用程式 Proxy 連接器安裝程式檔案複製到您的 VM。
1. 執行安裝檔，例如*aadapplicationproxyconnectorinstaller.exe。* 接受軟體授權條款。
1. 在安裝期間，系統會提示您在 Azure AD 目錄中向應用程式 Proxy 註冊連接器。
   * 在您的 Azure AD 目錄中提供全域管理員的認證。 Azure AD 的全域系統管理員認證可能與您在入口網站中的 Azure 認證不同

        > [!NOTE]
        > 用來註冊連接器的全域系統管理員帳戶必須屬於您啟用應用程式 Proxy 服務的相同目錄。
        >
        > 例如，如果 Azure AD 網域是*aaddscontoso.com*，則全域管理員應該是`admin@aaddscontoso.com`或該網域上的另一個有效別名。

   * 如果已針對您安裝連接器的 VM 開啟 Internet Explorer 增強式安全性設定，則可能會封鎖註冊畫面。 若要允許存取，請遵循錯誤訊息中的指示，或在安裝過程中關閉 Internet Explorer 增強式安全性。
   * 如果連接器註冊失敗，請參閱針對[應用程式 Proxy 進行疑難排解](../active-directory/manage-apps/application-proxy-troubleshoot.md)。
1. 在安裝結束時，會顯示具有輸出 proxy 之環境的附注。 若要設定 Azure AD 應用程式 Proxy 連接器以透過輸出 Proxy 運作，請執行提供的腳本，例如`C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`。
1. 在 Azure 入口網站的 [應用程式 proxy] 頁面上，新的連接器會列示為 [*作用中] 狀態，如*下列範例所示：

    ![新的 Azure AD 應用程式 Proxy 連接器在 Azure 入口網站中顯示為作用中](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> 若要為透過 Azure AD 應用程式 Proxy 進行驗證的應用程式提供高可用性，您可以在多個 Vm 上安裝連接器。 重複上一節所列的相同步驟，在加入 Azure AD DS 受控網域的其他伺服器上安裝連接器。

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>啟用以資源為基礎的 Kerberos 限制委派

如果您想要對使用整合式 Windows 驗證（IWA）的應用程式使用單一登入，請授與 Azure AD 應用程式 Proxy 連接器許可權以模擬使用者，並代表其傳送和接收權杖。 若要授與這些許可權，您可以設定連接器的 Kerberos 限制委派（KCD），以存取 Azure AD DS 受控網域上的資源。 因為您在 Azure AD DS 受控網域中沒有網域系統管理員許可權，所以無法在受控網域上設定傳統帳戶層級 KCD。 請改為使用以資源為基礎的 KCD。

如需詳細資訊，請參閱[在 Azure Active Directory Domain Services 中設定 Kerberos 限制委派（KCD）](deploy-kcd.md)。

> [!NOTE]
> 您必須登入屬於您 Azure AD 租使用者中*AZURE AD DC 系統管理員*群組成員的使用者帳戶，才能執行下列 PowerShell Cmdlet。
>
> 您的應用程式 Proxy 連接器 VM 和應用程式 Vm 的電腦帳戶必須位於您有權設定以資源為基礎之 KCD 的自訂 OU 中。 您無法在內建*AAD DC 電腦*容器中，為電腦帳戶設定以資源為基礎的 KCD。

使用[get-adcomputer][Get-ADComputer]來抓取安裝 Azure AD 應用程式 Proxy 連接器之電腦的設定。 從已加入網域的管理 VM，並以屬於*AZURE AD DC administrators*群組成員的使用者帳戶登入，執行下列 Cmdlet。

下列範例會取得名為*appproxy.aaddscontoso.com*之電腦帳戶的相關資訊。 為先前步驟中設定的 Azure AD 應用程式 Proxy VM 提供您自己的電腦名稱稱。

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

針對在 Azure AD 應用程式 Proxy 後方執行應用程式的每個應用程式伺服器，使用[Get-adcomputer][Set-ADComputer] PowerShell Cmdlet 來設定以資源為基礎的 KCD。 在下列範例中，Azure AD 應用程式 Proxy 連接器會被授與使用*appserver.aaddscontoso.com*電腦的許可權：

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

如果您部署多個 Azure AD 應用程式 Proxy 連接器，您必須為每個連接器實例設定以資源為基礎的 KCD。

## <a name="next-steps"></a>後續步驟

使用與 Azure AD DS 整合的 Azure AD 應用程式 Proxy，發佈應用程式供使用者存取。 如需詳細資訊，請參閱[使用 Azure AD 應用程式 Proxy 發佈應用程式](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
