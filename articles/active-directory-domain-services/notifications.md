---
title: Azure AD Domain Services 的電子郵件通知 |Microsoft Docs
description: 瞭解如何設定電子郵件通知，以警示您 Azure Active Directory Domain Services 受控網域中的問題
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 149de14ed2430613e9ca6d5d12f8dce6ba425dfe
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86039988"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>在 Azure Active Directory Domain Services 中設定問題的電子郵件通知

受 Azure 平臺監視的 Azure Active Directory Domain Services （Azure AD DS）受控網域的健康情況。 [Azure 入口網站中的 [健全狀況狀態] 頁面會顯示受控網域的任何警示。 若要確保及時回應問題，可以將電子郵件通知設定為在 Azure AD DS 受控網域中偵測到健康情況警示時立即進行報告。

本文說明如何設定受控網域的電子郵件通知收件者。

## <a name="email-notification-overview"></a>電子郵件通知總覽

若要對受控網域的問題發出警示，您可以設定電子郵件通知。 這些電子郵件通知會指定出現警示的受控網域，以及提供偵測時間和 Azure 入口網站中 [健康情況] 頁面的連結。 接著，您可以遵循提供的疑難排解建議來解決問題。

下列範例電子郵件通知表示受控網域上產生重大警告或警示：

![範例電子郵件通知](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> 在您按一下郵件中的連結之前，請務必確定電子郵件來自已驗證的 Microsoft 寄件者。 電子郵件通知一律來自 `azure-noreply@microsoft.com` 位址。

### <a name="why-would-i-receive-email-notifications"></a>為什麼我會收到電子郵件通知？

Azure AD DS 會針對受控網域的重要更新傳送電子郵件通知。 這些通知僅適用于影響服務且應立即解決的緊急問題。 每個電子郵件通知都是由受控網域上的警示所觸發。 警示也會出現在 [Azure 入口網站中，而且可以在 [ [AZURE AD DS 健全狀況] 頁面][check-health]上查看。

Azure AD DS 不會傳送電子郵件，供廣告、更新或銷售之用。

### <a name="when-will-i-receive-email-notifications"></a>何時會收到電子郵件通知？

在受控網域上找到[新警示][troubleshoot-alerts]時，就會立即傳送通知。 如果警示未解決，則會每隔四天以提醒方式傳送額外的電子郵件通知。

### <a name="who-should-receive-the-email-notifications"></a>誰應該收到電子郵件通知？

Azure AD DS 的電子郵件收件者清單應由能夠管理和變更受控網域的人員所組成。 此電子郵件清單應視為任何警示和問題的「第一次回應」。

您可以為電子郵件通知新增最多五個額外的電子郵件收件者。 如果您想要超過五位收件者的電子郵件通知，請建立通訊群組清單，並改為將其新增至通知清單。

您也可以選擇讓 Azure AD 目錄的所有*全域管理員*，而*AAD DC 系統管理員*群組的每個成員都會收到電子郵件通知。 Azure AD DS 只會將通知傳送到最多100的電子郵件地址，包括全域管理員和 AAD DC 系統管理員的清單。

## <a name="configure-email-notifications"></a>設定電子郵件通知

若要檢查現有的電子郵件通知收件者或新增其他收件者，請完成下列步驟：

1. 在 [Azure 入口網站中，搜尋並選取 [ **Azure AD Domain Services**]。
1. 選取您的受控網域，例如*aaddscontoso.com*。
1. 在 [Azure AD DS 資源] 視窗的左側，選取 [**通知設定**]。 會顯示電子郵件通知的現有收件者。
1. 若要新增電子郵件收件者，請在 [其他收件者] 資料表中輸入電子郵件地址。
1. 完成時，請選取頂端導覽上的 [**儲存**]。

> [!WARNING]
> 當您變更通知設定時，系統會更新整個受控網域的通知設定，而不只是您自己。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>我收到警示的電子郵件通知，但是登入 Azure 入口網站時並沒有任何警示。 發生什麼事？

如果警示已解決，則會從 [Azure 入口網站] 中清除警示。 最有可能的原因是，收到電子郵件通知的其他人已在受控網域上解決警示，或由 Azure 平臺自動解決。

### <a name="why-can-i-not-edit-the-notification-settings"></a>為什麼我無法編輯通知設定？

如果您無法存取 Azure 入口網站中的 [通知設定] 頁面，表示您沒有編輯受控網域的許可權。 請洽詢全域管理員，以取得編輯 Azure AD DS 資源或從收件者清單中移除的許可權。

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>即使我已經提供電子郵件地址，似乎還是無法接收電子郵件通知。 為什麼？

請檢查電子郵件中的垃圾郵件資料夾中是否有通知，並務必允許的寄件者 `azure-noreply@microsoft.com` 。

## <a name="next-steps"></a>後續步驟

如需有關疑難排解可能回報之問題的詳細資訊，請參閱[解決受控網域上的警示][troubleshoot-alerts]。

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
