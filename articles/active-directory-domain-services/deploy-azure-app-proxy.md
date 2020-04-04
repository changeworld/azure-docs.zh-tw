---
title: 為 Azure AD 網域服務部署 Azure AD 應用程式代理 |微軟文件
description: 瞭解如何透過 Azure 活動目錄網域服務託管域中部署和設定 Azure 活動目錄應用程式代理,為遠端工作人員提供安全存取
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
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655494"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>部署 Azure AD 應用程式代理,以安全存取 Azure AD 網域管理系統中的內部應用程式

使用 Azure AD 網域服務(Azure AD DS),可以將本地運行的舊應用程式提升到 Azure 中。 然後,Azure 活動目錄 (AD) 應用程式代理通過安全地發佈 Azure AD DS 託管域的這些內部應用程式,以便可以通過 Internet 訪問這些應用程式,從而説明您支援遠端工作。

如果您是 Azure AD 應用程式代理的新功能,並且想要瞭解詳細資訊,請參閱[如何提供安全遠端存取內部應用程式](../active-directory/manage-apps/application-proxy.md)。

本文介紹如何創建和配置 Azure AD 應用程式代理連接器,以提供安全訪問 Azure AD DS 託管域中的應用程式。

## <a name="before-you-begin"></a>開始之前

要完成本文,您需要以下資源和特權:

