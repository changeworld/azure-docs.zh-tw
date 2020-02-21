---
title: 使用 Microsoft Graph 管理資源
titleSuffix: Azure AD B2C
description: 藉由註冊已授與所需圖形 API 許可權的應用程式，來準備使用 Microsoft Graph 管理 Azure AD B2C 資源。
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bcddcf051e73381af35ca40e22c443c7b9ae30a7
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492938"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>使用 Microsoft Graph 管理 Azure AD B2C

[Microsoft Graph][ms-graph]可讓您管理 Azure AD B2C 租使用者內的許多資源，包括客戶使用者帳戶和自訂原則。 藉由撰寫呼叫[MICROSOFT GRAPH API][ms-graph-api]的腳本或應用程式，您可以自動化租使用者管理工作，例如：

* 將現有的使用者存放區遷移至 Azure AD B2C 租使用者
* 在 Azure DevOps 中使用 Azure 管線部署自訂原則，以及管理自訂原則金鑰
* 在您自己的頁面上裝載使用者註冊，並在幕後的 Azure AD B2C 目錄中建立使用者帳戶
* 自動化應用程式註冊
* 取得 audit 記錄

下列各節可協助您準備使用 Microsoft Graph API 來自動化 Azure AD B2C 目錄中的資源管理。

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph API 互動模式

當您使用 Microsoft Graph API 來管理 Azure AD B2C 租使用者中的資源時，可以使用兩種通訊模式：

* **Interactive** -適用于僅執行一次的工作，您可以使用 B2C 租使用者中的系統管理員帳戶來執行管理工作。 此模式需要系統管理員使用其認證登入，才能呼叫 Microsoft Graph API。

* **自動化**-針對已排程或連續執行的工作，這個方法會使用您以執行管理工作所需的許可權來設定的服務帳戶。 您會在 Azure AD B2C 中建立「服務帳戶」，方法是註冊應用程式和腳本使用其*應用程式（用戶端）識別碼*和 OAuth 2.0 用戶端認證授與進行驗證所用的應用程式。 在此情況下，應用程式本身就是呼叫 Microsoft Graph API，而不是系統管理員使用者，就像先前所述的互動式方法一樣。

您可以藉由建立下列各節所示的應用程式註冊，來啟用**自動化**互動案例。

## <a name="register-management-application"></a>註冊管理應用程式

在您的腳本和應用程式可以與[MICROSOFT GRAPH API][ms-graph-api]互動以管理 Azure AD B2C 資源之前，您需要在 Azure AD B2C 租使用者中建立應用程式註冊，以授與所需的 API 許可權。

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>授與 API 存取權限

接下來，授與已註冊的應用程式許可權，以透過呼叫 Microsoft Graph API 來操作租使用者資源。

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>建立用戶端密碼

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

您現在有一個應用程式，有權*建立*、*讀取*、*更新*和*刪除*Azure AD B2C 租使用者中的使用者。 繼續進行下一節，以新增*密碼更新*許可權。

## <a name="enable-user-delete-and-password-update"></a>啟用使用者刪除和密碼更新

*讀取和寫入目錄資料*許可權**不**包含刪除使用者或更新使用者帳戶密碼的能力。

如果您的應用程式或腳本需要刪除使用者或更新其密碼，請將*使用者系統管理員*角色指派給您的應用程式：

1. 登入[Azure 入口網站](https://portal.azure.com)，然後使用 [**目錄 + 訂**用帳戶] 篩選器切換至您的 Azure AD B2C 租使用者。
1. 搜尋並選取 [ **Azure AD B2C**]。
1. 在 [**管理**] 底下，選取 [**角色和系統管理員**]。
1. 選取 [**使用者系統管理員**] 角色。
1. 選取 [**新增指派**]。
1. 在 [**選取**] 文字方塊中，輸入您先前註冊之應用程式的名稱，例如*managementapp1*。 當您的應用程式出現在搜尋結果中時，請加以選取。
1. 選取 [新增]。 可能需要幾分鐘的時間才能完全傳播許可權。

## <a name="next-steps"></a>後續步驟

既然您已註冊您的管理應用程式，並將其授與必要的許可權，您的應用程式和服務（例如 Azure Pipelines）可以使用其認證和許可權來與 Microsoft Graph API 互動。

* [Microsoft Graph 支援 B2C 作業](microsoft-graph-operations.md)
* [使用 Microsoft Graph 管理 Azure AD B2C 的使用者帳戶](manage-user-accounts-graph-api.md)
* [使用 Azure AD 報告 API 取得 audit 記錄](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
