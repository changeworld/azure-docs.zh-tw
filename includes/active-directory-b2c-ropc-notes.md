---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "78186980"
---
## <a name="ropc-flow-notes"></a>ROPC 流程注意事項
Azure Active Directory B2C (Azure AD B2C) 支援下列選項：

- **原生用戶端**：程式碼在使用者端的裝置上執行時，使用者會在驗證期間與之互動。 裝置可以是在 Android 和 iOS 等原生作業系統中執行的行動應用程式。
- **公用用戶端流程**：只會以 API 呼叫傳送應用程式所收集的使用者認證。 應用程式的認證不會傳送。
- **新增宣告**：可以變更識別碼權杖內容以新增宣告。

不支援下列流程：

- **伺服器對伺服器**：身分識別保護系統必須要有從呼叫端 (原生用戶端) 收集到的可靠 IP 位址，以用於互動的過程中。 伺服器端的 API 呼叫只會使用伺服器的 IP 位址。 如果超出失敗驗證的動態閾值，身分識別保護系統可能會將重複的 IP 位址識別為攻擊者。
- **機密用戶端流程**：會驗證應用程式用戶端識別碼，但不會驗證應用程式密碼。

使用 ROPC 流程時，請考慮下列事項：

- 當需要使用者互動的驗證流程中斷時，ROPC 無法正常運作。 例如，當密碼過期或需要變更，或在登入期間需要收集更多資訊時 (例如，使用者同意)，即需要使用 Multi-Factor Authentication。
- ROPC 僅支援本機帳戶。 使用者無法使用同盟身分識別提供者登入，例如 Microsoft、Google+、Twitter、AD-FS 或 Facebook。
- 不適用工作階段管理，包括讓我保持登入 (KMSI)。
