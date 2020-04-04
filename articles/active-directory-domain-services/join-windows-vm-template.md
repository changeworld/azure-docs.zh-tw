---
title: 使用樣本將 Windows VM 加入 Azure AD DS |微軟文件
description: 瞭解如何使用 Azure 資源管理器樣本將新的或現有的 Windows 伺服器 VM 加入 Azure 活動目錄域服務託管域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: d2108b4c6b81675e2df6789d412dbd7d36f58a4d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655118"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>使用資源管理員樣本將 Windows 伺服器虛擬機器加入 Azure 活動目錄域服務託管域

要自動部署和配置 Azure 虛擬機器 (VM),可以使用資源管理器範本。 這些範本允許您每次創建一致的部署。 擴展也可以包含在範本中,以自動配置 VM 作為部署的一部分。 一個有用的擴展將 VM 連接到網域,該域可與 Azure 活動目錄域服務 (Azure AD DS) 託管域一起使用。

本文演示如何使用資源管理器範本創建 Windows 伺服器 VM 並將其加入 Azure AD DS 託管域。 您還瞭解如何將現有的 Windows 伺服器 VM 加入 Azure AD DS 網域。

## <a name="prerequisites"></a>Prerequisites

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果沒有 Azure 訂閱,[請建立帳號](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，第一個教學課程會引導您[建立並設定 Azure Active Directory Domain Services 執行個體][create-azure-ad-ds-instance]。
* 屬於 Azure AD DS 託管域的使用者帳戶。

## <a name="azure-resource-manager-template-overview"></a>Azure 資源管理員樣本概述

資源管理員範本允許您在代碼中定義 Azure 基礎結構。 所需的資源、網路連接或 VM 配置都可以在範本中定義。 這些範本每次都創建一致、可重複的部署,並在進行更改時進行版本控制。 有關詳細資訊,請參閱[Azure 資源管理器樣本概述][template-overview]。

每個資源都使用 JAVAScript 物件表示法 (JSON) 在範本中定義。 以下 JSON 範例使用*Microsoft.計算/虛擬機器/擴充*資源類型來安裝活動目錄域聯接擴展。 使用您在部署時指定的參數。 部署延伸時,VM 將連接到指定的 Azure AD DS 託管域。

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

即使您不在同一範本中創建 VM,也可以部署此 VM 擴展。 本文中的範例顯示以下兩種方法:

* [建立 Windows 伺服器 VM 並加入託管網域](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [將現有 Windows Server VM 加入受控網域](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>建立 Windows 伺服器 VM 並加入託管網域

如果需要 Windows 伺服器 VM,可以使用資源管理器範本創建和配置一個 VM。 部署 VM 時,將安裝擴展以將 VM 加入 Azure AD DS 託管域。 如果您已經有 VM,您希望加入 Azure AD DS 託管域,請跳至[將現有的 Windows 伺服器 VM 加入託管域](#join-an-existing-windows-server-vm-to-a-managed-domain)。

要建立 Windows 伺服器 VM,然後將其加入 Azure AD DS 託管域,請完成以下步驟:

1. 瀏覽到[快速入門範本](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)。 選擇要**部署到 Azure**的選項。
1. 在 **「自訂部署」** 頁上,輸入以下資訊以建立 Windows 伺服器 VM 並將其加入到 Azure AD DS 託管域:

    | 設定                   | 值 |
    |---------------------------|-------|
    | 訂用帳戶              | 挑選已啟用 Azure AD Domain Services 的相同 Azure 訂用帳戶。 |
    | 資源群組            | 選擇 VM 的資源組。 |
    | Location                  | 選擇 VM 的位置。 |
    | 現有 VNET 名稱        | 要將 VM 連線到的現有虛擬網路的名稱,如*myVnet*。 |
    | 現有子網名稱      | 現有虛擬網路子網的名稱,如*工作負載*。 |
    | DNS 標籤前置字串          | 輸入用於 VM 的 DNS 名稱,如*myvm*。 |
    | VM 大小                   | 指定 VM 大小,如*Standard_DS2_v2*。 |
    | 要加入的網域            | Azure AD DS 託管域 DNS 名稱,如*aaddscontoso.com*。 |
    | 網域使用者名稱           | 應用於將 VM 加入託管域(`contosoadmin@aaddscontoso.com`如 ) 的 Azure AD DS 託管域中的使用者帳戶。 此帳戶必須是 Azure AD DS 託管域的一部分。 |
    | 網域密碼           | 上一個設置中指定的使用者帳戶的密碼。 |
    | 選擇的的路徑          | 要在其中添加 VM 的自定義 OU。 如果不為此參數指定值,則 VM 將添加到預設*的 AAD DC 計算機*OU 中。 |
    | VM 管理員使用者名稱         | 指定要在 VM 上建立的本地管理員帳戶。 |
    | VM 管理員密碼         | 為 VM 指定本地管理員密碼。 創建強大的本地管理員密碼,以防止密碼暴力攻擊。 |

1. 檢視條款和條件,然後選擇「**我同意上述條款和條件」 選單方**塊 。 準備就緒後,選擇 **「購買」** 以創建 VM 並將其加入 Azure AD DS 託管域。

> [!WARNING]
> **處理密碼時多加注意。**
> 樣本參數檔請求屬於 Azure AD DS 託管域的使用者帳戶的密碼。 不要手動在此檔中輸入值,使其在檔共用或其他共用位置上訪問。

成功完成部署需要幾分鐘時間。 完成後,將創建 Windows VM 並加入 Azure AD DS 託管域。 可以使用域帳戶管理 VM 或登錄 VM。

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>將現有 Windows Server VM 加入受控網域

如果現有 VM 或 VM 組希望加入 Azure AD DS 託管域,則可以使用資源管理器範本來僅部署 VM 擴展。

要將現有的 Windows 伺服器 VM 加入 Azure AD DS 託管域,完成以下步驟:

1. 瀏覽到[快速入門範本](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)。 選擇要**部署到 Azure**的選項。
1. 在 **「自訂部署」** 頁上,輸入以下資訊以將 VM 加入 Azure AD DS 託管域:

    | 設定                   | 值 |
    |---------------------------|-------|
    | 訂用帳戶              | 挑選已啟用 Azure AD Domain Services 的相同 Azure 訂用帳戶。 |
    | 資源群組            | 選擇具有現有 VM 的資源組。 |
    | Location                  | 選擇現有 VM 的位置。 |
    | VM 清單                   | 輸入要加入 Azure AD DS 託管域的現有 VM 的逗號分隔清單,例如*myVM1,myVM2*。 |
    | 網域加入使用者名稱     | 應用於將 VM 加入託管域(`contosoadmin@aaddscontoso.com`如 ) 的 Azure AD DS 託管域中的使用者帳戶。 此帳戶必須是 Azure AD DS 託管域的一部分。 |
    | 網域加入使用者密碼 | 上一個設置中指定的使用者帳戶的密碼。 |
    | 選擇的的路徑          | 要在其中添加 VM 的自定義 OU。 如果不為此參數指定值,則 VM 將添加到預設*的 AAD DC 計算機*OU 中。 |

1. 檢視條款和條件,然後選擇「**我同意上述條款和條件」 選單方**塊 。 準備就緒後,選擇 **「購買」** 以將 VM 加入 Azure AD DS 託管域。

> [!WARNING]
> **處理密碼時多加注意。**
> 樣本參數檔請求屬於 Azure AD DS 託管域的使用者帳戶的密碼。 不要手動在此檔中輸入值,使其在檔共用或其他共用位置上訪問。

成功完成部署需要一些時間。 完成後,指定的 Windows VM 將連接到 Azure AD DS 託管域,並且可以使用網域帳戶進行管理或簽名。

## <a name="next-steps"></a>後續步驟

在本文中,您可以使用 Azure 門戶使用範本配置和部署資源。 您還可以使用[Azure PowerShell][deploy-powershell]或[Azure CLI][deploy-cli]使用資源管理器範本部署資源。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
