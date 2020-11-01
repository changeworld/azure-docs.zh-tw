---
title: 管理 Azure 入口網站設定和喜好設定 |Microsoft Docs
description: 您可以變更 Azure 入口網站預設設定，以符合您自己的喜好設定。 設定包括非使用中會話超時、預設視圖、功能表模式、對比、主題、通知，以及語言和區域格式
services: azure-portal
keywords: 設定、超時、語言、區域
author: mgblythe
ms.author: mblythe
ms.date: 08/05/2020
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 0f94f694163ba836091e522a29f73cabd64e22b5
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145934"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>管理 Azure 入口網站設定和喜好設定

您可以變更 Azure 入口網站的預設設定，以符合您自己的喜好設定。 大部分設定都可從全域頁首的 [ **設定** ] 功能表中取得。

![顯示全域頁面標題圖示的螢幕擷取畫面，其中已醒目提示設定](./media/set-preferences/header-settings.png)


## <a name="choose-your-default-subscription"></a>選擇您的預設訂用帳戶

當您登入 Azure 入口網站時，您可以變更預設開啟的訂用帳戶。 如果您有使用的主要訂用帳戶，但偶爾使用其他訂用帳戶，這會很有説明。 

:::image type="content" source="media/set-preferences/filter-subscription-default-view.png" alt-text="依訂用帳戶篩選資源清單。":::

1. 選取全域頁面標頭中的目錄和訂用帳戶篩選圖示。

1. 當您啟動入口網站時，請選取您想要作為預設訂用帳戶的訂用帳戶。 

    :::image type="content" source="media/set-preferences/default-directory-subscription-filter.png" alt-text="依訂用帳戶篩選資源清單。"::: 


## <a name="choose-your-default-view"></a>選擇您的預設視圖 

您可以在登入 Azure 入口網站時，變更預設開啟的頁面。

![顯示已醒目提示預設視圖之 Azure 入口網站設定的螢幕擷取畫面](./media/set-preferences/default-view.png)

- 無法自訂 **Home** 。  它會顯示熱門 Azure 服務的快捷方式，並列出您最近使用過的資源。 我們也會提供有用的資源連結，例如 Microsoft Learn 和 Azure 藍圖。

- 您可以自訂儀表板，以建立專為您設計的工作區。 例如，您可以建立以專案、工作或角色為焦點的儀表板。 如果您選取 [ **儀表板** ]，您的預設視圖將會移至最近使用的儀表板。 如需詳細資訊，請參閱[在 Azure 入口網站中建立和共用儀表板](azure-portal-dashboards.md)。

## <a name="choose-a-portal-menu-mode"></a>選擇入口網站功能表模式

入口網站功能表的預設模式，可控制入口網站功能表在頁面上所需的空間量。

![顯示如何設定入口網站功能表預設模式的螢幕擷取畫面。](./media/set-preferences/menu-mode.png)

- 當入口網站功能表處於 **飛出** 視窗模式時，它會一直隱藏直到您需要為止。 選取功能表圖示以開啟或關閉功能表。

- 如果您選擇入口網站功能表的停駐 **模式** ，它一律會顯示。 您可以摺疊功能表來提供更多的工作空間。

## <a name="choose-a-theme-or-enable-high-contrast"></a>選擇主題或啟用高對比

您選擇的主題會影響出現在 Azure 入口網站中的背景和字型色彩。 您可以從四個預設色彩主題中選取一個。 選取每個縮圖，以找出最適合您的主題。

或者，您可以選擇其中一個高對比主題。 高對比主題讓有視覺障礙的人更容易閱讀 Azure 入口網站;它們會覆寫所有其他主題選項。

![顯示已醒目提示主題之 Azure 入口網站設定的螢幕擷取畫面](./media/set-preferences/theme.png)

## <a name="enable-or-disable-pop-up-notifications"></a>啟用或停用快顯通知

通知是與您目前會話相關的系統訊息。 例如，當您剛才建立的資源變成可用，或確認您的最後一個動作時，它們會提供您目前的點數餘額等資訊。 開啟快顯通知時，訊息會短暫顯示在畫面的右上角。 

若要啟用或停用快顯通知，請選取或清除 [啟用快顯 **通知** ]。

![顯示已醒目提示快顯通知 Azure 入口網站設定的螢幕擷取畫面](./media/set-preferences/popup-notifications.png)

若要讀取在目前會話期間收到的所有通知，請從全域標頭中選取 [ **通知** ]。

![顯示已醒目提示通知的 Azure 入口網站全域標題的螢幕擷取畫面](./media/set-preferences/read-notifications.png)

