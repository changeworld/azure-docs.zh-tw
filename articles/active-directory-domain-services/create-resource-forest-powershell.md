---
title: 使用 Azure PowerShell 建立 Azure AD Domain Services 資源樹系 |Microsoft Docs
description: 在本文中，您將瞭解如何使用 Azure PowerShell，建立 Azure Active Directory Domain Services 資源樹系和輸出樹系，並將其設定為內部部署 Active Directory Domain Services 環境。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: iainfou
ms.openlocfilehash: d5eef553d0d3bf5acbcb61ef8f2dcfab88a53266
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505766"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>使用 Azure PowerShell 建立內部部署網域的 Azure Active Directory Domain Services 資源樹系和輸出樹系信任

在無法同步處理密碼雜湊的環境中，或您有使用智慧卡以獨佔方式登入，因此不知道密碼的使用者，可以使用 Azure Active Directory Domain Services (Azure AD DS) 中的資源樹系。 資源樹系會使用從 Azure AD DS 到一或多個內部部署 AD DS 環境的單向輸出信任。 此信任關係可讓使用者、應用程式和電腦向 Azure AD DS 受控網域中的內部部署網域進行驗證。 在資源樹系中，內部部署密碼雜湊永遠不會同步處理。

![從 Azure AD DS 到內部部署 AD DS 的樹系信任圖表](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

在本文中，您將學會如何：

> [!div class="checklist"]
> * 使用 Azure PowerShell 建立 Azure AD DS 資源樹系
> * 使用 Azure PowerShell 在受控網域中建立單向輸出樹系信任
> * 在內部部署 AD DS 環境中設定 DNS，以支援受控網域連線能力
> * 在內部部署 AD DS 環境中建立單向的輸入樹系信任
> * 測試和驗證信任關係以進行驗證和資源存取

如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)再開始。

> [!IMPORTANT]
> 受控網域資源樹系目前不支援 Azure HDInsight 或 Azure 檔案儲存體。 預設的受控網域使用者樹系支援這兩個額外的服務。

## <a name="prerequisites"></a>Prerequisites

若要完成本文章，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。

* 安裝並設定 Azure PowerShell。
    * 請依需要遵循指示，[安裝 Azure PowerShell 模組並連線至 Azure 訂用帳戶](/powershell/azure/install-az-ps)。
    * 務必使用 [Connect-AzAccount][Connect-AzAccount] Cmdlet 登入您的 Azure 訂用帳戶。
* 安裝並設定 Azure AD PowerShell。
    * 請依需要遵循指示，[安裝 Azure AD PowerShell 模組並連線至 Azure AD](/powershell/azure/active-directory/install-adv2)。
    * 務必使用 [Connect-AzureAD][Connect-AzureAD] Cmdlet 登入您的 Azure AD 租用戶。
* 您必須擁有 Azure AD 租用戶的*全域管理員*權限，才能啟用 Azure AD DS。
* 您需要 Azure 訂用帳戶中的「參與者」權限，才能建立必要的 Azure AD DS 資源。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

在本文中，您會使用 Azure 入口網站，從受控網域建立和設定輸出樹系信任。 若要開始使用，請先登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="deployment-process"></a>部署程序

這是一個多部分的程式，可建立受控網域資源樹系和內部部署 AD DS 的信任關係。 下列高階步驟會建立您信任的混合式環境：

1. 建立受控網域服務主體。
1. 建立受控網域資源樹系。
1. 使用站對站 VPN 或 Express Route 建立混合式網路連線能力。
1. 建立信任關係的受控網域端。
1. 建立信任關係的內部部署 AD DS 端。

