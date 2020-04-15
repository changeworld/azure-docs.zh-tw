---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: ee63c1c0913baf3700d77e6fba1582fe7027ead7
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875657"
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
1. 選取 [新增權限]  。
1. 選取 [授與管理員同意 (您的租用戶名稱)]  。
1. 若系統提示您選取帳戶，請選取您目前登入的管理員帳戶，或使用 Azure AD B2C 租用戶中的帳戶 (該租用戶至少已指派「雲端應用程式管理員」  角色) 來登入。
1. 選取 [是]  。
1. 選取 [重新整理]  ，然後確認 [授與...] 出現在這個範圍的 [狀態]  下方。
