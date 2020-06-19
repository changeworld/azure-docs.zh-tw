---
title: 使用 Azure Active Directory 來授權 Azure 應用程式組態的存取權
description: 啟用 RBAC 以授權您 Azure 應用程式組態執行個體的存取權
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 3ec30aafe63259237a89de6597970b908fb969cf
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773435"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>使用 Azure Active Directory 來授權 Azure 應用程式組態的存取權
Azure 應用程式組態支援使用 Azure Active Directory (Azure AD) 來授權對應用程式組態執行個體的要求。  Azure AD 可讓您使用角色型存取控制 (RBAC) 將權限授與安全性主體。  安全性主體可以是使用者或[應用程式服務主體](../active-directory/develop/app-objects-and-service-principals.md)。  若要深入了解角色和角色指派，請參閱[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概觀
安全性主體 (使用者或應用程式) 為了存取應用程式組態資源所提出的要求必須獲得授權。  使用 Azure AD，對資源的存取是兩個步驟的程序。
1. 安全性主體的身分識別已通過驗證，並傳回 OAuth 2.0 權杖。  要求權杖的資源名稱是 `https://login.microsoftonline.com/{tenantID}`，其中 `{tenantID}` 符合服務主體所屬的 Azure Active Directory 租用戶識別碼。
2. 權杖會當做要求的一部分傳遞給應用程式組態服務，以授權指定資源的存取權。

驗證步驟要求應用程式要求在執行階段包含 OAuth 2.0 存取權杖。  如果應用程式是在 Azure 實體內執行，例如 Azure Functions 應用程式、Azure Web 應用程式或 Azure VM，則可以使用受控識別來存取資源。  若要了解如何驗證受控識別對 Azure 應用程式組態所提出的要求，請參閱[使用適用於 Azure 資源的 Azure Active Directory 和受控識別來驗證 Azure 應用程式組態資源的存取權](howto-integrate-azure-managed-service-identity.md)。

授權步驟需要將一或多個 RBAC 角色指派給安全性主體。 Azure 應用程式組態提供 RBAC 角色，其中包含應用程式組態資源的權限集。 指派給安全性主體的角色會決定提供給主體的權限。 如需 RBAC 角色的詳細資訊，請參閱 [Azure 應用程式組態的內建 RBAC 角色](#built-in-rbac-roles-for-azure-app-configuration)。 

## <a name="assign-rbac-roles-for-access-rights"></a>指派存取權限的 RBAC 角色
Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../role-based-access-control/overview.md)，來授與存取受保護資源的權限。

當 RBAC 角色指派給 Azure AD 安全性主體時，Azure 會為該安全性主體授與這些資源的存取權。 存取範圍限定為應用程式組態資源。 Azure AD 安全性主體可以是使用者或應用程式服務主體，或是 [Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Azure 應用程式組態的內建 RBAC 角色
Azure 提供下列內建 RBAC 角色，以使用 Azure AD 和 OAuth 來授權應用程式組態資料的存取權：

- **應用程式組態資料擁有者**：使用此角色可提供應用程式組態資料的讀取/寫入/刪除存取權。 這不會授與應用程式組態資源的存取權。
- **應用程式組態資料讀者**：使用此角色可提供應用程式組態資料的讀取存取權。 這不會授與應用程式組態資源的存取權。
- **參與者**：使用此角色來管理應用程式組態資源。 雖然可以使用存取金鑰來存取應用程式組態資料，但是此角色並不會使用 Azure AD 授與資料的存取權。
- **讀者**：使用此角色可提供應用程式組態資源的讀取存取權。 這不會授與資源存取金鑰的存取權，也不會授與應用程式組態中所儲存資料的存取權。

## <a name="next-steps"></a>後續步驟
深入了解使用[受控識別](howto-integrate-azure-managed-service-identity.md)管理您的應用程式組態服務。
