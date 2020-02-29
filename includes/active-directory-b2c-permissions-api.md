---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 71a6654acd436c27bd2370646dede81778113860
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186131"
---
#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 選取 [應用程式]  ，然後選取應可存取 API 的 Web 應用程式。 例如，*webapp1*。
1. 選取 [API 存取]  ，然後選取 [新增]  。
1. 在 [選取 API]  下拉式清單中，選取 Web 應用程式應獲得其存取權的 API。 例如，*webapi1*。
1. 在 [選取範圍]  下拉式清單中，選取您先前定義的範圍。 例如 *demo.read* 和 *demo.write*。
1. 選取 [確定]  。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 選取 [應用程式註冊 (預覽)]  ，然後選取應可存取 API 的 Web 應用程式。 例如，*webapp1*。
1. 在 [管理]  之下選取 [API 權限]  。
1. 在 [已設定的權限]  底下，選取 [新增權限]  。
1. 選取 [我的 API]  索引標籤。
1. 選取 Web 應用程式應獲得其存取權的 API。 例如，*webapi1*。
1. 在 [權限]  下，展開 [示範]  ，然後選取先前定義的範圍。 例如 *demo.read* 和 *demo.write*。
1. 選取 [新增權限]  。 依照指示，稍等幾分鐘，然後再繼續進行下一個步驟。
1. 選取 [授與管理員同意 (您的租用戶名稱)]  。
1. 選取您目前登入的系統管理員帳戶，或使用 Azure AD B2C 租用戶中的帳戶 (該租用戶至少已指派「雲端應用程式管理員」  角色) 來登入。
1. 選取 [接受]  。
1. 選取 [重新整理]  ，然後確認 [授與...] 出現在這個範圍的 [狀態]  下方。 權限可能需要幾分鐘的時間來傳播。