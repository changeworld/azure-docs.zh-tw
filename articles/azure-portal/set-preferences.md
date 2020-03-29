---
title: 設置 Azure 門戶首選項 |微軟文檔
description: 您可以更改 Azure 門戶預設設置以滿足自己的首選項。 設置包括非活動會話超時、預設視圖、功能表模式、對比度、主題、通知以及語言和區域格式
services: azure-portal
keywords: 設置、超時、語言、區域
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310674"
---
# <a name="set-your-azure-portal-preferences"></a>設定您的 Azure 入口網站喜好設定

您可以更改 Azure 門戶的預設設置以滿足自己的首選項。 可以更改下面列出的每個設置：

* [非活動會話超時](#change-the-idle-duration-for-inactive-sign-out)
* [預設視圖](#choose-your-default-view)
* [門戶功能表模式](#choose-a-portal-menu-mode)
* [色彩和高對比主題](#choose-a-theme)
* [彈出式通知](#enable-or-disable-pop-up-notifications)
* [語言和地區格式](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>更改常規門戶設置

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 從全域頁面標題中選擇 **"設置**"。

    ![顯示帶有突出顯示設定的全域頁面標題圖示的螢幕截圖](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>更改非活動登出的閒置時間持續時間

不活動超時設置有助於保護資源，防止您忘記保護工作站時受到未經授權的訪問。 空閒一段時間後，將自動登出 Azure 門戶會話。

**選擇在非活動時"登出"** 下的下拉清單。 如果處於空閒狀態，請選擇 Azure 門戶會話登出的持續時間。

   ![顯示突出顯示非活動超時設置的門戶設置的螢幕截圖](./media/set-preferences/inactive-signout-user.png)

更改將自動儲存。 如果處於空閒狀態，則 Azure 門戶會話將在設置的持續時間後登出。

此設置也可以由目錄級別的管理員進行，以強制最大閒置時間。 如果管理員已創建目錄級超時設置，您仍然可以設置自己的非活動登出持續時間。 選擇小於目錄級別設置的時間設置。

如果您的管理員啟用了非活動超時策略，請選擇"**覆蓋目錄不活動超時策略**"核取方塊。 設置小於策略設置的時間間隔。

   ![顯示具有覆蓋突出顯示的目錄不活動超時策略設置的門戶設置的螢幕截圖](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> 如果您是管理員，並且希望為 Azure 門戶的所有使用者強制實施非活動超時設置，請參閱[為 Azure 門戶的使用者設置目錄級不活動超時](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>選擇預設視圖 

您可以更改預設情況下登錄 Azure 門戶時打開的頁面。

   ![顯示突出顯示預設視圖的 Azure 門戶設置的螢幕截圖](./media/set-preferences/default-view.png)

預設視圖設置控制登錄時顯示的 Azure 門戶視圖。 預設情況下，您可以選擇打開 Azure 主頁或儀表板視圖。

* **無法自訂主頁**。  它顯示熱門 Azure 服務的快捷方式，並列出您最近使用的資源。 我們還為您提供指向 Microsoft 學習和 Azure 路線圖等資源的有用連結。
* 可以自訂儀表板以創建專為您設計的工作區。 例如，可以生成以專案、任務或角色為中心的儀表板。 如果選擇 **"儀表板"，** 則預設視圖將轉到最近使用的儀表板。

### <a name="choose-a-portal-menu-mode"></a>選擇門戶功能表模式

門戶功能表的預設模式控制門戶功能表在頁面上佔用的空間。

* 當門戶功能表處於**快顯視窗**時，它將隱藏，直到您需要它。 選擇功能表圖示以打開或關閉功能表。
* 如果為門戶功能表選擇**停靠**模式，則始終可見。 您可以折疊功能表以提供更多的工作空間。 

### <a name="choose-a-theme"></a>選擇佈景主題

您選擇的主題會影響 Azure 門戶中顯示的背景和字體顏色。 您可以從四個預設顏色主題之一中進行選擇。 選擇每個縮略圖，找到最適合您的主題。

   ![顯示突出顯示主題的 Azure 門戶設置的螢幕截圖](./media/set-preferences/theme.png)

您可以選擇高對比主題之一。 高對比設置使 Azure 門戶更易於視力受損使用者閱讀，並覆蓋所有其他主題選擇。 有關詳細資訊，請參閱[打開高對比或更改主題](azure-portal-change-theme-high-contrast.md)。

### <a name="enable-or-disable-pop-up-notifications"></a>啟用或禁用彈出式通知

通知是與您的當前會話相關的系統消息。 例如，當您剛剛創建的資源可用時，它們會提供您當前的信用餘額等資訊，或者確認您的最後一個操作。 打開快顯視窗通知後，消息會短暫顯示在螢幕的頂角。 

要啟用或禁用彈出式通知，請選擇或取消選擇"**啟用彈出式通知**"核取方塊。

   ![顯示 Azure 門戶設置的螢幕截圖，突出顯示了快顯視窗通知](./media/set-preferences/popup-notifications.png)

要讀取當前會話期間收到的所有通知，請從全域標頭中選擇 **"通知**"。

   ![顯示 Azure 門戶全域標頭的螢幕截圖，其中突出顯示了通知](./media/set-preferences/read-notifications.png)

如果要閱讀以前會話中的通知，請查找活動日誌中的事件。 若要瞭解詳細資訊，請閱讀["查看"並檢索 Azure 活動日誌事件](/azure/azure-monitor/platform/activity-log-view)。

### <a name="settings-under-useful-links"></a>有用連結下的設置

如果對 Azure 門戶設置進行了更改並希望放棄這些設置，請選擇"**還原預設設置**"。 您對門戶設置所做的任何更改都將丟失。 此選項不會影響儀表板自訂。

有關**匯出所有設置**或刪除**所有設置和專用儀表板**的詳細資訊，請參閱[匯出或刪除使用者設置](azure-portal-export-delete-settings.md)。

## <a name="change-language-and-regional-settings"></a>更改語言和地區設定

有兩種設置控制 Azure 門戶中的文本顯示方式。 **"語言**"設置控制您在 Azure 門戶中看到的文本的語言。 **區域格式**控制日期、時間、數位和貨幣的顯示方式。

要更改 Azure 門戶中使用的語言，請使用下拉清單從可用語言清單中選擇。

區域格式選擇將更改為僅顯示所選語言的區域選項。 要更改該自動選擇，請使用下拉清單選擇所需的區域格式。

例如，如果您選擇英語作為您的語言，然後選擇美國作為區域格式，則貨幣以美元顯示。 如果您選擇英語作為語言，然後選擇歐洲作為區域格式，則貨幣以歐元顯示。

選擇 **"應用"** 以更新您的語言和區域格式設置。

   ![顯示語言和區域格式設置的螢幕截圖](./media/set-preferences/language.png)

>[!NOTE]
>這些語言和地區設定僅影響 Azure 門戶。 在新選項卡或視窗中打開的文檔連結將使用瀏覽器的語言設置來確定要顯示的語言。
>

## <a name="next-steps"></a>後續步驟

* [建立和共用自訂儀表板](azure-portal-dashboards.md)
* [Azure 入口網站操作說明影片系列](azure-portal-video-series.md)
