---
title: 管理 Azure 入口網站設定和喜好設定 |Microsoft Docs
description: 您可以變更 Azure 入口網站預設值，以符合您自己的喜好設定。 設定包括非使用中會話超時、預設視圖、功能表模式、對比、主題、通知，以及語言和地區格式
services: azure-portal
keywords: 設定、超時、語言、地區
author: mgblythe
ms.author: mblythe
ms.date: 08/05/2020
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 20ed84a87486f1095a90e012368b1f56d6426c8e
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88205724"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>管理 Azure 入口網站設定和喜好設定

您可以變更 Azure 入口網站的預設值，以符合您自己的喜好設定。 大部分的設定都可以從全域頁首的 [ **設定** ] 功能表取得。

![顯示醒目提示設定之全域頁首圖示的螢幕擷取畫面](./media/set-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>選擇您的預設訂用帳戶

當您登入 Azure 入口網站時，您可以變更預設開啟的訂用帳戶。 如果您有使用的主要訂用帳戶，但偶爾使用其他訂閱，這會很有説明。 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="依訂用帳戶篩選資源清單。":::

1. 選取全域頁首中的 [目錄和訂用帳戶篩選] 圖示。

1. 當您啟動入口網站時，請選取您想要作為預設訂用帳戶的訂用帳戶。 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="當您啟動入口網站時，請選取您想要作為預設訂用帳戶的訂用帳戶。"::: 


## <a name="choose-your-default-view"></a>選擇您的預設 view 

當您登入 Azure 入口網站時，您可以變更預設開啟的頁面。

![顯示已醒目提示預設視圖之 Azure 入口網站設定的螢幕擷取畫面](./media/set-preferences/default-view.png)

- 無法自訂**首頁**。  它會顯示熱門 Azure 服務的快捷方式，並列出您最近使用過的資源。 我們也會提供您資源的實用連結，例如 Microsoft Learn 和 Azure 藍圖。

- 您可以自訂儀表板，以建立專為您設計的工作區。 例如，您可以建立以專案、工作或角色為焦點的儀表板。 如果您選取 [ **儀表板**]，您的預設視圖會移至最近使用的儀表板。 如需詳細資訊，請參閱[在 Azure 入口網站中建立和共用儀表板](azure-portal-dashboards.md)。

## <a name="choose-a-portal-menu-mode"></a>選擇入口網站功能表模式

入口網站功能表的預設模式控制入口網站功能表在頁面上佔用的空間量。

![顯示已醒目提示主題之 Azure 入口網站設定的螢幕擷取畫面](./media/set-preferences/menu-mode.png)

- 當入口網站功能表處於 **飛出** 視窗模式時，在您需要的時候才會隱藏。 選取功能表圖示以開啟或關閉功能表。

- 如果您選擇 [停駐 **模式]** 作為入口網站功能表，則一律會顯示。 您可以摺疊功能表來提供更多的工作空間。

## <a name="choose-a-theme-or-enable-high-contrast"></a>選擇主題或啟用高對比

您選擇的主題會影響出現在 Azure 入口網站中的背景和字型色彩。 您可以從四個預設色彩主題的其中一個進行選取。 選取每個縮圖，以尋找最適合您的主題。

或者，您可以選擇其中一個高對比主題。 高對比主題讓具有視覺障礙的人更容易閱讀 Azure 入口網站;它們會覆寫所有其他主題選取專案。

![顯示已醒目提示主題之 Azure 入口網站設定的螢幕擷取畫面](./media/set-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>啟用或停用快顯通知

通知是與您目前會話相關的系統訊息。 例如，當您剛建立的資源可供使用時，他們會提供您目前的信用額度餘額等資訊，或確認您的最後一個動作。 開啟快顯通知時，訊息會短暫顯示在畫面的右上角。 

若要啟用或停用快顯通知，請選取或清除 [啟用快顯 **通知**]。

![顯示已醒目提示快顯通知的 Azure 入口網站設定的螢幕擷取畫面](./media/set-preferences/popup-notifications.png)

若要讀取目前會話期間收到的所有通知，請從全域標頭選取 [ **通知** ]。

![顯示已醒目提示通知之 Azure 入口網站全域標頭的螢幕擷取畫面](./media/set-preferences/read-notifications.png)

如果您想要從先前的會話讀取通知，請在活動記錄檔中尋找事件。 如需詳細資訊，請參閱 [查看活動記錄](../azure-monitor/platform/activity-log.md#view-the-activity-log)。 

## <a name="change-the-inactivity-timeout-setting"></a>變更無活動超時設定

如果您忘記保護您的工作站，無活動超時設定有助於保護資源免于未經授權的存取。 在閒置一段時間之後，您就會自動登出 Azure 入口網站會話。 身為個人，您可以變更自己的 timeout 設定。 如果您是系統管理員，您可以在目錄中為您所有的使用者在目錄層級設定它。

### <a name="change-your-individual-timeout-setting-user"></a> (使用者) 變更個別的超時設定

選取 [在非使用中 **時將我登出**] 底下的下拉式選單。 選擇您的 Azure 入口網站會話已登出的持續時間（如果您處於閒置狀態）。

![顯示已醒目提示非使用中超時設定的入口網站設定的螢幕擷取畫面](./media/set-preferences/inactive-signout-user.png)

變更會自動儲存。 如果您是閒置的，您的 Azure 入口網站會話會在您設定的持續時間之後登出。

如果您的系統管理員已啟用非使用狀態的超時原則，您仍然可以設定自己的時間，只要它小於目錄層級的設定即可。 選取 **[覆寫目錄無活動超時原則**]，然後設定時間間隔。

![顯示已醒目提示 [目錄非使用時間超時] 原則設定之入口網站設定的螢幕擷取畫面](./media/set-preferences/inactive-signout-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a>變更 (系統管理員) 的目錄超時設定

[全域管理員角色](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)中的系統管理員可以在會話登出之前，強制執行最大閒置時間。無活動超時設定適用于目錄層級。 此設定會對新的會話生效。 它不會立即套用到已登入的任何使用者。 如需目錄的詳細資訊，請參閱 [Active Directory Domain Services 總覽](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

如果您是全域系統管理員，而且想要針對 Azure 入口網站的所有使用者強制執行閒置的超時設定，請遵循下列步驟：

1. 選取連結文字 [ **設定目錄層級超時**]。

    ![螢幕擷取畫面，其中顯示已醒目提示連結文字的入口網站設定](./media/set-preferences/settings-admin.png)

1. 在 [ **設定目錄層級的非使用時間超時** ] 頁面上，選取 **[啟用 Azure 入口網站的目錄層級閒置時間** ] 以開啟設定。

1. 接下來，輸入使用者在其會話自動登出之前，可以閒置的時間和**分鐘****數**。

1. 選取 [套用]。

    ![顯示頁面以設定目錄層級非啟用時間的螢幕擷取畫面](./media/set-preferences/configure.png)

若要確認是否已正確設定 [無活動超時] 原則，請從全域頁面標頭選取 [ **通知** ]。 確認已列出成功通知。

![螢幕擷取畫面，顯示目錄層級非啟用時間的成功通知訊息](./media/set-preferences/confirmation.png)

## <a name="restore-default-settings"></a>還原預設設定

如果您已對 Azure 入口網站設定進行變更，並想要捨棄它們，請選取 [ **還原預設設定**]。 您對入口網站設定所做的任何變更都將遺失。 此選項不會影響儀表板的自訂專案。

![螢幕擷取畫面，顯示預設設定的還原](./media/set-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>匯出使用者設定

您的自訂設定的相關資訊會儲存在 Azure 中。 您可以匯出下列使用者資料：

* Azure 入口網站中的私用儀表板
* 使用者設定，例如我的最愛訂閱或目錄，以及最後一個登入的目錄
* 主題和其他自訂入口網站設定

如果您打算刪除您的設定，最好先匯出和檢查。 重建儀表板或重做設定可能相當耗時。

若要匯出您的入口網站設定，請選取 [ **匯出所有設定**]。

![顯示匯出設定的螢幕擷取畫面](./media/set-preferences/useful-links-export-settings.png)

匯出設定會建立 *json* 檔案，其中包含您的使用者設定，例如您的色彩主題、我的最愛和私人儀表板。 由於使用者設定的動態性質和資料損毀的風險，因此您無法從 *json* 檔案匯入設定。

## <a name="delete-user-settings-and-dashboards"></a>刪除使用者設定和儀表板

您的自訂設定的相關資訊會儲存在 Azure 中。 您可以刪除下列使用者資料：

* Azure 入口網站中的私用儀表板
* 使用者設定，例如我的最愛訂閱或目錄，以及最後一個登入的目錄
* 主題和其他自訂入口網站設定

在刪除您的設定之前，最好先匯出並加以檢查。 重建儀表板或重做自訂設定可能相當耗時。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

若要刪除您的入口網站設定，請選取 [ **刪除所有設定和私人儀表板**]。

![顯示刪除設定的螢幕擷取畫面](./media/set-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>變更語言和地區設定

有兩個設定可控制 Azure 入口網站中文字的顯示方式： 
- [ **語言** ] 設定可控制您在 Azure 入口網站中看到的文字語言。 

- **地區格式** 控制日期、時間、數位和貨幣的顯示方式。

若要變更 Azure 入口網站中使用的語言，請使用下拉式清單從可用的語言清單中選取。

地區格式選取專案會變更為只顯示您選取之語言的地區選項。 若要變更該自動選取專案，請使用下拉式選選擇您想要的地區格式。

例如，如果您選取 [英文] 做為語言，然後選取 [美國] 作為地區格式，貨幣會以美元顯示。 如果您選取 [英文] 做為語言，然後選取 [歐洲] 做為地區格式，貨幣會以歐元顯示。

選取 **[** 套用] 以更新您的語言和地區設定。

   ![顯示語言和地區設定格式設定的螢幕擷取畫面](./media/set-preferences/language.png)

>[!NOTE]
>這些語言和地區設定只會影響 Azure 入口網站。 在新索引標籤或視窗中開啟的檔連結，會使用您瀏覽器的語言設定來決定要顯示的語言。
>

## <a name="next-steps"></a>後續步驟

- [Azure 入口網站的鍵盤快速鍵](azure-portal-keyboard-shortcuts.md)
- [支援的瀏覽器和裝置](azure-portal-supported-browsers-devices.md)
- [新增、移除和重新排列我的最愛](azure-portal-add-remove-sort-favorites.md)
- [建立和共用自訂儀表板](azure-portal-dashboards.md)
- [Azure 入口網站操作說明影片系列](azure-portal-video-series.md)
