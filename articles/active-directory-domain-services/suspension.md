---
title: Azure AD 網域服務中的掛起域 |微軟文件
description: 瞭解 Azure AD DS 託管域的不同運行狀況狀態以及如何還原掛起的域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 0b6e28fc92361fe20fc34e92386bf17199b19617
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519017"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>瞭解執行狀況狀態並解析 Azure 的目錄網域服務中的掛起域

當 Azure Active Directory Domain Services (Azure AD DS) 有很長的一段時間無法服務受控網域時，其會使該受控網域進入暫時停權狀態。 如果託管域保持掛起狀態,則會自動刪除它。 要保持 Azure AD DS 託管域正常執行並避免掛起,請盡可能快速解決任何警報。

本文解釋託管域掛起的原因以及如何恢復掛起的域。

## <a name="overview-of-managed-domain-states"></a>託管域狀態概述

在 Azure AD DS 託管域的生命週期中,有不同狀態指示其運行狀況。 如果託管域報告問題,請快速解決根本原因以阻止狀態繼續降級。

![Azure AD DS 託管域需要暫停狀態進度](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Azure AD DS 託管域可以處於以下狀態之一:

* [正在執行](#running-state)
* [需要注意](#needs-attention-state)
* [暫停](#suspended-state)
* [刪除](#deleted-state)

## <a name="running-state"></a>執行狀態

正確配置且運行且沒有問題的 Azure AD DS 託管域處於 *「正在運行」* 狀態。 這是託管域所需的狀態。

### <a name="what-to-expect"></a>未來展望

* Azure 平臺可以定期監視託管域的運行狀況。
* 託管域的域控制器會定期進行修補和更新。
* Azure 活動目錄的更改定期同步到託管域。
* 對託管域進行定期備份。

## <a name="needs-attention-state"></a>需要注意狀態

具有一個或多個需要修復問題的 Azure AD DS 託管域處於 *"需要注意"* 狀態。 託管域的運行狀況頁列出警報,並指示哪裡有問題。 某些警報是暫時性的,由 Azure 平台自動解析。 對於其他警報,您可以按照提供的解決方案步驟來解決此問題。 它有一個嚴重警報,打開[Azure 支援請求][azure-support]以獲取其他故障排除説明。

警報的一個示例是,當有一個限制性的網路安全組。 在此配置中,Azure 平臺可能無法更新和監視託管域。 將生成警報,狀態更改為 *"需要注意*"。

有關詳細資訊,請參閱[如何對 Azure AD DS 託管域的警示進行故障排除][resolve-alerts]。

### <a name="what-to-expect"></a>未來展望

當 Azure AD DS 託管域處於 *"需要注意"* 狀態時,Azure 平臺可能無法定期監視、修補、更新或備份數據。 在某些情況下,例如網路配置無效,託管域的域控制器可能無法到達。

* 託管域處於不正常狀態,在警報解決之前,可能會停止正在進行的運行狀況監視。
* 無法修補或更新託管域的域控制器。
* Azure 活動目錄的更改可能不會同步到託管域。
* 可能無法執行託管域的備份。
* 如果解決影響託管域的非關鍵警報,運行狀況應返回到 *「正在運行」* 狀態。
* 對於 Azure 平台無法到達域控制器的配置問題,將觸發嚴重警報。 如果這些關鍵警報在 15 天內未解決,則託管域將進入*掛起*狀態。

## <a name="suspended-state"></a>暫停狀態

Azure AD DS 託管域進入**掛起**狀態的原因如下:

* 有一或多個重大警示未在 15 天內解決。
    * 重大警示可能是因為設定錯誤而使 Azure AD DS 所需資源的存取遭到封鎖而導致的。 例如，受控網域中的 [AADDS104：網路錯誤][alert-nsg]警示並未在 15 天內解決。
* Azure 訂閱存在計費問題,或者 Azure 訂閱已過期。

當 Azure 平台無法管理、監視、修補或備份域時,託管域將掛起。 託管域處於*掛起*狀態 15 天。 要維護對託管域的訪問,立即解決關鍵警報。

### <a name="what-to-expect"></a>未來展望

當 Azure AD DS 託管域處於*掛起*狀態時,將經歷以下行為:

* 託管域的域控制器已取消預配,並且無法在虛擬網路中進行。
* 安全 LDAP 透過網路對託管域的訪問(如果啟用)將停止工作。
* 身份驗證到託管域、登錄到域加入的 VM 或通過 LDAP/LDAPS 進行連接時存在失敗。
* 不再執行託管域的備份。
* 與 Azure AD 的同步處理會停止。

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>如何得知您的受控網域已被暫時停權？

在 Azure 門戶中,Azure AD DS 運行狀況頁上會顯示[一條警報][resolve-alerts],指出域已掛起。 網域的狀態還顯示*掛起*。

### <a name="restore-a-suspended-domain"></a>還原暫時停權的網域

要還原處於*掛起*狀態的 Azure AD DS 託管域的運行狀況,請完成以下步驟:

1. 在 Azure 門戶中,搜尋並選擇**網域服務**。
1. 從清單中選擇 Azure AD DS 託管域,如*aaddscontoso.com*,然後選擇 **「運行狀況**」。
1. 根據掛起的原因選擇警報,如*AADDS503*或*AADDS504。*
1. 選擇警報中提供的解決方法連結,然後按照步驟進行解析。

只能將託管域還原到上次備份的日期。 上次備份的日期顯示在託管域的 **「運行狀況**」 頁上。 在上次備份之後發生的任何變更都將無法還原。 受控網域的備份最多儲存 30 天。 超過 30 天的備份會被刪除。

在託管域處於*掛起*狀態時解決警報后,打開 Azure[支援請求][azure-support]以返回到正常狀態。 如果備份少於 30 天,Azure 支援可以還原託管域。

## <a name="deleted-state"></a>已刪除狀態

如果 Azure AD DS 託管域處於*掛起*狀態 15 天,則將其刪除。 此過程不可恢復。

### <a name="what-to-expect"></a>未來展望

當 Azure AD DS 託管域進入 *「已刪除」* 狀態時,可以看到以下行為:

* 該受控網域的所有資源和備份都會被刪除。
* 無法還原託管域,需要創建替換託管域以重用 Azure AD DS。
* 將它刪除之後，您就不需繼續支付該受控網域的費用。

## <a name="next-steps"></a>後續步驟

要保持 Azure AD DS 託管域的健康並盡量減少其掛起的風險,請瞭解如何[解決託管域的警報][resolve-alerts]。

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
