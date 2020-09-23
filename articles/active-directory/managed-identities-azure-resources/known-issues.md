---
title: 受控識別的常見問題集與已知問題 - Azure AD
description: Azure 資源適用受控識別的已知問題。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 08/06/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 0d8c835cdc501061607dc05d0b40ebf95deb36a8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969142"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Azure 資源適用受控識別的常見問題集與已知問題

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>常見問題集 (FAQ)

> [!NOTE]
> 先前稱為「受控服務識別」(MSI) 的服務，其新名稱為「Azure 資源適用受控識別」。

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>如何尋找具有受控識別的資源？

您可以使用下列 Azure CLI 命令，找出具有系統指派受控識別的資源清單： 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="do-managed-identities-have-a-backing-app-object"></a>受控識別是否有支援的應用程式物件？

不可以。 受控識別和 Azure AD App 註冊與目錄中的內容不同。 

應用程式註冊有兩個元件：應用程式物件 + 服務主體物件。 適用于 Azure 資源的受控識別只有其中一個元件：服務主體物件。 

受控識別在目錄中沒有應用程式物件，此物件通常是用來授與 MS graph 的應用程式許可權。 相反地，受控識別的 MS graph 許可權必須直接授與服務主體。  

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Azure 資源適用受控識別是否可與 Azure 雲端服務一起使用？

否，目前沒有計劃讓 Azure 雲端服務支援 Azure 資源適用受控識別。

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>什麼是與受控識別相關聯的認證？ 有效期限和旋轉的頻率為何？

> [!NOTE]
> 受控識別驗證的方式是內部執行的詳細資料，如有變更，恕不另行通知。

受控識別使用以憑證為基礎的驗證。 每個受控識別的認證都有90天的到期日，並會在45天后復原。

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>什麼是 Azure 資源適用受控識別安全性界限？

身分識別的安全性界限，是指身分識別所連結到的資源。 例如，對於 Azure 資源適用受控識別已啟用的虛擬機器而言，其安全性界限就是虛擬機器。 在該虛擬機器上執行的任何程式碼，都能呼叫 Azure 資源適用受控識別與要求權杖。 此體驗類似於其他支援 Azure 資源適用受控識別的資源。

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>如果未在要求中指定身分識別，則 IMDS 的身分識別將預設為何？

- 如果啟用了系統指派的受控識別，且要求中沒有指定任何身分識別，IMDS 將會預設為系統指派的受控識別。
- 如果未啟用系統指派的受控識別，且只有一個使用者指派的受控識別，IMDS 將會預設為該單一使用者指派的受控識別。 
- 如果未啟用系統指派的受控識別，且有多個使用者指派的受控識別，則必須在要求中指定受控識別。

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>如果我將訂用帳戶移到另一個目錄，系統是否會自動重新建立受控識別？

否。 如果您將訂用帳戶移至另一個目錄，則必須手動重新建立訂用帳戶，並再次授與 Azure 角色指派。
- 若為系統指派的受控識別：停用然後重新啟用。 
- 若為使用者指派的受控識別：加以刪除、重新建立，然後重新連結至所需的資源 (例如虛擬機器)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>可以使用受控識別來存取不同目錄/租用戶中的資源嗎？

否。 受控識別目前不支援跨目錄案例。 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>資源上的受控識別需要哪些 Azure RBAC 權限？ 

- 系統指派的受控識別：您需要資源的寫入權限。 例如，針對虛擬機器，您需要 Microsoft.Compute/virtualMachines/write。 此動作包含在資源特定的內建角色中，例如[虛擬機器參與者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)。
- 使用者指派的受控識別：您需要資源的寫入權限。 例如，針對虛擬機器，您需要 Microsoft.Compute/virtualMachines/write。 加上透過受控識別的[受控識別操作員](../../role-based-access-control/built-in-roles.md#managed-identity-operator)角色指派。


## <a name="known-issues"></a>已知問題

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>嘗試使用 Azure 資源適用受控識別擴充功能的結構描述匯出時，「自動化指令碼」會失敗

在虛擬機器上啟用 Azure 資源適用受控識別時，如果嘗試對虛擬機器或其資源群組使用「自動化指令碼」功能時，會出現下列錯誤：

![Azure 資源適用受控識別自動化指令碼匯出錯誤](./media/msi-known-issues/automation-script-export-error.png)

Azure 資源適用受控識別 VM 擴充功能 (預計在 2019 年 1 月淘汰) 目前不支援將其結構描述匯出至資源群組範本的功能。 因此，產生的範本不會顯示可在資源上啟用 Azure 資源適用受控識別的設定參數。 您可以依照[使用範本在 Azure 虛擬機器上設定 Azure 資源適用受控識別](qs-configure-template-windows-vm.md)中的範例，手動新增這些區段。

當結構描述匯出功能變成可供 Azure 資源適用受控識別 VM 擴充功能 (預計在 2019 年 1 月淘汰) 使用時，就會列在[匯出包含虛擬機器擴充功能的資源群組](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)中。

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM 從資源群組或訂用帳戶移走後會無法啟動

如果 VM 在執行期間遭到移動，VM 會在移動時繼續執行。 不過，移動之後，如果 VM 停止並重新啟動，則會無法啟動。 此問題是因為虛擬機器不會更新對 Azure 資源適用受控識別身分識別的參照，並持續指向舊資源群組中的參照所致。

**因應措施** 
 
觸發虛擬機器上的更新，如此一來就可以為 Azure 資源適用受控識別取得正確的值。 您可以變更虛擬機器屬性，以更新 Azure 資源適用受控識別身分識別的參照。 例如，您可以使用下列命令在 VM 上設定新的標記值：

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
此命令會在 VM 上設定值為 1 的新標記 "fixVM"。 
 
設定此屬性後，虛擬機器會透過正確的 Azure 資源適用受控識別資源 URI 進行更新，之後您應該就可以啟動虛擬機器了。 
 
一旦啟動 VM 後，您可以使用下列命令移除標記：

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>在 Azure AD 目錄之間轉移訂用帳戶

當訂用帳戶移動/轉移至另一個目錄時，不會更新受控身分識別。 因此，系統指派或使用者指派的任何現有受控身分識別都會中斷。 

在已移至另一個目錄的訂用帳戶中，受控識別的因應措施：

 - 若為系統指派的受控識別：停用然後重新啟用。 
 - 若為使用者指派的受控識別：加以刪除、重新建立，然後重新連結至所需的資源 (例如虛擬機器)

如需詳細資訊，請參閱[將 Azure 訂用帳戶轉移至不同的 Azure AD 目錄 (預覽)](../../role-based-access-control/transfer-subscription.md)。

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>將使用者指派的受控識別移至不同的資源群組/訂用帳戶

不支援將使用者指派的受控識別移至不同的資源群組。
