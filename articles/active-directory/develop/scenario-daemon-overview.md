---
title: 建構調用 Web API 的守護程式應用 - 微軟身份平臺 |蔚藍
description: 瞭解如何建構 Web API 的守護程式應用程式
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: df06c4c55941f4424d6b90d2846af17bf055b2e4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885458"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>專案:呼叫 Web API 的守護程式應用程式

瞭解編譯呼叫 Web API 的守護程式應用程式所需的一切。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>概觀

您的應用程式可以獲取權杖來代表自身(而不是代表使用者)調用Web API。 此方案對守護程序應用程式很有用。 它使用標準的 OAuth 2.0[客戶端認證](v2-oauth2-client-creds-grant-flow.md)。

![精靈應用程式](./media/scenario-daemon-app/daemon-app.svg)

下面是守護程式應用的一些用例範例:

- 用於預先設定或管理使用者或在目錄中執行批次的 Web 應用程式
- 執行批次應用程式(如 Windows 上的 Windows 服務或 Linux 上的守護行程)或背景執行的作業系統服務
- 需要操作目錄(而不是特定使用者的 Web API)

另一種常見情況是,非守護程式應用程式使用客戶端憑據:即使它們代表使用者操作,出於技術原因,它們也需要訪問 Web API 或自己的標識下的資源。 例如,訪問 Azure 密鑰保管庫或快取的 Azure SQL 資料庫中的秘密。

為其自身識別取得權杖的應用程式:

- 是機密客戶端應用程式。 這些應用由於獨立於使用者訪問資源,需要證明其身份。 它們也是相當敏感的應用程式。 它們需要由 Azure 活動目錄 (Azure AD) 租戶管理員批准。
- 已向 Azure AD 註冊了機密(應用程式密碼或證書)。 此機密在調用 Azure AD 以獲取權杖期間傳入。

## <a name="specifics"></a>詳細資料

> [!IMPORTANT]
>
> - 使用者無法與守護程序應用程式進行互動。 守護程序應用程式需要其自己的標識。 這種類型的應用程式通過使用其應用程式識別並向 Azure AD 顯示其應用程式 ID、認證或憑證)和應用程式 ID URI 來請求存取權杖。 身份驗證成功後,守護進程將從 Microsoft 標識平台終結點接收訪問令牌(和刷新權杖)。 然後使用此令牌調用 Web API(並根據需要刷新)。
> - 由於使用者無法與守護程序應用程式互動,因此無法增量同意。 所有必需的 API 許可權都需要在應用程式註冊時配置。 應用程式的代碼只是請求靜態定義的許可權。 這也意味著守護程序應用程式不支援增量同意。

對於開發人員來說,此方案的端到端體驗具有以下幾個方面:

- 守護程序應用程式只能在 Azure AD 租戶中工作。 構建試圖操作 Microsoft 個人帳戶的守護程序應用程式是沒有意義的。 如果您是業務線 (LOB) 應用開發人員,則可以在租戶中創建守護程式應用。 如果您是 ISV,則可能需要創建多租戶守護程序應用程式。 每個租戶管理員都需要提供同意。
- 在[應用程式註冊](./scenario-daemon-app-registration.md)期間,不需要回復URI。 您需要與 Azure AD 共用機密或證書或簽名斷言。 您還需要請求應用程式許可權並授予管理員同意才能使用這些應用許可權。
- [應用程式配置](./scenario-daemon-app-configuration.md)需要提供在應用程式註冊期間與 Azure AD 共用的用戶端認證。
- 用於使用客戶端認證串流獲取權杖[的範圍](scenario-daemon-acquire-token.md#scopes-to-request)必須是靜態作用域。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [守護程式應用程式 - 應用程式註冊](./scenario-daemon-app-registration.md)
