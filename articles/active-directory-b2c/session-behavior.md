---
title: 配置會話行為 - Azure 活動目錄 B2C |微軟文檔
description: 在 Azure 活動目錄 B2C 中配置會話行為。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c1d39fdbca9484f47ce0c8537c82247b75b2e3db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186806"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>在 Azure 活動目錄 B2C 中配置會話行為

這項功能可讓您以[每個使用者流程為基礎](user-flow-overview.md)，針對以下項目進行精細控制︰

- 由 Azure AD B2C 管理之 Web 應用程式工作階段的存留期。
- Azure AD B2C 租用戶中跨越多個應用程式和使用者流程的單一登入 (SSO) 行為。

這些設定不適用於密碼重設使用者流程。

Azure AD B2C 支援以 [OpenID Connect 驗證通訊協定](openid-connect.md) 啟用 Web 應用程式的安全登入。 您可以使用下列屬性管理 Web 應用程式工作階段︰

## <a name="session-behavior-properties"></a>會話行為屬性

- **Web 應用程式工作階段存留期 (分鐘)** - 在成功通過驗證時，使用者瀏覽器中所儲存 Azure AD B2C 工作階段 Cookie 的存留期。
    - 預設值 = 1440 分鐘。
    - 最小值 (含) = 15 分鐘。
    - 最大值 (含) = 1440 分鐘。
- **Web 應用程式工作階段逾時** - 如果將此參數設定為 [絕對]****，使用者必須在 [Web 應用程式工作階段存留期 (分鐘)]**** 指定的期間結束後再度驗證。 如果將此參數設定為 **[循環]** \(預設設定)，只要使用者在 Web 應用程式中持續保持作用狀態，他們便能維持登入狀態。
- **單一登入配置**如果您的 B2C 租戶中有多個應用程式和使用者流，則可以使用**單一登入配置**屬性管理跨應用程式的使用者交互。 您可以將屬性配置為以下任一設定︰
    - **Tenant** - 這項設定是預設值。 此設定可允許 B2C 租用戶中的多個應用程式和使用者流程共用同一個使用者工作階段。 例如，一旦使用者登入應用程式，就可以在存取另一個應用程式 Contoso Pharmacy 時順暢地登入。
    - **應用程式** - 這項設定可讓您保留應用程式專用的使用者工作階段，不受其他應用程式所限制。 例如，如果您想讓使用者登入 Contoso Pharmacy (使用相同的認證)，即使使用者已登入 Contoso Shopping (同一個 B2C 租用戶上的另一個應用程式)。
    - **原則** - 這項設定可讓您保留使用者流程專用的使用者工作階段，不論使用該使用者工作階段的應用程式為何。 例如，如果使用者已登入並完成多重要素驗證 (MFA) 步驟，只要與使用者流程繫結的工作階段未過期，使用者就可以取得多個應用程式較高安全性之組件的存取權限。
    - **已停用** - 這項設定可強制使用者在每次原則執行時，都必須完成整個使用者流程。

使用這些屬性可啟用以下使用案例：

- 請設定適當的 Web 應用程式工作階段存留期，以滿足業界的安全性和循規規範。
- 當使用者與 Web 應用程式之高度安全組件的互動達到設定期間後，請強制他們驗證。

## <a name="configure-the-properties"></a>配置屬性

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 通過在頂部功能表中選擇**目錄 + 訂閱**篩選器並選擇包含 Azure AD B2C 租戶的目錄，請確保使用的目錄包含 Azure AD B2C 租戶。
3. 選擇 Azure 入口網站左上角的 [所有服務]****，然後搜尋並選取 [Azure AD B2C]****。
4. 選擇**使用者流（策略）。**
5. 打開以前創建的使用者流。
6. 選取 [內容]****。
7. 根據需要配置**Web 應用會話存留期（分鐘）、Web****應用會話超時**、**單一登入配置**和**在登出請求中要求 ID 權杖**。

    ![Azure 門戶中的會話行為屬性設置](./media/session-behavior/session-behavior.png)

8. 按一下 [儲存]****。
