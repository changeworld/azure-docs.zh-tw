---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186980"
---
## <a name="ropc-flow-notes"></a>ROPC 流量注釋
在 Azure 活動目錄 B2C（Azure AD B2C）中，支援以下選項：

- **原生用戶端**：程式碼在使用者端的裝置上執行時，使用者會在驗證期間與之互動。 該設備可以是在本機作業系統（如 Android 和 iOS）中運行的移動應用程式。
- **公用用戶端流程**：只會以 API 呼叫傳送應用程式所收集的使用者認證。 應用程式的認證不會傳送。
- **新增宣告**：可以變更識別碼權杖內容以新增宣告。

不支援下列流程：

- **伺服器對伺服器**：身分識別保護系統必須要有從呼叫端 (原生用戶端) 收集到的可靠 IP 位址，以用於互動的過程中。 伺服器端的 API 呼叫只會使用伺服器的 IP 位址。 如果超出失敗驗證的動態閾值，身分識別保護系統可能會將重複的 IP 位址識別為攻擊者。
- **機密用戶端流程**：會驗證應用程式用戶端識別碼，但不會驗證應用程式密碼。

使用 ROPC 流時，請考慮以下事項：

- 當需要使用者交互的身份驗證流出現任何中斷時，ROPC 不起作用。 例如，當密碼過期或需要更改時，需要多重要素驗證，或者需要在登錄期間收集更多資訊（例如，使用者同意）。
- ROPC 僅支援本地帳戶。 使用者無法與微軟、Google+、Twitter、AD-FS 或 Facebook 等聯合身份供應商登錄。
- 會話管理（包括保留我登錄 （KMSI） 不適用。
