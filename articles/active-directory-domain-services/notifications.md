---
title: Azure AD 網域服務的電子郵件通知 |微軟文件
description: 讓您要設定電子郵件通知以提醒您 Azure 的目錄網域服務託管網域
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 8c6d59889e572893877f2178cade57e07aa91413
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654794"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>為 Azure 活動目錄網域服務中的問題設定電子郵件通知

Azure 活動目錄域服務 (Azure AD DS) 託管域的運行狀況由 Azure 平台監視。 Azure 門戶中的運行狀況狀態頁顯示託管域的任何警報。 為了確保及時回應問題,可以將電子郵件通知配置為在 Azure AD DS 託管域中檢測到運行狀況警報後立即報告。

本文介紹如何為 Azure AD DS 託管域配置電子郵件通知收件者。

## <a name="email-notification-overview"></a>電子郵件通知概述

要提醒您 Azure AD DS 託管域的問題,可以配置電子郵件通知。 這些電子郵件通知指定警報存在的 Azure AD DS 託管域,以及提供檢測時間和指向 Azure 門戶中運行狀況頁的連結。 然後,您可以按照提供的故障排除建議來解決問題。

以下範例電子郵件通知指示在 Azure AD DS 託管域上生成了嚴重警告或警報:

![範例電子郵件通知](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> 在按一下郵件中的連結之前,請務必確保電子郵件來自經過驗證的 Microsoft 寄件者。 電子郵件通知始終來自位址`azure-noreply@microsoft.com`。

### <a name="why-would-i-receive-email-notifications"></a>為什麼我會收到電子郵件通知？

Azure AD DS 會發送有關託管域的重要更新的電子郵件通知。 這些通知僅適用於影響服務的緊急問題,應立即解決。 每個電子郵件通知都由 Azure AD DS 託管域上的警報觸發。 警報也顯示在 Azure 門戶中,可以在 Azure AD [DS 運行狀況頁上][check-health]查看。

Azure AD DS 不會為廣告、更新或銷售目的發送電子郵件。

### <a name="when-will-i-receive-email-notifications"></a>何時會收到電子郵件通知？

在 Azure AD DS 託管域上發現[新警報][troubleshoot-alerts]時,將立即發送通知。 如果警報未解決,則每四天發送一次其他電子郵件通知作為提醒。

### <a name="who-should-receive-the-email-notifications"></a>誰應該收到電子郵件通知？

Azure AD DS 的電子郵件收件者清單應由能夠管理和更改託管域的人員組成。 此電子郵件清單應視為任何警報和問題的"第一回應者"

您最多可以添加五個其他電子郵件收件者的電子郵件通知。 如果希望超過五個收件者用於電子郵件通知,請創建一個通訊組清單,並將其添加到通知清單中。

您還可以選擇讓 Azure AD 目錄的所有*全域管理員*和*AAD DC 管理員*組的每個成員接收電子郵件通知。 Azure AD DS 僅向多達 100 個電子郵件地址發送通知,包括全域管理員和 AAD DC 管理員的清單。

## <a name="configure-email-notifications"></a>設定電子郵件通知

要檢視現有電子郵件通知收件者或添加其他收件者,請完成以下步驟:

1. 在 Azure 門戶中,搜尋並選擇**Azure AD 網域服務**。
1. 選擇 Azure AD DS 託管域,如*aaddscontoso.com*。
1. 在 Azure AD DS 資源視窗的左邊,選擇**通知設定**。 將顯示電子郵件通知的現有收件者。
1. 要添加電子郵件收件者,請在其他收件者表中輸入電子郵件位址。
1. 完成後,選擇「**在**頂部導航上保存」。

> [!WARNING]
> 更改通知設定時,將更新整個 Azure AD DS 託管域的通知設置,而不僅僅是更新您自己。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>我收到警示的電子郵件通知，但是登入 Azure 入口網站時並沒有任何警示。 發生什麼情形？

如果警報已解決,則從 Azure 門戶清除警報。 最可能的原因是,收到電子郵件通知的其他人解決了 Azure AD DS 託管域上的警報,或者該警報被 Azure 平臺自動解析。

### <a name="why-can-i-not-edit-the-notification-settings"></a>為什麼我無法編輯通知設定？

如果無法訪問 Azure 門戶中的通知設置頁,則無權編輯 Azure AD DS 託管域。 您必須與全域管理員聯繫,以獲得編輯 Azure AD DS 資源的許可權或從收件者清單中刪除的許可權。

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>即使我已經提供電子郵件地址，似乎還是無法接收電子郵件通知。 原因為何？

檢查電子郵件中的垃圾郵件或垃圾郵件資料夾,瞭解通知,並確保允許寄件者`azure-noreply@microsoft.com`。

## <a name="next-steps"></a>後續步驟

有關排除可能報告的一些問題的疑難排解的詳細資訊,請參閱[解決 Azure AD DS 託管域上的警報][troubleshoot-alerts]。

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