如果您想要讀取先前會話的通知，請尋找活動記錄中的事件。 如需詳細資訊，請參閱 [查看活動記錄](../azure-monitor/platform/activity-log.md#view-the-activity-log)。 

## <a name="change-the-inactivity-timeout-setting"></a>變更閒置時間設定

如果您忘記保護工作站的安全，則無活動超時設定有助於保護資源不受未經授權的存取。 閒置一段時間之後，您就會自動登出 Azure 入口網站會話。 您可以為個人變更 timeout 設定。 如果您是系統管理員，您可以針對目錄中的所有使用者，在目錄層級進行設定。

### <a name="change-your-individual-timeout-setting-user"></a>變更您的個別 timeout 設定 (使用者) 

選取 [非使用中 **時將我登出** ] 下的下拉式清單。 選擇您的 Azure 入口網站會話在閒置之後登出的持續時間。

![顯示反白顯示非作用中 timeout 設定之入口網站設定的螢幕擷取畫面](./media/set-preferences/inactive-signout-user.png)

變更會自動儲存。 如果您是閒置的，您的 Azure 入口網站會話將會在您設定的持續時間之後登出。

如果您的系統管理員已啟用非使用狀態的超時原則，您仍然可以設定自己的，只要它小於目錄層級設定即可。 選取 **[覆寫目錄非活動** 時間] 原則，然後設定時間間隔。

![顯示入口網站設定的螢幕擷取畫面，其中醒目提示 [覆寫目錄非使用時間] 原則設定](./media/set-preferences/inactive-signout-override.png)

### <a name="change-the-directory-timeout-setting-admin"></a> (系統管理員) 變更 [目錄超時] 設定

[全域管理員角色](../active-directory/roles/permissions-reference.md#global-administrator--company-administrator)中的系統管理員可以強制執行會話登出之前的最大閒置時間。無活動 timeout 設定適用于目錄層級。 此設定會在新的會話中生效。 它不會立即套用至已登入的任何使用者。 如需目錄的詳細資訊，請參閱 [Active Directory Domain Services 總覽](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。

如果您是全域管理員，而且想要為 Azure 入口網站的所有使用者強制執行閒置的 timeout 設定，請遵循下列步驟：

1. 選取連結文字 **設定目錄層級的超時時間** 。

    ![顯示入口網站設定的螢幕擷取畫面，其中已醒目提示連結文字](./media/set-preferences/settings-admin.png)

1. 在 [ **設定目錄層級無啟用時間** ] 頁面上，選取 **[啟用 Azure 入口網站的目錄層級閒置時間** ] 以開啟設定。

1. 接下來，輸入使用者可閒置的時間上限（ **小時** 和 **分鐘）** ，然後才會自動將其會話登出。

1. 選取 [套用]  。

    ![顯示頁面的螢幕擷取畫面，可設定目錄層級的非啟用時間](./media/set-preferences/configure.png)

若要確認是否已正確設定閒置時間原則，請從全域頁面標頭中選取 [ **通知** ]。 確認已列出成功通知。

![螢幕擷取畫面，顯示目錄層級無啟用時間的成功通知訊息](./media/set-preferences/confirmation.png)

## <a name="restore-default-settings"></a>還原預設設定

如果您對 Azure 入口網站設定進行了變更，而且想要捨棄這些設定，請選取 [ **還原預設設定** ]。 您對入口網站設定所做的任何變更都將遺失。 此選項不會影響儀表板自訂專案。

![顯示還原預設設定的螢幕擷取畫面](./media/set-preferences/useful-links-restore-defaults.png)

## <a name="export-user-settings"></a>匯出使用者設定

您自訂設定的相關資訊會儲存在 Azure 中。 您可以匯出下列使用者資料：

* Azure 入口網站中的私人儀表板
* 使用者設定（例如我的最愛訂用帳戶或目錄），以及上次登入目錄
* 主題和其他自訂入口網站設定

如果您打算刪除您的設定，最好先匯出和檢查您的設定。 重建儀表板或重做設定可能會很耗時。

若要匯出您的入口網站設定，請選取 [ **匯出所有設定** ]。

![顯示匯出設定的螢幕擷取畫面](./media/set-preferences/useful-links-export-settings.png)

匯出設定會建立一個包含使用者設定的 *json* 檔案，例如您的色彩主題、我的最愛和私用儀表板。 由於使用者設定的動態本質和資料損毀的風險，您無法從 *json* 檔案匯入設定。

## <a name="delete-user-settings-and-dashboards"></a>刪除使用者設定和儀表板

您自訂設定的相關資訊會儲存在 Azure 中。 您可以刪除下列使用者資料：

* Azure 入口網站中的私人儀表板
* 使用者設定（例如我的最愛訂用帳戶或目錄），以及上次登入目錄
* 主題和其他自訂入口網站設定

在刪除您的設定之前，最好先匯出和檢查您的設定。 重建儀表板或重做自訂設定可能相當耗時。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

若要刪除您的入口網站設定，請選取 [ **刪除所有設定和私人儀表板** ]。

![顯示刪除設定的螢幕擷取畫面](./media/set-preferences/useful-links-delete-settings.png)

## <a name="change-language-and-regional-settings"></a>變更語言及地區設定

有兩個設定可控制 Azure 入口網站中的文字顯示方式： 
- **語言** 設定會控制您在 Azure 入口網站中為文字所看到的語言。 

- **地區設定** 控制日期、時間、數位和貨幣的顯示方式。

若要變更 Azure 入口網站中使用的語言，請使用下拉式清單，從可用語言的清單中選取。

地區格式選取專案會變更為只顯示您所選語言的地區選項。 若要變更自動選取，請使用下拉式清單來選擇您想要的地區格式。

例如，如果您選取英文做為語言，然後選取 [美國] 作為地區格式，貨幣會以美元顯示。 如果您選取英文做為語言，然後選取 [歐洲] 作為地區格式，貨幣會以歐元顯示。

選取 **[** 套用] 以更新您的語言和地區設定。

   ![顯示語言和區域格式設定的螢幕擷取畫面](./media/set-preferences/language.png)

>[!NOTE]
>這些語言和地區設定只會影響 Azure 入口網站。 在新索引標籤或視窗中開啟的檔連結會使用瀏覽器的語言設定來決定要顯示的語言。
>

## <a name="next-steps"></a>下一步

- [Azure 入口網站的鍵盤快速鍵](azure-portal-keyboard-shortcuts.md)
- [支援的瀏覽器和裝置](azure-portal-supported-browsers-devices.md)
- [新增、移除及重新排列我的最愛](azure-portal-add-remove-sort-favorites.md)
- [建立和共用自訂儀表板](azure-portal-dashboards.md)
- [Azure 入口網站操作說明影片系列](azure-portal-video-series.md)
