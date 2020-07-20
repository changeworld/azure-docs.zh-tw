---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: f8c972bdb9195008c2983d3993e8d9369749b284
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200185"
---
#### <a name="app-registrations"></a>[應用程式註冊](#tab/app-reg-ga/) 

1. 在 [管理]**** 之下選取 [API 權限]****。
1. 在 [已設定的權限]**** 底下，選取 [新增權限]****。
1. 選取 [ **Microsoft api** ] 索引標籤，然後選取 [ **Microsoft Graph**]。
1. 選取 [應用程式權限]  。
1. 展開適當的許可權群組，然後選取要授與管理應用程式之許可權的核取方塊。 例如：
    * **AuditLog**  > **AuditLog**：讀取目錄的 audit 記錄檔。
    * **目錄**  > **目錄. ReadWrite. All**：用於使用者遷移或使用者管理案例。
    * **原則**  > **TrustFramework**：用於持續整合/持續傳遞（CI/CD）案例。 例如，使用 Azure Pipelines 的自訂原則部署。
1. 選取 [新增權限]。 依照指示，稍等幾分鐘，然後再繼續進行下一個步驟。
1. 選取 [授與管理員同意 (您的租用戶名稱)]。
1. 如果您目前未使用全域系統管理員帳戶登入，請使用至少指派*雲端應用程式管理員*角色的 Azure AD B2C 租使用者中的帳戶登入，然後選取 [授與系統**管理員同意（您的租使用者名稱）**]。
1. 選取 **[** 重新整理]，然後確認「已授與 ...」出現在 [**狀態**] 之下。 權限可能需要幾分鐘的時間來傳播。

#### <a name="applications-legacy"></a>[應用程式 (舊版)](#tab/applications-legacy/)

1. 在 [**已註冊的應用程式**總覽] 頁面上，選取 [**設定**]。
1. 在 [ **API 存取**] 底下，選取 [**必要許可權**]。
1. 選取 [Microsoft Graph]  。
1. 在 [**應用程式許可權**] 下，選取要授與管理應用程式之許可權的核取方塊。 例如：
    * **讀取所有 audit 記錄資料**：選取此許可權以讀取目錄的 audit 記錄檔。
    * **讀取和寫入目錄資料**：針對使用者遷移或使用者管理案例選取此許可權。
    * **讀取及寫入您組織的信任架構原則**：選取此許可權以進行持續整合/持續傳遞（CI/CD）案例。 例如，使用 Azure Pipelines 的自訂原則部署。
1. 選取 [儲存]。
1. 選取 [授與權限]，然後選取 [是]。 可能需要幾分鐘的時間才能完全傳播許可權。
