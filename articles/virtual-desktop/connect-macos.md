---
title: 從 macOS 連接到 Windows 虛擬桌面-Azure
description: 如何使用 macOS 用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bcd7bf46a3def963a4f9fe8913c4f8afcdce743f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075643"
---
# <a name="connect-to-windows-virtual-desktop-with-the-macos-client"></a>使用 macOS 用戶端連接到 Windows 虛擬桌面

> 適用于： macOS 10.12 或更新版本

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/connect-macos-2019.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以使用可下載的用戶端，從 macOS 裝置存取 Windows 虛擬桌面資源。 本指南將告訴您如何設定用戶端。

## <a name="install-the-client"></a>安裝用戶端

若要開始使用[download](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)，請   在您的 macOS 裝置上下載並安裝用戶端。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

訂閱系統管理員提供給您的摘要，以取得可供您在 macOS 裝置上使用的受控資源清單。

若要訂閱摘要：

1. 選取主頁面上的 [**新增工作區**]，以連線至服務並抓取您的資源。
2. 輸入摘要 URL。 這可以是 URL 或電子郵件地址：
   - 如果您使用 URL，請使用系統管理員提供給您的帳戶。 此 URL 通常是 <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>。
   - 若要使用電子郵件，請輸入您的電子郵件地址。 這會告訴用戶端搜尋與您的電子郵件地址相關聯的 URL (如果系統管理員是這樣設定伺服器的話)。
3. 選取 [新增]。
4. 出現提示時，使用您的使用者帳戶登入。

登入之後，您應該會看到可用資源的清單。

訂閱摘要之後，摘要的內容便會定期自動更新。 根據系統管理員所做的變更，可能會新增、變更或移除資源。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 macOS 用戶端，請參閱[開始使用 macOS 用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)檔。
