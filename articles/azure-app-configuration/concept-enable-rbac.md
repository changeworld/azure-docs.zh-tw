---
title: 使用 Azure 活動目錄授權訪問 Azure 應用配置
description: 啟用 RBAC 授權訪問 Azure 應用配置實例
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472618"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>使用 Azure 活動目錄授權訪問 Azure 應用配置
Azure 應用配置支援使用 Azure 活動目錄 （Azure AD） 授權對應用配置實例的請求。  Azure AD 允許您使用基於角色的存取控制 （RBAC） 向安全主體授予許可權。  安全主體可以是使用者或[應用程式服務主體](../active-directory/develop/app-objects-and-service-principals.md)。  要瞭解有關角色和角色指派的更多內容，請參閱[瞭解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>總覽
必須授權安全主體（使用者或應用程式）請求訪問應用配置資源。  使用 Azure AD，訪問資源是一個兩步過程。
1. 安全主體的標識經過身份驗證，並返回 OAuth 2.0 權杖。  請求權杖的資源名稱是`https://login.microsoftonline.com/{tenantID}`與服務主體所屬`{tenantID}`的 Azure 活動目錄租戶 ID 匹配的位置。
2. 權杖作為請求的一部分傳遞給應用佈建服務，以授權對指定資源的訪問。

身份驗證步驟要求應用程式請求在運行時包含 OAuth 2.0 訪問權杖。  如果應用程式在 Azure 實體（如 Azure 函數應用、Azure Web 應用或 Azure VM）中運行，則可以使用託管標識訪問資源。  要瞭解如何對託管標識對 Azure 應用配置發出的請求進行身份驗證，請參閱[使用 Azure 活動目錄和 Azure 資源的託管標識對 Azure 應用配置資源進行身份驗證訪問](howto-integrate-azure-managed-service-identity.md)。

授權步驟要求將一個或多個 RBAC 角色指派給安全主體。 Azure 應用配置提供 RBAC 角色，這些角色包含應用配置資源的許可權集。 分配給安全主體的角色確定向主體提供的許可權。 有關 RBAC 角色的詳細資訊，請參閱[Azure 應用配置的內置 RBAC 角色](#built-in-rbac-roles-for-azure-app-configuration)。 

## <a name="assign-rbac-roles-for-access-rights"></a>為存取權限分配 RBAC 角色
Azure Active Directory (Azure AD) 會透過[角色型存取控制 (RBAC)](../role-based-access-control/overview.md)，來授與存取受保護資源的權限。

將 RBAC 角色指派給 Azure AD 安全主體時，Azure 會授予對該安全主體的這些資源的存取權限。 訪問範圍為應用配置資源。 Azure AD 安全主體可以是使用者、應用程式服務主體或[Azure 資源的託管標識](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Azure 應用配置的內置 RBAC 角色
Azure 提供了以下內置 RBAC 角色，用於使用 Azure AD 和 Auth 授權訪問應用配置資料：

- Azure 應用配置資料擁有者：使用此角色授予對應用配置資源的讀/寫存取權限。
- Azure 應用配置資料讀取器：使用此角色授予對應用配置資源的讀取存取許可權。
- 參與者：使用此角色授予管理員對服務的存取權限，而無需授予對應用配置實例中存儲的資料的存取權限。

## <a name="next-steps"></a>後續步驟
詳細瞭解如何使用[託管標識](howto-integrate-azure-managed-service-identity.md)來管理應用佈建服務。