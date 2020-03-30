---
title: 為 Azure 門戶的使用者設置目錄級不活動超時 |微軟文檔
description: 管理員可以在會話登出之前強制執行最大閒置時間。非活動超時策略在目錄級別設置。
services: azure-portal
keywords: 設置、超時
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096603"
---
# <a name="set-directory-level-inactivity-timeout"></a>設置目錄級非活動超時

不活動超時設置有助於保護資源免受使用者忘記保護其工作站的未授權訪問。 當使用者處於空閒狀態一段時間時，其 Azure 門戶會話將自動登出。[全域管理員角色的](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)管理員可以在會話登出之前強制執行最大閒置時間。不活動超時設置適用于目錄級別。 有關目錄的詳細資訊，請參閱[活動目錄域服務概述](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

## <a name="configure-the-inactive-timeout-setting"></a>配置非活動超時設置

如果您是全域管理員，並且希望為 Azure 門戶的所有使用者強制實施空閒超時設置，請按照以下步驟操作：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 從全域頁面標題中選擇 **"設置**"。
3. 選擇連結文本**配置目錄級別超時**。

    ![顯示突出顯示連結文本的門戶設置的螢幕截圖](./media/admin-timeout/settings.png)

4. 新的頁面隨即開啟。 在 **"配置目錄級別不活動超時"** 頁上，選擇**啟用目錄級空閒超時，以便 Azure 門戶**打開該設置。
5. 接下來，輸入使用者在自動登出會話之前可以空閒的最長時間**的時間和****分鐘**。
6. 選取 [**套用**]。

    ![顯示頁面以設置目錄級非活動超時的螢幕截圖](./media/admin-timeout/configure.png)

要確認未活動超時策略設置正確，請從全域頁眉中選擇 **"通知**"。 驗證是否列出了成功通知。

  ![顯示目錄級非活動超時成功通知訊息的螢幕截圖](./media/admin-timeout/confirmation.png)

該設置對新會話生效。 它不會立即應用於已登錄的任何使用者。

> [!NOTE]
> 如果全域管理員配置了目錄級超時設置，使用者可以覆蓋策略並設置自己的非活動登出持續時間。 但是，使用者必須選擇的時間間隔小於全域管理員在目錄級別設置的時間間隔。
>

## <a name="next-steps"></a>後續步驟

* [設定您的 Azure 入口網站喜好設定](set-preferences.md)
* [匯出或刪除使用者設定](azure-portal-export-delete-settings.md)
* [開啟高對比或變更佈景主題](azure-portal-change-theme-high-contrast.md)