開始之前，請確定您瞭解[網路考慮、樹系命名和 DNS 需求](tutorial-create-forest-trust.md#networking-considerations)。 部署之後，就無法變更受控網域樹系名稱。

## <a name="create-the-azure-ad-service-principal"></a>建立 Azure AD 服務主體

Azure AD DS 需要服務主體從 Azure AD 同步處理資料。 建立受控網域資源樹系之前，必須先在您的 Azure AD 租使用者中建立此主體。

建立 Azure AD DS 的 Azure AD 服務主體，以進行通訊並自行驗證。 系統會使用特定的應用程式識別碼，其名稱為「網域控制站服務」以及 2565bd9d-da50-47d4-8b85-4c97f669dc36 的識別碼。 請勿變更此應用程式識別碼。

使用 [New-AzureADServicePrincipal][New-AzureADServicePrincipal] Cmdlet 來建立 Azure AD 服務主體：

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="create-a-managed-domain-resource-forest"></a>建立受控網域資源樹系

若要建立受控網域資源樹系，您可以使用 `New-AzureAaddsForest` 腳本。 此腳本屬於更廣泛的命令集，可支援建立和管理受控網域資源樹系，包括在下一節中建立單向系結樹系。 這些腳本可從[PowerShell 資源庫](https://www.powershellgallery.com/)取得，並由 Azure AD 工程小組以數位方式簽署。

1. 首先，使用[remove-azresourcegroup][New-AzResourceGroup] Cmdlet 來建立資源群組。 在下列範例中，會在 westus 區域中建立名為 myResourceGroup 的資源群組。 使用您自己的名稱和所需的區域：

    ```azure-powershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. `New-AaddsResourceForestTrust`使用[安裝腳本][Install-Script]Cmdlet 從[PowerShell 資源庫][powershell-gallery]安裝腳本：

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. 請檢查腳本所需的下列參數 `New-AzureAaddsForest` 。 請確定您也有必要的**Azure PowerShell**和**Azure AD PowerShell**模組。 請確定您已規劃虛擬網路需求，以提供應用程式和內部部署連線能力。

    | Name                         | 腳本參數          | 描述 |
    |:-----------------------------|---------------------------|:------------|
    | 訂用帳戶                 | *-azureSubscriptionId*    | 用於 Azure AD DS 帳單的訂用帳戶識別碼。 您可以使用[AzureRMSubscription][Get-AzureRMSubscription] Cmdlet 來取得訂用帳戶的清單。 |
    | 資源群組               | *-aaddsResourceGroupName* | 受控網域和相關聯資源的資源組名。 |
    | Location                     | *-aaddsLocation*          | 要裝載受控網域的 Azure 區域。 如需可用的區域，請參閱[AZURE AD DS 的支援區域。](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all) |
    | Azure AD DS 系統管理員    | *-aaddsAdminUser*         | 第一個受管理網域系統管理員的使用者主體名稱。 此帳戶必須是您 Azure Active Directory 中的現有雲端使用者帳戶。 使用者和執行腳本的使用者會新增至*AAD DC 系統管理員*群組。 |
    | Azure AD DS 功能變數名稱      | *-aaddsDomainName*        | 受控網域的 FQDN，以先前關於如何選擇樹系名稱的指引為基礎。 |

    `New-AzureAaddsForest`如果這些資源尚未存在，腳本可以建立 Azure 虛擬網路和 AZURE AD DS 子網。 當指定時，腳本可以選擇性地建立工作負載子網：

    | Name                              | 腳本參數                  | 說明 |
    |:----------------------------------|:----------------------------------|:------------|
    | 虛擬網路名稱              | *-aaddsVnetName*                  | 受控網域的虛擬網路名稱。|
    | 位址空間                     | *-aaddsVnetCIDRAddressSpace*      | 以 CIDR 標記法表示的虛擬網路位址範圍（如果建立虛擬網路）。|
    | Azure AD DS 子網名稱           | *-aaddsSubnetName*                | 裝載受控網域之*aaddsVnetName*虛擬網路的子網名稱。 請勿將您自己的 Vm 和工作負載部署到此子網。 |
    | Azure AD DS 位址範圍         | *-aaddsSubnetCIDRAddressRange*    | AAD DS 實例的子網位址範圍（採用 CIDR 標記法），例如*192.168.1.0/24*。 位址範圍必須包含在虛擬網路的位址範圍內，而且與其他子網不同。 |
    | 工作負載子網名稱（選擇性）   | *-workloadSubnetName*             | 在*aaddsVnetName*虛擬網路中，為您自己的應用程式工作負載建立的選擇性子網名稱。 Vm 和應用程式，也會改為連線至對等互連 Azure 虛擬網路。 |
    | 工作負載位址範圍（選擇性） | *-workloadSubnetCIDRAddressRange* | 應用程式工作負載的選擇性子網位址範圍（CIDR 標記法），例如*192.168.2.0/24*。 位址範圍必須包含在虛擬網路的位址範圍內，而且與其他子網不同。|

1. 現在，使用腳本建立受控網域資源樹系 `New-AzureAaaddsForest` 。 下列範例會建立名為*addscontoso.com*的樹系，並建立工作負載子網。 提供您自己的參數名稱和 IP 位址範圍，或現有的虛擬網路。

    ```azure-powershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    建立受控網域資源樹系和支援資源需要相當長的時間。 允許腳本完成。 繼續進行下一節，以設定 Azure AD 資源樹系在背景中布建的內部部署網路連線。

## <a name="configure-and-validate-network-settings"></a>設定及驗證網路設定

當受控網域繼續部署時，請設定並驗證與內部部署資料中心之間的混合式網路連線能力。 您也需要管理 VM 以搭配受控網域使用，以進行定期維護。 某些混合式連線可能已存在於您的環境中，或者您可能需要與小組中的其他人合作來設定連線。

開始之前，請確定您瞭解[網路考慮和建議](tutorial-create-forest-trust.md#networking-considerations)。

1. 使用 Azure VPN 或 Azure ExpressRoute 連線，建立與內部部署網路到 Azure 的混合式連線。 混合式網路設定已超出本檔的範圍，而且可能已存在於您的環境中。 如需特定案例的詳細資訊，請參閱下列文章：

    * [Azure 站對站 VPN](/vpn-gateway/vpn-gateway-about-vpngateways)。
    * [Azure ExpressRoute 總覽](/vpn-gateway/vpn-gateway-about-vpngateways)。

    > [!IMPORTANT]
    > 如果您直接對受控網域的虛擬網路建立連線，請使用個別的閘道子網。 請勿在受控網域的子網中建立閘道。

1. 若要管理受控網域，請建立管理 VM、將其加入受控網域，並安裝必要的 AD DS 管理工具。

    部署受控網域資源樹系時，請[建立 Windows SERVER VM](https://docs.microsoft.com/azure/active-directory-domain-services/join-windows-vm) ，然後[安裝核心 AD DS 管理工具](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-management-vm)來安裝所需的管理工具。 等到成功部署網域之後，才將管理 VM 加入受控網域，直到下列其中一個步驟。

1. 驗證內部部署網路與 Azure 虛擬網路之間的網路連線能力。

    * 請確認您的內部部署網域控制站可以使用或遠端桌面連線到受控 VM `ping` ，例如。
    * 請確認您的管理 VM 可以使用公用程式（例如），再次連線到您的內部部署網域控制站 `ping` 。

1. 在 [Azure 入口網站中，搜尋並選取 [ **Azure AD Domain Services**]。 選擇您的受控網域（例如*aaddscontoso.com* ），並等候狀態報表為 [執行中 **]。**

    執行時，請[更新 Azure 虛擬網路的 DNS 設定](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)，然後[啟用 Azure AD DS 的使用者帳戶](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，以完成受控網域資源樹系的設定。

1. 記下 [總覽] 頁面上顯示的 DNS 位址。 當您在下一節中設定信任關係的內部部署 Active Directory 端時，您需要這些位址。
1. 重新開機管理 VM 以接收新的 DNS 設定，然後將[VM 加入受控網域](join-windows-vm.md#join-the-vm-to-the-managed-domain)。
1. 管理 VM 加入受控網域之後，請使用遠端桌面再次連線。

    從命令提示字元中，使用 `nslookup` 和受控網域資源樹系名稱來驗證資源樹系的名稱解析。

    ```console
    nslookup aaddscontoso.com
    ```

    此命令應該會傳回資源樹系的兩個 IP 位址。

## <a name="create-the-forest-trust"></a>建立樹系信任

樹系信任有兩個部分-受控網域資源樹系中的單向輸出樹系信任，以及內部部署 AD DS 樹系中的單向輸入樹系信任。 您會手動建立這兩個信任關係的兩端。 當雙方建立時，使用者和資源都可以使用樹系信任成功進行驗證。 受控網域資源樹系最多支援對內部部署樹系 5 1 向外的輸出樹系信任。

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>建立信任關係的受控網域端

使用 `Add-AaddsResourceForestTrust` 腳本建立信任關係的受控網域端。 首先， `Add-AaddsResourceForestTrust` 使用[安裝腳本][Install-Script]Cmdlet 從[PowerShell 資源庫][powershell-gallery]安裝腳本：

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

現在提供下列資訊給腳本：

| Name                               | 腳本參數     | 說明 |
|:-----------------------------------|:---------------------|:------------|
| Azure AD DS 功能變數名稱            | *-ManagedDomainFqdn* | 受控網域的 FQDN，例如*aaddscontoso.com* |
| 內部部署 AD DS 功能變數名稱      | *-TrustFqdn*         | 受信任樹系的 FQDN，例如*onprem.contoso.com* |
| 信任易記名稱                | *-TrustFriendlyName* | 信任關係的易記名稱。 |
| 內部部署 AD DS DNS IP 位址 | *-TrustDnsIPs*       | 列出受信任網域的 DNS 伺服器 IPv4 地址清單（以逗號分隔）。 |
| 信任密碼                     | *-TrustPassword*     | 信任關係的複雜密碼。 建立內部部署 AD DS 中的單向輸入信任時，也會輸入此密碼。 |
| 認證                        | *-認證*       | 用來向 Azure 進行驗證的認證。 使用者必須位於*AAD DC 系統管理員群組*中。 如果未提供，腳本會提示您進行驗證。 |

下列範例會建立名為*myAzureADDSTrust*的信任關係至*onprem.contoso.com*。 使用您自己的參數名稱和密碼：。

```azure-powershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> 請記住您的信任密碼。 當您建立信任的內部部署端時，您必須使用相同的密碼。

## <a name="configure-dns-in-the-on-premises-domain"></a>設定內部部署網域中的 DNS

若要從內部部署環境正確地解析受控網域，建議您將轉寄站新增至現有的 DNS 伺服器。 如果您尚未將內部部署環境設定為可與受控網域通訊，請從內部部署 AD DS 網域的管理工作站完成下列步驟：

1. 選取**開始 | 系統管理工具 | DNS**
1. 以滑鼠右鍵選取 DNS 伺服器，例如 *myAD01*，然後選取 [屬性]****
1. 選擇 [轉寄站]****，然後選擇 [編輯]**** 新增其他轉寄站。
1. 新增受控網域的 IP 位址，例如*10.0.1.4*和*10.0.1.5*。
1. 在本機命令提示字元中，使用受控網域資源樹系功能變數名稱的**nslookup**來驗證名稱解析。 例如， `Nslookup aaddscontoso.com` 應該會傳回受控網域資源樹系的兩個 IP 位址。

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>在內部部署網域中建立輸入樹系信任

內部部署 AD DS 網域需要受控網域的連入樹系信任。 此信任必須以手動方式在內部部署 AD DS 網域中建立，無法從 Azure 入口網站建立。

若要在內部部署 AD DS 網域設定輸入信任，請從內部部署 AD DS 網域的管理工作站完成下列步驟：

1. 選取 **開始 | 系統管理工具 | Active Directory 網域及信任**
1. 以滑鼠右鍵選取網域，例如 *onprem.contoso.com*，然後選取 [屬性]****
1. 選擇 [信任]**** 索引標籤，然後選擇 [新增信任]****
1. 輸入受控網域的名稱，例如*aaddscontoso.com*，然後選取 **[下一步]**
1. 選取選項以建立**樹系信任**，然後建立**單向: 連入** 信任。
1. 選擇為**僅此網域**建立信任。 在下一個步驟中，您會在 Azure 入口網站中建立受控網域的信任。
1. 選擇使用**全樹系驗證**，然後輸入並確認信任密碼。 在下一節中，也需要在 Azure 入口網站中輸入相同的密碼。
1. 使用預設選項逐步執行接下來的幾個視窗，然後選擇 [否，不要確認外寄信任] 的選項。 因為您委派的系統管理員帳戶對受控網域資源樹系沒有必要的許可權，所以無法驗證信任關係。 這是設計的行為。
1. 選取 [完成]。

## <a name="validate-resource-authentication"></a>驗證資源驗證

下列常見案例可讓您驗證樹系信任是否正確地驗證使用者和資源的存取權：

* [Azure AD DS 資源樹系中的內部部署使用者驗證](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [使用內部部署使用者存取 Azure AD DS 資源樹系中的資源](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [啟用檔案及印表機共用](#enable-file-and-printer-sharing)
    * [建立安全性群組並新增成員](#create-a-security-group-and-add-members)
    * [建立跨樹系存取的檔案共用](#create-a-file-share-for-cross-forest-access)
    * [驗證資源的跨樹系驗證](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Azure AD DS 資源樹系中的內部部署使用者驗證

您應該將 Windows Server 虛擬機器加入受控網域資源網域。 使用這部虛擬機器來測試您的內部部署使用者是否可以在虛擬機器上進行驗證。

1. 使用遠端桌面和您的受控網域系統管理員認證，連接到已加入受控網域資源樹系的 Windows Server VM。 如果您收到網路層級驗證（NLA）錯誤，請檢查您所使用的使用者帳戶不是網域使用者帳戶。

    > [!TIP]
    > 若要安全地連接到已加入 Azure AD Domain Services 的 Vm，您可以在支援的 Azure 區域中使用[azure 防禦主機服務](https://docs.microsoft.com/azure/bastion/bastion-overview)。

1. 開啟命令提示字元，然後使用 `whoami` 命令來顯示目前已驗證使用者的辨別名稱：

    ```console
    whoami /fqdn
    ```

1. 使用 `runas` 命令，以來自內部部署網域的使用者身分進行驗證。 在下列命令中，以來自受信任內部部署網域之使用者的 UPN 取代 `userUpn@trusteddomain.com`。 此命令會提示您輸入使用者密碼：

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. 如果驗證成功，就會開啟新的命令提示字元。 新命令提示字元的標題包含 `running as userUpn@trusteddomain.com`。
1. 在新的命令提示字元中使用 `whoami /fqdn`，以從內部部署 Active Directory 檢視已驗證使用者的辨別名稱。

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>使用內部部署使用者存取 Azure AD DS 資源樹系中的資源

使用已加入受控網域資源樹系的 Windows Server VM，您可以測試案例，當使用者從內部部署網域中的電腦向內部部署網域的使用者進行驗證時，可以存取資源樹系中主控的資源。 下列範例會示範如何建立和測試各種常見的案例。

#### <a name="enable-file-and-printer-sharing"></a>啟用檔案及印表機共用

1. 使用遠端桌面和您的受控網域系統管理員認證，連接到已加入受控網域資源樹系的 Windows Server VM。 如果您收到網路層級驗證（NLA）錯誤，請檢查您所使用的使用者帳戶不是網域使用者帳戶。

    > [!TIP]
    > 若要安全地連接到已加入 Azure AD Domain Services 的 Vm，您可以在支援的 Azure 區域中使用[azure 防禦主機服務](https://docs.microsoft.com/azure/bastion/bastion-overview)。

1. 開啟 **Windows 設定**，然後搜尋並選取 [網路和共用中心]。
1. 選擇 [變更進階共用] 設定選項。
1. 在 [網域設定檔]下，選取 [開啟檔案及印表機共用] 然後選取 [儲存變更]。
1. 關閉**網路和共用中心**。

#### <a name="create-a-security-group-and-add-members"></a>建立安全性群組並新增成員

1. 開啟 [Active Directory 使用者及電腦]。
1. 在網域名稱上按一下滑鼠右鍵，選擇 [新增]，然後選取 [組織單位]。
1. 在 [名稱] 方塊中，輸入 LocalObjects，然後選取 [確定]。
1. 選取並以滑鼠右鍵按一下瀏覽窗格中的 **LocalObjects**。 選取 [新增]，然後選取 [群組]。
1. 在 [群組名稱] 方塊中，輸入 FileServerAccess。 在 [群組範圍] 中，選取 [網域本機]，然後選擇 [確定]。
1. 在內容窗格中，按兩下 **FileServerAccess**。 選取 [成員]，選擇 [新增]，然後選取 [位置]。
1. 從**位置**檢視中選取您的內部部署 Active Directory，然後選擇 [確定]。
1. 在 [輸入要選取的物件名稱] 方塊中輸入「網域使用者」。 選取 [檢查名稱]並提供內部部署 Active Directory 的認證，然後選取 [確定]。

    > [!NOTE]
    > 您必須提供認證，因為信任關係只有單向。 這表示來自受控網域的使用者無法存取資源，或搜尋受信任（內部部署）網域中的使用者或群組。

1. 來自內部部署 Active Directory 的**網域使用者**群組應該是 **FileServerAccess** 群組的成員。 選取 [確定] 儲存群組並關閉視窗。

#### <a name="create-a-file-share-for-cross-forest-access"></a>建立跨樹系存取的檔案共用

1. 在已加入受控網域資源樹系的 Windows Server VM 上，建立資料夾並提供名稱，例如*CrossForestShare*。
1. 以滑鼠右鍵按一下資料夾，然後選擇 [屬性]。
1. 選取 [安全性] 索引標籤，接著選取 [編輯]。
1. 在 [CrossForestShare 權限] 對話方塊中，選取 [新增]。
1. 在 **輸入物件名稱以選取**中輸入 FileServerAccess，然後選取 [確定]。
1. 從 [群組或使用者名稱] 清單中選取 FileServerAccess。 在 [FileServerAccess 權限] 清單中，針對 [修改] 和 [寫入] 權限選擇 [允許]，然後選取 [確定]。
1. 選取 [共用] 索引標籤，然後選擇 [進階共用...]
1. 選擇 [共用此資料夾]，然後在 [共用名稱] 中，為檔案共用輸入易記的名稱，例如 CrossForestShare。
1. 選取 [權限]。 在 [每個人的權限] 清單中，針對 [變更] 權限選擇 [允許]。
1. 選取 [確定] 兩次，然後選取 [關閉]。

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>驗證資源的跨樹系驗證

1. 使用內部部署 Active Directory 的使用者帳戶，登入已加入內部部署 Active Directory 的 Windows 電腦。
1. 使用 **Windows 檔案總管**連線到您使用完整主機名稱和共用 (例如 `\\fs1.aaddscontoso.com\CrossforestShare`) 所建立的共用。
1. 若要驗證寫入權限，請在資料夾中按一下滑鼠右鍵，選擇 [新增]，然後選取 [文字文件]。 使用預設名稱**新增文字檔**。

    如果已正確設定寫入權限，則會建立新的文字檔。 接下來的步驟將會適當地開啟、編輯及刪除檔案。
1. 若要驗證讀取權限，請開啟**新增文字文件**。
1. 若要驗證修改權限，請將文字新增至檔案，然後關閉**記事本**。 當系統提示您儲存變更時，請選擇 [儲存]。
1. 若要驗證刪除權限，請以滑鼠右鍵選取 [新增文字文件]，然後選擇 [刪除]。 選擇 [是] 確認檔案刪除。

## <a name="update-or-remove-outbound-forest-trust"></a>更新或移除連出樹系信任

如果您需要從受控網域更新現有的單向輸出樹系，您可以使用 `Get-AaddsResourceForestTrusts` 和 `Set-AaddsResourceForestTrust` 腳本。 這些腳本有助於您想要更新樹系信任的易記名稱或信任密碼的情況。 若要從受控網域移除單向輸出信任，您可以使用 `Remove-AaddsResourceForestTrust` 腳本。 您必須手動移除相關聯內部部署 AD DS 樹系中的單向輸入樹系信任。

### <a name="update-a-forest-trust"></a>更新樹系信任

在正常操作中，受管理的網域樹系和內部部署樹系會協調其本身之間的定期密碼更新程式。 這是一般 AD DS 信任關係安全性程式的一部分。 您不需要手動輪替信任密碼，除非信任關係發生問題，而您想要手動重設為已知的密碼。 如需詳細資訊，請參閱[受信任的網域物件密碼變更](concepts-forest-trust.md#tdo-password-changes)。

下列範例步驟會示範如何在您需要手動重設輸出信任密碼時，更新現有的信任關係：

1. `Get-AaddsResourceForestTrusts` `Set-AaddsResourceForestTrust` 使用[安裝腳本][Install-Script]Cmdlet，從[PowerShell 資源庫][powershell-gallery]安裝和腳本：

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. 您必須先使用腳本來取得信任資源，才能更新現有的信任 `Get-AaddsResourceForestTrusts` 。 在下列範例中，現有的信任會指派給名為*existingTrust*的物件。 指定您自己的受控網域樹系名稱，以及要更新的內部部署樹系名稱：

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. 若要更新現有的信任密碼，請使用 `Set-AaddsResourceForestTrust` 腳本。 指定上一個步驟中的現有信任物件，然後指定新的信任關係密碼。 PowerShell 不會強制執行密碼複雜性，因此請務必為您的環境產生及使用安全的密碼。

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>刪除樹系信任

如果您不再需要從受控網域到內部部署 AD DS 樹系的單向輸出樹系信任，您可以將它移除。 移除信任之前，請確定沒有任何應用程式或服務需要對內部部署 AD DS 樹系進行驗證。 您也必須手動移除內部部署 AD DS 樹系中的單向輸入信任。

1. `Remove-AaddsResourceForestTrust`使用[安裝腳本][Install-Script]Cmdlet 從[PowerShell 資源庫][powershell-gallery]安裝腳本：

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. 現在請使用腳本移除樹系信任 `Remove-AaddsResourceForestTrust` 。 在下列範例中，會移除名為*aaddscontoso.com*的受控網域樹系與內部部署樹系*onprem.contoso.com*之間名為*myAzureADDSTrust*的信任。 指定您自己的受控網域樹系名稱，以及要移除的內部部署樹系名稱：

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

若要從內部部署 AD DS 樹系移除單向輸入信任，請連線到具有內部部署 AD DS 樹系存取權的管理電腦，然後完成下列步驟：

1. 選取 **開始 | 系統管理工具 | Active Directory 網域及信任**
1. 以滑鼠右鍵選取網域，例如 *onprem.contoso.com*，然後選取 [屬性]****
1. 選擇 [**信任**] 索引標籤，然後從您的受控網域樹系中選取現有的連入信任。
1. 選取 [**移除**]，然後確認您想要移除連入信任。

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何：

> [!div class="checklist"]
> * 使用 Azure PowerShell 建立受控網域資源樹系
> * 使用 Azure PowerShell 在受控網域中建立單向輸出樹系信任
> * 在內部部署 AD DS 環境中設定 DNS 以支援受控網域連線能力
> * 在內部部署 AD DS 環境中建立單向的輸入樹系信任
> * 測試和驗證信任關係以進行驗證和資源存取

如需有關 Azure AD DS 中樹系類型的詳細概念資訊，請參閱[什麼是資源樹系？][concepts-forest]和[樹系信任如何在 Azure AD DS 中運作？][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/