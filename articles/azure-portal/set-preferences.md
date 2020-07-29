---
title: 設定您的 Azure 入口網站喜好設定 |Microsoft Docs
description: 您可以變更 Azure 入口網站預設值，以符合您自己的喜好設定。 設定包括非使用中會話超時、預設視圖、功能表模式、對比、主題、通知，以及語言和地區格式
services: azure-portal
keywords: 設定、超時、語言、地區
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: how-to
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: d4c675ab18a7a9231229ce0c7de9a7450dc1baa8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763953"
---
# <a name="set-your-azure-portal-preferences"></a>設定您的 Azure 入口網站喜好設定

您可以變更 Azure 入口網站的預設值，以符合您自己的喜好設定。 下面列出的每個設定都可以變更：

* [非使用中會話超時](#change-the-idle-duration-for-inactive-sign-out)
* [預設視圖](#choose-your-default-view)
* [入口網站功能表模式](#choose-a-portal-menu-mode)
* [色彩和高對比主題](#choose-a-theme)
* [快顯通知](#enable-or-disable-pop-up-notifications)
* [語言和地區格式](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>變更一般入口網站設定

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從全域頁面標頭中選取 [**設定**]。

    ![顯示醒目提示設定之全域頁首圖示的螢幕擷取畫面](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>變更非作用中登出的閒置持續時間

如果您忘記保護您的工作站，無活動超時設定有助於保護資源免于未經授權的存取。 在閒置一段時間之後，您就會自動登出 Azure 入口網站會話。

選取 [在非使用中**時將我登出**] 底下的下拉式選單。 選擇您的 Azure 入口網站會話已登出的持續時間（如果您處於閒置狀態）。

   ![顯示已醒目提示非使用中超時設定的入口網站設定的螢幕擷取畫面](./media/set-preferences/inactive-signout-user.png)

變更會自動儲存。 如果您是閒置的，您的 Azure 入口網站會話會在您設定的持續時間之後登出。

這項設定也可以由目錄層級的系統管理員進行，以強制執行最大閒置時間。 如果系統管理員已建立目錄層級的超時設定，您仍然可以設定自己的非作用中登出持續時間。 選擇小於目錄層級設定的時間設定。

如果您的系統管理員已啟用非使用狀態的超時原則，請選取 [覆**寫目錄非使用時間超時原則**] 核取方塊。 設定小於原則設定的時間間隔。

   ![顯示已醒目提示 [目錄非使用時間超時] 原則設定之入口網站設定的螢幕擷取畫面](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> 如果您是系統管理員，而且想要為 Azure 入口網站的所有使用者強制執行非作用中的 timeout 設定，請參閱[為使用者的 Azure 入口網站設定目錄層級的非使用時間超時](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>選擇您的預設 view 

當您登入 Azure 入口網站時，您可以變更預設開啟的頁面。

   ![顯示已醒目提示預設視圖之 Azure 入口網站設定的螢幕擷取畫面](./media/set-preferences/default-view.png)

預設的 [view] 設定可控制當您登入時顯示的 Azure 入口網站 view。 根據預設，您可以選擇開啟 [Azure 首頁] 或 [儀表板] 視圖。

* 無法自訂**首頁**。  它會顯示熱門 Azure 服務的快捷方式，並列出您最近使用過的資源。 我們也會提供您資源的實用連結，例如 Microsoft Learn 和 Azure 藍圖。
* 您可以自訂儀表板，以建立專為您設計的工作區。 例如，您可以建立以專案、工作或角色為焦點的儀表板。 如果您選取 [**儀表板**]，您的預設視圖會移至最近使用的儀表板。

### <a name="choose-a-portal-menu-mode"></a>選擇入口網站功能表模式

入口網站功能表的預設模式控制入口網站功能表在頁面上佔用的空間量。

* 當入口網站功能表處於**飛出**視窗模式時，在您需要的時候才會隱藏。 選取功能表圖示以開啟或關閉功能表。
* 如果**您選擇 [** 停駐模式] 作為入口網站功能表，則一律會顯示。 您可以摺疊功能表來提供更多的工作空間。 

### <a name="choose-a-theme"></a>選擇佈景主題

您選擇的主題會影響出現在 Azure 入口網站中的背景和字型色彩。 您可以從四個預設色彩主題的其中一個進行選取。 選取每個縮圖，以尋找最適合您的主題。

   ![顯示已醒目提示主題之 Azure 入口網站設定的螢幕擷取畫面](./media/set-preferences/theme.png)

您可以改為選擇其中一個高對比主題。 高對比設定讓具有視覺障礙的人更容易閱讀 Azure 入口網站，並覆寫所有其他主題選取專案。 如需詳細資訊，請參閱[開啟高對比或變更主題](azure-portal-change-theme-high-contrast.md)。

### <a name="enable-or-disable-pop-up-notifications"></a>啟用或停用快顯通知

通知是與您目前會話相關的系統訊息。 例如，當您剛建立的資源可供使用時，他們會提供您目前的信用額度餘額等資訊，或確認您的最後一個動作。 開啟快顯通知時，訊息會短暫顯示在畫面的右上角。 

若要啟用或停用快顯通知，請選取或取消選取 [啟用快顯**通知**] 核取方塊。

   ![顯示已醒目提示快顯通知的 Azure 入口網站設定的螢幕擷取畫面](./media/set-preferences/popup-notifications.png)

若要讀取目前會話期間收到的所有通知，請從全域標頭選取 [**通知**]。

   ![顯示已醒目提示通知之 Azure 入口網站全域標頭的螢幕擷取畫面](./media/set-preferences/read-notifications.png)

如果您想要從先前的會話讀取通知，請在活動記錄檔中尋找事件。 若要深入瞭解，請參閱[View and 取出 Azure 活動記錄事件](/azure/azure-monitor/platform/activity-log-view)。

### <a name="settings-under-useful-links"></a>有用連結下的設定

如果您已對 Azure 入口網站設定進行變更，並想要捨棄它們，請選取 [**還原預設設定**]。 您對入口網站設定所做的任何變更都將遺失。 此選項不會影響儀表板的自訂專案。

如需**匯出所有設定**或**刪除所有設定和私人儀表板**的詳細資訊，請參閱[匯出或刪除使用者設定](azure-portal-export-delete-settings.md)。

## <a name="change-language-and-regional-settings"></a>變更語言和地區設定

有兩個設定可控制 Azure 入口網站中文字的顯示方式。 [**語言**] 設定可控制您在 Azure 入口網站中看到的文字語言。 **地區格式**控制日期、時間、數位和貨幣的顯示方式。

若要變更 Azure 入口網站中使用的語言，請使用下拉式清單從可用的語言清單中選取。

地區格式選取專案會變更為只顯示您選取之語言的地區選項。 若要變更該自動選取專案，請使用下拉式選選擇您想要的地區格式。

例如，如果您選取 [英文] 做為語言，然後選取 [美國] 作為地區格式，貨幣會以美元顯示。 如果您選取 [英文] 做為語言，然後選取 [歐洲] 做為地區格式，貨幣會以歐元顯示。

選取 **[** 套用] 以更新您的語言和地區設定。

   ![顯示語言和地區設定格式設定的螢幕擷取畫面](./media/set-preferences/language.png)

>[!NOTE]
>這些語言和地區設定只會影響 Azure 入口網站。 在新索引標籤或視窗中開啟的檔連結，將會使用您瀏覽器的語言設定來決定要顯示的語言。
>

## <a name="next-steps"></a>後續步驟

* [建立和共用自訂儀表板](azure-portal-dashboards.md)
* [Azure 入口網站操作說明影片系列](azure-portal-video-series.md)
