---
title: 使用 Microsoft 圖形管理資源
titleSuffix: Azure AD B2C
description: 通過註冊被授予所需圖形 API 許可權的應用程式，準備使用 Microsoft 圖形管理 Azure AD B2C 資源。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 32117d4bfcf0c0af94eced095b94ab0c1b6f88af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184332"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>使用微軟圖形管理 Azure AD B2C

[Microsoft 圖形][ms-graph]允許您管理 Azure AD B2C 租戶中的許多資源，包括客戶使用者帳戶和自訂策略。 通過編寫調用 Microsoft 圖形[API][ms-graph-api]的腳本或應用程式，可以自動執行租戶管理工作，例如：

* 將現有使用者存儲遷移到 Azure AD B2C 租戶
* 在 Azure DevOps 中使用 Azure 管道部署自訂策略，並管理自訂策略鍵
* 在您自己的頁面上託管使用者註冊，並在後臺在 Azure AD B2C 目錄中創建使用者帳戶
* 自動註冊應用程式
* 獲取稽核記錄

以下各節可説明您準備使用 Microsoft 圖形 API 自動管理 Azure AD B2C 目錄中的資源。

## <a name="microsoft-graph-api-interaction-modes"></a>微軟圖形API交互模式

使用 Microsoft 圖形 API 管理 Azure AD B2C 租戶中的資源時，可以使用兩種通訊模式：

* **互動式**- 適合運行一次的任務，您可以使用 B2C 租戶中的管理員帳戶來執行管理工作。 此模式要求管理員在調用 Microsoft 圖形 API 之前使用其憑據登錄。

* **自動化**- 對於計畫或連續運行的任務，此方法使用您配置的服務帳戶，該服務帳戶具有執行管理工作所需的許可權。 通過註冊應用程式和腳本使用應用程式 *（用戶端） ID*和 OAuth 2.0 用戶端憑據授予進行身份驗證的應用程式，在 Azure AD B2C 中創建"服務帳戶"。 在這種情況下，應用程式充當自身來調用 Microsoft 圖形 API，而不是管理員使用者，如前面描述的互動式方法所示。

通過創建以下各節中所示的應用程式註冊，可以啟用**自動**交互方案。

## <a name="register-management-application"></a>註冊管理應用程式

在腳本和應用程式可以與 Microsoft[圖形 API][ms-graph-api]進行交互以管理 Azure AD B2C 資源之前，需要在 Azure AD B2C 租戶中創建應用程式註冊，該租戶授予所需的 API 許可權。

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>授與 API 存取權限

接下來，授予註冊的應用程式許可權，以便通過調用 Microsoft Graph API 來操作租戶資源。

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>創建用戶端機密

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

現在，您擁有在 Azure AD B2C 租戶中*創建*、*讀取*、*更新*和*刪除*使用者的許可權的應用程式。 繼續下一節添加*密碼更新*許可權。

## <a name="enable-user-delete-and-password-update"></a>啟用使用者刪除和密碼更新

*讀取和寫入目錄資料*許可權**不包括**刪除使用者或更新使用者帳戶密碼的功能。

如果應用程式或腳本需要刪除使用者或更新其密碼，請為應用程式分配*使用者管理員*角色：

1. 登錄到 Azure[門戶](https://portal.azure.com)並使用**目錄 + 訂閱**篩選器切換到 Azure AD B2C 租戶。
1. 搜索並選擇**Azure AD B2C**。
1. 在 **"管理**"下，選擇**角色和管理員**。
1. 選擇 **"使用者管理員"** 角色。
1. 選擇 **"添加分配**"。
1. 在 **"選擇**文本"框中，輸入之前註冊的應用程式的名稱，例如*管理應用1*。 在搜尋結果中顯示應用程式時選擇該應用程式。
1. 選取 [加入]****。 許可權可能需要幾分鐘才能完全傳播。

## <a name="next-steps"></a>後續步驟

現在，您已註冊管理應用程式並授予它所需的許可權，您的應用程式和服務（例如，Azure 管道）可以使用其憑據和許可權與 Microsoft 圖形 API 進行交互。

* [微軟圖形支援的 B2C 操作](microsoft-graph-operations.md)
* [使用微軟圖形管理 Azure AD B2C 使用者帳戶](manage-user-accounts-graph-api.md)
* [使用 Azure AD 報告 API 獲取稽核記錄](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
