---
title: 使用範本將 Windows VM 加入 Azure AD DS |Microsoft Docs
description: 瞭解如何使用 Azure Resource Manager 範本將新的或現有的 Windows Server VM 加入 Azure Active Directory Domain Services 受控網域。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e7245e8e468ea051ee095d97cc250ad303aa80a5
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619431"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>使用 Resource Manager 範本將 Windows Server 虛擬機器加入 Azure Active Directory Domain Services 受控網域

若要將 Azure 虛擬機器的部署和設定自動化 (Vm) ，您可以使用 Resource Manager 範本。 這些範本可讓您每次建立一致的部署。 延伸模組也可以包含在範本中，以便在部署過程中自動設定 VM。 有一個實用的延伸模組會將 Vm 加入網域，而該網域可搭配 Azure Active Directory Domain Services (Azure AD DS) 受控網域使用。

本文說明如何使用 Resource Manager 範本，建立 Windows Server VM 並將其加入 Azure AD DS 受控網域。 您也會瞭解如何將現有的 Windows Server VM 加入 Azure AD DS 網域。

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源和權限：

* 有效的 Azure 訂用帳戶。
    * 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 與您的訂用帳戶相關聯的 Azure Active Directory 租用戶，可與內部部署目錄或僅限雲端的目錄同步。
    * 如果需要，請[建立 Azure Active Directory 租用戶][create-azure-ad-tenant]或[將 Azure 訂用帳戶與您的帳戶建立關聯][associate-azure-ad-tenant]。
* 已在您的 Azure AD 租用戶中啟用並設定 Azure Active Directory Domain Services 受控網域。
    * 如有需要，第一個教學課程會引導您[建立並設定 Azure Active Directory Domain Services 受控網域][create-azure-ad-ds-instance]。
* 屬於受控網域一部分的使用者帳戶。

## <a name="azure-resource-manager-template-overview"></a>Azure Resource Manager 範本總覽

Resource Manager 範本可讓您在程式碼中定義 Azure 基礎結構。 必要的資源、網路連線或 Vm 的設定都可以在範本中定義。 這些範本會每次建立一致且可重現的部署，並可在您進行變更時進行版本設定。 如需詳細資訊，請參閱 [Azure Resource Manager 範本總覽][template-overview]。

每個資源都是在使用 JavaScript 物件標記法 (JSON) 的範本中定義。 下列 JSON 範例會使用 *Microsoft. Compute/virtualMachines/extensions* 資源類型來安裝 Active Directory 的網域加入延伸模組。 使用您在部署時指定的參數。 部署延伸模組時，VM 會加入指定的受控網域。

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

即使您不在相同的範本中建立 VM，也可以部署此 VM 擴充功能。 本文中的範例顯示下列兩種方法：

