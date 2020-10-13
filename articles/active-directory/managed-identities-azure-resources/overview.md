---
title: 適用於 Azure 資源的受控識別
description: 適用於 Azure 資源的受控識別概觀。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 10/06/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 728ca38cc3ef3bf989a75d757c69f7ca1993d82d
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803106"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>什麼是適用於 Azure 資源的受控識別？

開發人員常見的挑戰是管理秘密和認證，以保護不同服務之間的通訊。 在 Azure 上，受控識別可讓開發人員不需要管理認證，方法是在 Azure AD 中提供 Azure 資源的身分識別，並將其用來取得 Azure Active Directory (Azure AD) 權杖。 這也有助於存取 [Azure Key Vault](../../key-vault/general/overview.md)，開發人員可在其中以安全的方式儲存認證。 適用於 Azure 資源的受控識別會在 Azure AD 中為 Azure 服務提供自動的受控識別來解決此問題。

受控識別可以做什麼？

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

以下是使用受控識別的一些優點：

- 您不需要管理認證。 您甚至無法存取認證。
- 您可以使用受控識別，對支援 Azure AD 驗證的任何 Azure 服務 (包括 Key Vault) 進行驗證。
- 不需任何額外成本，即可使用受控識別。

> [!NOTE]
> 先前稱為「受控服務識別」(MSI) 的服務，其新名稱為「Azure 資源適用受控識別」。

## <a name="managed-identity-types"></a>受控識別類型

受控身分識別有兩種：

- **系統指派的** 某些 Azure 服務可讓您直接在服務執行個體上啟用受控識別。 啟用系統指派的受控識別時，會在繫結至該服務執行個體生命週期的 Azure AD 中建立身分識別。 因此，當刪除資源時，Azure 會自動為您刪除身分識別。 根據設計，只有該 Azure 資源可以使用此身分識別，自 Azure AD 要求權杖。
- **使用者指派的** 您也可以建立受控識別做為獨立的 Azure 資源。 您可以[建立使用者指派的受控識別](how-to-manage-ua-identity-portal.md)，並將其指派給 Azure 服務的一個或多個執行個體。 若是使用者指派的受控識別，則會分開管理身分識別與使用其的資源。 </br></br>

  > [!VIDEO https://www.youtube.com/embed/OzqpxeD3fG0]

下表顯示這兩種受控識別類型之間的差異。

|  屬性    | 系統指派的受控識別 | 使用者指派的受控識別 |
|------|----------------------------------|--------------------------------|
| 建立 |  建立為 Azure 資源的一部分 (例如 Azure 虛擬機器或 Azure App Service) | 建立為獨立的 Azure 資源 |
| 生命週期 | 與用來建立受控識別的 Azure 資源共用生命週期。 <br/> 當父代資源刪除時，受控識別也會一併刪除。 | 獨立的生命週期。 <br/> 必須明確刪除。 |
| 由所有 Azure 資源共用 | 無法共用。 <br/> 它只能與單一 Azure 資源相關聯。 | 可以共用 <br/> 使用者指派的同一個受控識別可與多個 Azure 資源相關聯。 |
| 一般使用案例 | 包含在單一 Azure 資源內的工作負載 <br/> 您需要獨立身分識別的工作負載。 <br/> 例如，在單一虛擬機器上執行的應用程式 | 在多個資源上執行、且可共用單一身分識別的工作負載。 <br/> 在佈建流程中需要預先授權以保護資源的工作負載。 <br/> 資源回收頻率高、但權限應保持一致的工作負載。 <br/> 例如，有多個虛擬機器需要存取相同資源的工作負載 |

>[!IMPORTANT]
>無論身分識別的類型為何，受控識別都是特殊類型的服務主體，可能僅適用於 Azure 資源。 刪除受控識別後，對應的服務主體也會自動移除。

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>如何使用適用於 Azure 資源的受控識別？

![開發人員如何使用受控識別從其程式碼存取資源，而不需要管理驗證資訊的一些範例](media/overview/azure-managed-identities-examples.png)

## <a name="what-azure-services-support-the-feature"></a>哪些 Azure 服務支援此功能？<a name="which-azure-services-support-managed-identity"></a>

適用於 Azure 資源的受控識別，可用來向支援 Azure AD 驗證的服務進行驗證。 有關哪些 Azure 服務支援適用於 Azure 資源的受控識別功能，請參閱[這些服務支援適用於 Azure 資源的受控識別](./services-support-managed-identities.md)。

## <a name="next-steps"></a>後續步驟

* [使用 Windows VM 系統指派的受控識別來存取 Resource Manager](tutorial-windows-vm-access-arm.md)
* [使用 Linux VM 系統指派的受控識別來存取 Resource Manager](tutorial-linux-vm-access-arm.md)
* [如何使用 App Service 和 Azure Functions 的受控身分識別](../../app-service/overview-managed-identity.md)
* [如何搭配 Azure 容器執行個體使用受控識別](../../container-instances/container-instances-managed-identity.md)
* [實作 Microsoft Azure 資源的受控識別](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing)。