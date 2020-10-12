---
title: 從 iOS 連接到 Windows 虛擬桌面-Azure
description: 如何使用 iOS 用戶端連接到 Windows 虛擬桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 87bb2cc53ce056552e8f44aac4ade96e603a8787
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230036"
---
# <a name="connect-to-windows-virtual-desktop-with-the-ios-client"></a>使用 iOS 用戶端連接到 Windows 虛擬桌面

> 適用于： iOS 13.0 或更新版本。 與 iPhone、iPad 和 iPod touch 相容。

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 (傳統版)，請參閱[這篇文章](./virtual-desktop-fall-2019/connect-ios-2019.md)。

您可以透過可下載的用戶端，從 iOS 裝置存取 Windows 虛擬桌面資源。 本指南將告訴您如何設定 iOS 用戶端。

## <a name="install-the-ios-client"></a>安裝 iOS 用戶端

若要開始使用，請在您的 iOS 裝置上 [下載](https://aka.ms/rdios) 並安裝用戶端。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

訂閱系統管理員提供的摘要，以取得您可以在 iOS 裝置上存取的受控資源清單。

若要訂閱摘要：

1. 在 [連接中心] 中，按一下，然後按 **+** [ **新增工作區**]。
2. 在 [摘要 **url** ] 欄位中輸入摘要 url。 摘要 URL 可以是 URL 或電子郵件地址。
   - 如果您使用 URL，請使用系統管理員提供給您的帳戶。 此 URL 通常是 <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>。
   - 若要使用電子郵件，請輸入您的電子郵件地址。 這會告訴用戶端搜尋與您的電子郵件地址相關聯的 URL (如果系統管理員是這樣設定伺服器的話)。
   - 若要透過 US Gov 入口網站連接，請使用 <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> 。
3. 點選 [下一步]。
4. 出現提示時，請提供您的認證。
   - 針對 [ **使用者名稱**]，授與具有存取資源許可權的使用者名稱。
   - 在 [ **密碼**] 中，提供與使用者名稱相關聯的密碼。
   - 如果系統管理員以這種方式設定驗證，系統可能也會提示您提供其他因素。
5. 點選 [儲存]  。

在此之後，連線中心應該會顯示遠端資源。

訂閱摘要之後，摘要的內容便會定期自動更新。 根據系統管理員所做的變更，可能會新增、變更或移除資源。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用 iOS 用戶端，請參閱 [開始使用 ios 用戶端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) 檔。
