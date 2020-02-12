---
title: 為 Azure 入口網站的使用者設定目錄層級的非啟用時間 |Microsoft Docs
description: 系統管理員可以在會話登出之前，強制執行最大閒置時間。在目錄層級設定的無活動超時原則。
services: azure-portal
keywords: 設定，超時
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 950580048f0496fd8436901938a5b6768c61bab6
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132082"
---
# <a name="set-directory-level-inactivity-timeout"></a>設定目錄層級的非啟用時間

如果使用者忘記保護其工作站，無活動超時設定有助於保護您的資源免于未經授權的存取。 當使用者閒置一段時間時，系統會自動登出他們的 Azure 入口網站會話。[全域管理員角色](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)中的系統管理員可以在會話登出之前，強制執行最大閒置時間。無活動超時設定適用于目錄層級。 如需目錄的詳細資訊，請參閱[Active Directory Domain Services 總覽](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

## <a name="configure-the-inactive-timeout-setting"></a>設定非使用中的超時設定

如果您是全域系統管理員，而且想要針對 Azure 入口網站的所有使用者強制執行閒置的超時設定，請遵循下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從全域頁面標頭中選取 [**設定**]。
3. 選取連結文字 [**設定目錄層級超時**]。

    ![螢幕擷取畫面，其中顯示已醒目提示連結文字的入口網站設定](./media/admin-timeout/settings.png)

4. 新的頁面隨即開啟。 在 [**設定目錄層級的非使用時間超時**] 頁面上，選取 **[啟用 Azure 入口網站的目錄層級閒置時間**] 以開啟設定。
5. 接下來，輸入使用者在其會話自動登出之前，可以閒置的時間和**分鐘** **數**。
6. 選取 [套用]。

    ![顯示頁面以設定目錄層級非啟用時間的螢幕擷取畫面](./media/admin-timeout/configure.png)

若要確認是否已正確設定 [無活動超時] 原則，請從全域頁面標頭選取 [**通知**]。 確認已列出成功通知。

  ![螢幕擷取畫面，顯示目錄層級非啟用時間的成功通知訊息](./media/admin-timeout/confirmation.png)

此設定會對新的會話生效。 它不會立即套用到已登入的任何使用者。

> [!NOTE]
> 如果系統管理員已設定目錄層級的超時設定，使用者可以覆寫原則並設定自己的非作用中登出持續時間。 不過，使用者必須選擇小於目錄層級設定的時間間隔。
>

## <a name="next-steps"></a>後續步驟

* [設定您的 Azure 入口網站喜好設定](set-preferences.md)
* [匯出或刪除使用者設定](azure-portal-export-delete-settings.md)
* [開啟高對比或變更佈景主題](azure-portal-change-theme-high-contrast.md)
