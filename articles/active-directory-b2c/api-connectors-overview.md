---
title: 關於 Azure AD B2C 中的 API 連接器
description: 使用 Azure Active Directory (Azure AD) API 連接器來自訂和擴充您的註冊使用者流程，方法是使用 web Api。
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 09/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 195101a432d16c2236ea2d164416e75df33b12e4
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828353"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>使用 API 連接器來自訂和擴充註冊使用者流程

## <a name="overview"></a>概觀 
如果您是開發人員或 IT 系統管理員，您可以使用 API 連接器來整合註冊使用者流程與 web Api，以自訂註冊體驗。 使用 API 連接器，您可以：

- **執行身分識別驗證**。 使用身分識別驗證服務，將額外的安全性層級新增至帳戶建立決策。
- **驗證使用者輸入資料**。 驗證使用者資料格式不正確或無效。 例如，您可以針對外部資料存放區或允許值清單中的現有資料，驗證使用者提供的資料。 如果無效，您可以要求使用者提供有效的資料，或封鎖使用者繼續註冊流程。
- **與自訂核准工作流程整合**。 連接至自訂核准系統，以管理和限制帳戶建立。
- **覆寫使用者屬性**。 重新格式化或指派值給從使用者收集的屬性。 例如，如果使用者輸入全部小寫或全部大寫的名字，您可以設定名稱格式為只有第一個字母大寫。 
- **執行自訂商務邏輯**。 您可以在雲端系統中觸發下游事件，以傳送推播通知、更新公司資料庫、管理許可權、審核資料庫，以及執行其他自訂動作。

API 連接器提供呼叫 API 所需資訊的 Azure Active Directory，包括端點 URL 和驗證。 設定 API 連接器之後，您可以針對使用者流程中的特定步驟啟用它。 當使用者在註冊流程中達到該步驟時，會叫用 API 連接器，並具體化為 API 的 HTTP POST 要求，將使用者資訊 ( 「宣告」 ) 作為 JSON 主體中的機碼值組。 API 回應可能會影響使用者流程的執行。 例如，API 回應可能會封鎖使用者的註冊，要求使用者重新輸入資訊，或覆寫並附加使用者屬性。

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>您可以在使用者流程中啟用 API 連接器

使用者流程中有兩個位置可讓您啟用 API 連接器：

- 使用身分識別提供者登入之後
- 建立使用者之前

> [!IMPORTANT]
> 在這兩種情況下，會在使用者 **註冊**期間叫用 API 連接器，而不是登入。

### <a name="after-signing-in-with-an-identity-provider"></a>使用身分識別提供者登入之後

在使用者向身分識別 (提供者（例如 Google、Facebook、& Azure AD) ）進行驗證之後，會立即叫用註冊程式中這個步驟的 API 連接器。 此步驟會在 [ ***屬性集合] 頁面***的前面，也就是向使用者呈現的表單，以收集使用者屬性。 如果使用者正在使用本機帳戶註冊，就不會叫用這個步驟。 以下是您可能會在此步驟啟用的 API 連接器案例範例：

- 使用使用者提供的電子郵件或同盟身分識別來查閱現有系統中的宣告。 從現有的系統傳回這些宣告、預先填入屬性集合頁面，並讓它們可在權杖中傳回。
- 根據社交身分識別來執行允許或封鎖清單。

### <a name="before-creating-the-user"></a>建立使用者之前

註冊程式中這個步驟的 API 連接器會在屬性集合頁面（如果包含的話）之後叫用。 在建立使用者帳戶之前，一律會叫用此步驟。 以下是您在註冊期間可能會啟用的案例範例：

- 驗證使用者輸入資料，並要求使用者重新提交資料。
- 根據使用者輸入的資料來封鎖使用者註冊。
- 執行身分識別驗證。
- 查詢外部系統，以取得使用者的現有資料，以在應用程式權杖中傳回該資料，或將它儲存在 Azure AD 中。


## <a name="next-steps"></a>下一步
- 瞭解如何 [將 API 連接器新增至使用者流程](add-api-connector.md)
<!-- - Learn how to [add a custom approval system to self-service sign-up](add-approvals.md) -->