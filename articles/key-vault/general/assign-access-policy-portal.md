---
title: " (入口網站指派 Azure Key Vault 存取原則) "
description: 如何使用 Azure 入口網站將 Key Vault 存取原則指派給服務主體或應用程式身分識別。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: d16d14ff51cb9a7e71193d93c6755a8e4cb93ed0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89481404"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>使用 Azure 入口網站指派 Key Vault 存取原則

Key Vault 存取原則會決定指定的服務主體（也就是應用程式或使用者群組）是否可以對 Key Vault 的 [秘密](../secrets/index.yml)、 [金鑰](../keys/index.yml)和 [憑證](../certificates/index.yml)執行不同的作業。 您可以使用 Azure 入口網站 (本文) 、 [Azure CLI](assign-access-policy-cli.md)或 [Azure PowerShell](assign-access-policy-powershell.md)，來指派存取原則。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

如需透過 Azure 入口網站在 Azure Active Directory 中建立群組的詳細資訊，請參閱 [建立基本群組和新增成員](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

## <a name="assign-an-access-policy"></a>指派存取原則

1.  在 [Azure 入口網站](https://portal.azure.com)中，流覽至 Key Vault 資源。 

1.  在 [ **設定**] 底下，選取 [ **存取**原則]，然後選取 [ **新增存取原則**]：

    ![選取 [存取原則]，選取 [新增角色指派]](../media/authentication/assign-policy-portal-01.png)

1.  在 [ **憑證許可權**]、[ **金鑰許可權**] 和 [ **秘密許可權**] 下，選取您要的許可權。 您也可以選取包含一般許可權組合的範本：

    ![指定存取原則許可權](../media/authentication/assign-policy-portal-02.png)

1. 在 [ **選取主體**] 下，選擇 [ **無選取** ] 連結以開啟 [ **主體** 選取] 窗格。 在 [搜尋] 欄位中，輸入應用程式或服務主體的名稱，選取適當的結果，然後選擇 [ **選取**]。

    ![選取存取原則的服務主體](../media/authentication/assign-policy-portal-03.png)

    如果您是使用應用程式的受控識別，請搜尋並選取應用程式本身的名稱。  (如需受控識別與服務主體的詳細資訊，請參閱 [Key Vault 驗證-應用程式身分識別與服務主體](authentication.md#app-identity-and-security-principals)。 ) 
 
1.  回到 [ **新增存取原則** ] 窗格中，選取 [ **新增** ] 以儲存存取原則。

    ![新增已指派服務主體的存取原則](../media/authentication/assign-policy-portal-04.png)

1. 回到 [ **存取原則** ] 頁面，確認您的存取原則列在 [ **目前的存取**原則] 底下，然後選取 [ **儲存**]。 存取原則在您儲存之前都不會套用。

    ![儲存存取原則變更](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>後續步驟

- [Azure Key Vault 安全性：身分識別和存取管理](overview-security.md#identity-and-access-management)
- [保護您的金鑰保存庫](secure-your-key-vault.md)。
- [Azure Key Vault 開發人員指南](developers-guide.md)
- [Azure Key Vault 最佳做法](best-practices.md)
