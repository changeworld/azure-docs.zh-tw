---
title: 從 Android 連接到 Windows 虛擬桌面-Azure
description: 如何使用 Android 用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6c1ffb4b1c0018cf56771d3fea56fe56d151bae3
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612923"
---
# <a name="connect-with-the-android-client"></a>與 Android 用戶端連線

> 適用于： Android 4.1 和更新版本，使用 ChromeOS 53 和更新版本 Chromebook。

>[!IMPORTANT]
>此內容適用于具有 Azure Resource Manager Windows 虛擬桌面物件的春季2020更新。 如果您使用的是 Windows 虛擬桌面不含 Azure Resource Manager 物件的2019版，請參閱[這篇文章](./virtual-desktop-fall-2019/connect-android-2019.md)。
>
> Windows 虛擬桌面春季2020更新目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議針對生產環境工作負載使用。 可能不支援特定功能，或可能已經限制功能。 
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

>[!NOTE]
> 從 Android 用戶端存取 Windows 虛擬桌面資源的能力目前已提供預覽。

您可以使用可下載的用戶端，從您的 Android 裝置存取 Windows 虛擬桌面資源。 您也可以在支援 Google Play 商店的 Chromebook 裝置上使用 Android 用戶端。 本指南將告訴您如何設定 Android 用戶端。

## <a name="install-the-android-client"></a>安裝 Android 用戶端

若要開始使用，請在您的 Android 裝置上[下載](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)並安裝用戶端。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

訂閱您的系統管理員所提供的摘要，以取得您可以在 Android 裝置上存取的受控資源清單。

若要訂閱摘要：

1. 在 [連線中心] 上，點選 **+** ，然後點選 [遠端資源摘要]  。
2. 在 [摘要**url** ] 欄位中輸入摘要 url。 摘要 URL 可以是 URL 或電子郵件地址。
   - 如果您使用 URL，通常<https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>會使用您系統管理員提供給您的帳戶。
   - 若要使用電子郵件，請輸入您的電子郵件地址。 如果您的系統管理員以這種方式設定伺服器，用戶端將會搜尋與您的電子郵件地址相關聯的 URL。
3. 按 **[下一步]**。
4. 出現提示時，請提供您的認證。
   - 針對 [**使用者名稱**]，為使用者名稱提供存取資源的許可權。
   - 針對 [**密碼**]，提供與使用者名稱相關聯的密碼。
   - 如果您的系統管理員以這種方式設定驗證，可能也會提示您提供其他因素。

訂閱之後，[連接中心] 應該會顯示遠端資源。

訂閱摘要之後，摘要的內容將會定期自動更新。 根據系統管理員所做的變更，可能會新增、變更或移除資源。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用 Android 用戶端，請參閱[開始使用 android 用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/)。