* 有效的 Azure 訂用帳戶。
    * 如果沒有 Azure 訂閱,[請建立帳號](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
    * 使用 Azure AD 應用程式代理需要**Azure AD 進階授權**。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，請[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance]。

## <a name="create-a-domain-joined-windows-vm"></a>建立加入網域的 Windows VM

要將流量路由到環境中運行的應用程式,請安裝 Azure AD 應用程式代理連接器元件。 此 Azure AD 應用程式代理連接器必須安裝在已加入 Azure AD DS 託管域的 Windows 伺服器虛擬機器 (VM) 上。 對於某些應用程式,可以部署多個安裝了連接器的伺服器。 這個部署選項為您提供更高的可用性，並協助處理更大量的驗證負載。

運行 Azure AD 應用程式代理連接器的 VM 必須位於啟用 Azure AD DS 的同一虛擬網路上。 然後承載使用應用程式代理發佈的應用程式的 VM 也必須部署在同一 Azure 虛擬網路上。

要為 Azure AD 應用程式代理連線器建立 VM,請完成以下步驟:

1. [建立自訂 OU](create-ou.md)。 您可以將管理此自定義 OU 的權限委派給 Azure AD DS 託管域中的使用者。 Azure AD 應用程式代理的 VM 和執行應用程式的 VM 必須是自訂 OU 的一部分,而不是預設*的 AAD DC 電腦*OU。
1. [域將虛擬機器][create-join-windows-vm](執行 Azure AD 應用程式代理連接器的虛擬機器)和執行應用程式的虛擬機器連線連接到 Azure AD DS 託管域。 從上一步在自定義 OU 中創建這些計算機帳戶。

## <a name="download-the-azure-ad-application-proxy-connector"></a>下載 Azure AD 應用程式代理連線器

執行以下步驟以下載 Azure AD 應用程式代理連接器。 您下載的設定檔將複製到應用程式代理 VM 的下一部分。

1. 使用在 Azure AD 中具有*企業管理員*權限的使用者帳戶登入[Azure 門戶](https://portal.azure.com)。
1. 在門戶頂部搜尋並選擇**Azure 活動目錄**,然後選擇**企業應用程式**。
1. 從左方的選單中選擇**應用程式代理**程式 。 要建立第一個連接器並啟用應用程式, 請選擇連結以**下載連接器**。
1. 在下載頁面上,接受許可條款和隱私協議,然後選擇 **「接受條款&下載**」。。

    ![下載 Azure AD 應用程式代理連線器](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>安裝並註冊 Azure AD 應用程式代理連接器

準備好將 VM 用作 Azure AD 應用程式代理連接器,現在複製並運行從 Azure 入口下載的安裝程式檔案。

1. 將 Azure AD 應用程式代理連接器設定檔案複製到 VM。
1. 執行設定檔,如*AAD 應用程式代理連線器安裝程式.exe*。 接受軟體授權條款。
1. 在安裝過程中,系統會提示您將連接器註冊到 Azure AD 目錄中的應用程式代理。
   * 在 Azure AD 目錄中為全域管理員提供認證。 Azure AD 全域管理員認證可能與門戶中的 Azure 認證不同

        > [!NOTE]
        > 用於註冊連接器的全域管理員帳戶必須屬於啟用應用程式代理服務的同一目錄。
        >
        > 例如,如果 Azure AD 功能*是 aaddscontoso.com,* 則`admin@aaddscontoso.com`全域管理員應 為該網域上的另一個有效別名。

   * 如果為安裝連接器的 VM 啟用 Internet Explorer 增強的安全配置,則註冊螢幕可能會被阻止。 要允許存取,請按照錯誤訊息中的說明操作,或在安裝過程中關閉 Internet Explorer 增強的安全性。
   * 如果連接器註冊失敗,請參閱[排除應用程式代理容錯](../active-directory/manage-apps/application-proxy-troubleshoot.md)。
1. 在設置結束時,將顯示一個註釋,用於具有出站代理的環境。 要將 Azure AD 應用程式代理連線器設定為透過出站代理工作,請執行的`C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`文稿, 如 。
1. 在 Azure 門戶中的應用程式代理頁上,新連接器以*活動*狀態列出,如以下範例所示:

    ![在 Azure 門戶中顯示為活動的新 Azure AD 應用程式代理連接器](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> 要為透過 Azure AD 應用程式代理進行身份驗證的應用程式提供高可用性,可以在多個 VM 上安裝連接器。 重複上一節中列出的相同步驟,以在加入到 Azure AD DS 託管域的其他伺服器上安裝連接器。

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>開啟基於資源的 Kerberos 限制委派

如果要使用整合 Windows 身份驗證 (IWA) 對應用程式使用單一登入,請授予 Azure AD 應用程式代理連接器權限以模擬使用者並代表他們發送和接收權杖。 要授予這些許可權,請為連接器配置 Kerberos 約束委派 (KCD) 以造訪 Azure AD DS 託管域上的資源。 由於 Azure AD DS 託管域中沒有域管理員許可權,因此無法在託管域上配置傳統的帳戶級 KCD。 相反,請使用基於資源的 KCD。

有關詳細資訊,請參閱在[Azure 活動目錄域服務中配置 Kerberos 約束委派 (KCD)。](deploy-kcd.md)

> [!NOTE]
> 您必須登入 Azure AD 租戶中的 Azure AD DC*管理員*組的成員的使用者帳戶才能運行以下 PowerShell cmdlet。
>
> 應用程式代理連線器 VM 和應用程式 VM 的電腦帳戶必須位於自訂 OU 中,您可以在其中設定基於資源的 KCD 的許可權。 無法為內建*AAD DC 計算機*容器中的電腦帳戶配置基於資源的 KCD。

使用[Get-AD 計算機][Get-ADComputer]檢索安裝 Azure AD 應用程式代理連接器的計算機的設置。 從加入網域的管理 VM 中作為*Azure AD DC 管理員*組的成員登錄的使用者帳戶,運行以下 cmdlet。

下面的範例獲取有關名為*appproxy.aaddscontoso.com*的電腦帳戶的資訊。 為在前面的步驟中配置的 Azure AD 應用程式代理 VM 提供您自己的電腦名稱。

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

對於運行 Azure AD 應用程式代理後面的應用的每個應用程式伺服器,使用[Set-AD 電腦][Set-ADComputer]PowerShell cmdlet 配置基於資源的 KCD。 在以下範例中,Azure AD 應用程式代理連接器被授予使用*appserver.aaddscontoso.com*電腦的許可權:

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

如果部署多個 Azure AD 應用程式代理連接器,則必須為每個連接器實例配置基於資源的 KCD。

## <a name="next-steps"></a>後續步驟

將 Azure AD 應用程式代理與 Azure AD DS 整合後,發佈應用程式供使用者存取。 有關詳細資訊,請參閱使用[Azure AD 應用程式代理發佈應用程式](../active-directory/manage-apps/application-proxy-publish-azure-portal.md)。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
