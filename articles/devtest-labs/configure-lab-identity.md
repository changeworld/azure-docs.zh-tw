---
title: 在 Azure DevTest Labs 中設定實驗室身分識別
description: 瞭解如何在 Azure DevTest 中設定實驗室身分識別。
ms.topic: article
ms.date: 08/20/2020
ms.openlocfilehash: a652eb5751f9b723911a1c1baaaaf9860febc5b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88719623"
---
# <a name="configure-a-lab-identity"></a>設定實驗室身分識別

建置雲端應用程式常見的難題是如何管理程式碼中的認證，以向雲端服務進行驗證。 保護好這些認證是相當重要的工作。 在理想情況下，這些認證永不會出現在開發人員工作站且簽入原始程式碼控制。 Azure Key Vault 提供安全地儲存認證、秘密和其他金鑰的方法，但您的程式碼必須向 Key Vault 驗證才能取得它們。 

適用于 Azure 資源的受控識別功能 Azure Active Directory (Azure AD) 解決此問題。 這項功能會在 Azure AD 中將自動受控識別提供給 Azure 服務。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要您程式碼中的任何認證。 深入瞭解 [Azure 上的受控](../active-directory/managed-identities-azure-resources/overview.md)識別。 

受控身分識別有兩種： 

## <a name="system-assigned-managed-identity"></a>系統指派的受控識別  

 **系統指派的受控識別**   會直接在 Azure 服務實例上啟用。 啟用此身分識別時，Azure 會在執行個體的訂用帳戶所信任的 Azure AD 租用戶中，建立執行個體的身分識別。 建立身分識別後，就會將認證佈建到執行個體。 系統指派的身分識別生命週期，會直接繫結至已啟用該身分識別的 Azure 服務執行個體。 如果執行個體已刪除，則 Azure 會自動清除 Azure AD 中的認證和身分識別。 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>使用實驗室系統指派身分識別的案例  

每個 DevTest Labs 都是使用系統指派的身分識別所建立，在實驗室的存留期內仍有效。 系統指派的身分識別用於下列用途：  

- 用來啟動多 VM 和/或平臺即服務環境的所有 [Azure Resource Manager](devtest-lab-create-environment-from-arm.md) 型部署，將會使用實驗室的系統指派身分識別來執行。  
- 透過實驗室系統指派的身分識別，支援使用客戶管理金鑰的實驗室磁片的磁片加密。 藉由提供實驗室身分識別的明確存取權來存取磁片加密集，實驗室可以代表您加密所有虛擬機器磁片。 深入瞭解如何使用客戶管理的金鑰來啟用實驗室磁片的 [磁片加密](encrypt-disks-customer-managed-keys.md) 。  

### <a name="configure-identity"></a>設定身分識別

本節示範如何設定實驗室的身分識別原則。

> [!NOTE]
> 針對在8/10/2020 之前建立的實驗室，系統指派的身分識別將設定為 Off。 作為實驗室擁有者，您可以將它開啟，以防您想要針對上一節所列的目的使用實驗室。  
>
> 針對在8/10/2020 之後建立的新實驗室，實驗室的系統指派身分識別預設為開啟，實驗室擁有者將無法在實驗室生命週期關閉此功能。  

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋 **DevTest Labs**。
1. 從實驗室清單中，選取您要的實驗室。
1. 選取**Configuration 和原則**身分  ->  **識別 (預覽) **。 

> [!div class="mx-imgBorder"]
> ![設定身分識別](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>使用者指派的受控識別  

使用者指派的受控識別會建立為獨立的 Azure 資源。 透過建立程序，Azure 會在所使用訂用帳戶信任的 Azure AD 租用戶中建立身分識別。 建立身分識別之後，即可將它指派給一個或多個 Azure 服務執行個體。 使用者指派的身分識別與獲指派此身分識別的 Azure 服務執行個體，兩者的生命週期分開管理。 

DevTest Labs 支援虛擬機器和 Azure Resource Manager 架構環境的使用者指派身分識別。  如需詳細資訊，請參閱下列主題：

- [新增使用者指派的身分識別，以部署實驗室 Azure Resource Manager 環境](use-managed-identities-environments.md)
- [新增使用者指派的身分識別來部署實驗室虛擬機器](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>接下來的步驟

檢查 [設定成本管理](devtest-lab-configure-cost-management.md)