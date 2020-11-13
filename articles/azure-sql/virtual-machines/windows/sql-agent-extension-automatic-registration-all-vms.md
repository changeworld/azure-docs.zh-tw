---
title: 使用 SQL IaaS 代理程式擴充功能自動註冊
description: 瞭解如何啟用自動註冊功能，以使用 Azure 入口網站的 SQL IaaS 代理程式擴充功能，自動註冊所有過去和未來的 SQL Server Vm。
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.openlocfilehash: a8eb112b0895d1c7a927621e6fb9e5160038692d
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557550"
---
# <a name="automatic-registration-with-sql-iaas-agent-extension"></a>使用 SQL IaaS 代理程式擴充功能自動註冊
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure 入口網站中啟用自動註冊功能，可在 Azure 虛擬機器上自動註冊 Azure 虛擬機器上的所有目前和未來的 SQL Server， (Vm 以輕量模式中的 [SQL IaaS 代理程式擴充](sql-server-iaas-agent-extension-automate-management.md) 功能進行) 。 

本文將指導您啟用自動註冊功能。 或者，您可以 [註冊單一 VM](sql-agent-extension-manually-register-single-vm.md)，或使用 SQL IaaS 代理程式擴充功能 [大量註冊您的 vm](sql-agent-extension-manually-register-vms-bulk.md) 。 

## <a name="overview"></a>概觀

使用 [SQL IaaS 代理程式延伸](sql-server-iaas-agent-extension-automate-management.md) 模組註冊您的 SQL Server VM，以將一組完整的功能發揮到最大。 

啟用自動註冊時，會每日執行一次作業，以偵測是否在訂用帳戶中所有未註冊的 Vm 上安裝 SQL Server。 這是藉由將 SQL IaaS 代理程式延伸模組二進位檔複製到 VM，然後執行可檢查 SQL Server 登入區的一次性公用程式來完成。 如果偵測到 SQL Server hive，虛擬機器就會以輕量模式註冊擴充功能。 如果登錄中沒有任何 SQL Server hive 存在，則會移除二進位檔。

針對訂用帳戶啟用自動註冊之後，已安裝 SQL Server 的所有目前和未來 Vm 都會以輕量模式向 SQL IaaS 代理程式延伸模組註冊。 您仍然需要以 [手動方式升級為完整的管理模式](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) ，以充分利用完整的功能集。 

> [!IMPORTANT]
> SQL IaaS 代理程式擴充功能會收集資料，以在 Azure 虛擬機器中使用 SQL Server 時，為客戶提供選擇性的權益。 Microsoft 不會在未經客戶同意的情況下，使用此資料進行授權審核。 如需詳細資訊，請參閱 [SQL Server 隱私權補充](/sql/sql-server/sql-server-privacy#non-personal-data) 資訊。

## <a name="prerequisites"></a>先決條件

若要向擴充功能註冊 SQL Server VM，您需要： 

- [Azure 訂](https://azure.microsoft.com/free/)用帳戶和至少[參與者角色](../../../role-based-access-control/built-in-roles.md#all)許可權。
- Azure 資源模型 [Windows Server 2008 R2 (或更新版本) 虛擬機器](../../../virtual-machines/windows/quick-create-portal.md) ，並將 [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) 部署至公用或 Azure Government 雲端。 不支援 Windows Server 2008。 


## <a name="enable"></a>啟用

若要在 Azure 入口網站中啟用 SQL Server Vm 的自動註冊，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至 [ [**SQL 虛擬機器**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) ] 資源頁面。 
1. 選取 [ **自動 SQL SERVER VM 註冊** ] 以開啟 [ **自動註冊** ] 頁面。 

   :::image type="content" source="media/sql-agent-extension-automatic-registration-all-vms/automatic-registration.png" alt-text="選取 [自動 SQL Server VM 註冊] 以開啟 [自動註冊] 頁面":::

1. 從下拉式清單中選擇您的訂用帳戶。 
1. 請仔細閱讀條款，如果您同意，請選取 [ **我接受** ]。 
1. 選取 [ **註冊** ] 以啟用此功能，並使用 SQL IaaS 代理程式擴充功能自動註冊所有目前和未來的 SQL Server vm。 這不會重新開機任何 Vm 上的 SQL Server 服務。 

## <a name="disable"></a>停用

使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps) 停用自動註冊功能。 停用自動註冊功能時，新增至訂用帳戶 SQL Server Vm 必須以手動方式向 SQL IaaS 代理程式延伸模組註冊。 這不會取消註冊現有的 SQL Server 已註冊的 Vm。



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 停用自動註冊，請執行下列命令： 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 停用自動註冊，請執行下列命令： 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>針對多個訂用帳戶啟用

您可以使用 PowerShell 來啟用多個 Azure 訂用帳戶的自動註冊功能。 

若要這樣做，請依照下列步驟執行：

1. 將 [此腳本](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) 儲存至檔案 `.ps1` ，例如 `EnableBySubscription.ps1` 。 
1. 使用系統管理命令提示字元或 PowerShell 視窗，流覽至您儲存腳本的位置。 
1. 連接到 Azure (`az login`) 。
1. 執行腳本，以參數的形式傳入 Subscriptionid，例如   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   例如： 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

失敗的註冊錯誤會儲存在與 `RegistrationErrors.csv` 您儲存和執行腳本的相同目錄中 `.ps1` 。 

## <a name="next-steps"></a>後續步驟

將您的管理性模式升級為 [full](sql-agent-extension-manually-register-single-vm.md#upgrade-to-full) ，以利用 SQL IaaS 代理程式擴充功能提供給您的完整功能集。 