* [建立 Windows Server VM 並加入受控網域](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [將現有 Windows Server VM 加入受控網域](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>建立 Windows Server VM 並加入受控網域

如果您需要 Windows Server VM，您可以使用 Resource Manager 範本來建立及設定它。 部署 VM 後，就會安裝延伸模組，以將 VM 加入受控網域。 如果您已經有想要加入受控網域的 VM，請跳到將 [現有的 Windows SERVER VM 加入受控網域](#join-an-existing-windows-server-vm-to-a-managed-domain)。

若要建立 Windows Server VM，然後將它加入受控網域，請完成下列步驟：

1. 流覽至 [快速入門範本](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)。 選取要 **部署到 Azure** 的選項。
1. 在 [ **自訂部署** ] 頁面上，輸入下列資訊，以建立 WINDOWS Server VM 並將其加入至受控網域：

    | 設定                   | 值 |
    |---------------------------|-------|
    | 訂用帳戶              | 挑選已啟用 Azure AD Domain Services 的相同 Azure 訂用帳戶。 |
    | 資源群組            | 選擇 VM 的資源群組。 |
    | Location                  | 選取 VM 的位置。 |
    | 現有的 VNET 名稱        | 要連接 VM 的現有虛擬網路的名稱，例如 *myVnet*。 |
    | 現有的子網名稱      | 現有虛擬網路子網的名稱，例如 *工作負載*。 |
    | DNS 標籤首碼          | 輸入要用於 VM 的 DNS 名稱，例如 *myvm*。 |
    | VM 大小                   | 指定 VM 大小，例如 *Standard_DS2_v2*。 |
    | 要加入的網域            | 受控網域 DNS 名稱，例如 *aaddscontoso.com*。 |
    | 網域使用者名稱           | 受控網域中應用來將 VM 加入受控網域的使用者帳戶，例如 `contosoadmin@aaddscontoso.com` 。 此帳戶必須是受控網域的一部分。 |
    | 網域密碼           | 在先前設定中指定之使用者帳戶的密碼。 |
    | 選用的 OU 路徑          | 要在其中新增 VM 的自訂 OU。 如果您未指定此參數的值，則會將 VM 新增至預設的 *AAD DC 電腦* OU。 |
    | VM 系統管理員使用者名稱         | 指定要在 VM 上建立的本機系統管理員帳戶。 |
    | VM 管理員密碼         | 指定 VM 的本機系統管理員密碼。 建立強式本機系統管理員密碼，以防止密碼暴力密碼破解攻擊。 |

1. 檢查條款及條件，然後核取 [ **我同意上述條款及條件**] 方塊。 準備好時，請選取 [ **購買** ] 以建立 VM 並將其加入受控網域。

> [!WARNING]
> **處理密碼時多加注意。**
> 範本參數檔案會要求屬於受控網域一部分之使用者帳戶的密碼。 請勿以手動方式將值輸入此檔案，並讓它可在檔案共用或其他共用位置上存取。

部署需要幾分鐘的時間才能順利完成。 完成時，會建立 Windows VM 並加入受控網域。 您可以使用網域帳戶來管理或登入 VM。

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>將現有 Windows Server VM 加入受控網域

如果您有想要加入受控網域的現有 VM 或 Vm 群組，您可以使用 Resource Manager 範本，只部署 VM 擴充功能。

若要將現有的 Windows Server VM 加入受控網域，請完成下列步驟：

1. 流覽至 [快速入門範本](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)。 選取要 **部署到 Azure** 的選項。
1. 在 [ **自訂部署** ] 頁面上，輸入下列資訊以將 VM 加入受控網域：

    | 設定                   | 值 |
    |---------------------------|-------|
    | 訂用帳戶              | 挑選已啟用 Azure AD Domain Services 的相同 Azure 訂用帳戶。 |
    | 資源群組            | 選擇現有 VM 的資源群組。 |
    | Location                  | 選取現有 VM 的位置。 |
    | VM 清單                   | 輸入現有 VM (s 的逗號分隔清單，) 加入至受控網域，例如 *myVM1、myVM2*。 |
    | 加入網域使用者名稱     | 受控網域中應用來將 VM 加入受控網域的使用者帳戶，例如 `contosoadmin@aaddscontoso.com` 。 此帳戶必須是受控網域的一部分。 |
    | 加入網域使用者密碼 | 在先前設定中指定之使用者帳戶的密碼。 |
    | 選用的 OU 路徑          | 要在其中新增 VM 的自訂 OU。 如果您未指定此參數的值，則會將 VM 新增至預設的 *AAD DC 電腦* OU。 |

1. 檢查條款及條件，然後核取 [ **我同意上述條款及條件**] 方塊。 準備好時，請選取 [ **購買** ] 以將 VM 加入受控網域。

> [!WARNING]
> **處理密碼時多加注意。**
> 範本參數檔案會要求屬於受控網域一部分之使用者帳戶的密碼。 請勿以手動方式將值輸入此檔案，並讓它可在檔案共用或其他共用位置上存取。

部署需要幾分鐘的時間才能順利完成。 完成時，指定的 Windows Vm 會加入受控網域，並且可使用網域帳戶進行管理或登入。

## <a name="next-steps"></a>後續步驟

在本文中，您已使用 Azure 入口網站來設定和部署使用範本的資源。 您也可以使用 [Azure PowerShell][deploy-powershell] 或 [Azure CLI][deploy-cli]來部署具有 Resource Manager 範本的資源。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
