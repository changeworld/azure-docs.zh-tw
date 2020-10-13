---
title: 連接到 Microsoft Store 用戶端-Azure
description: 如何使用 Microsoft Store 用戶端連接到 Windows 虛擬桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9bab2a11ff9e7907621087e5027929e3e42eaf29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744756"
---
# <a name="connect-with-the-microsoft-store-client"></a>與 Microsoft Store 用戶端連線

>適用于： Windows 10。

您可以使用 Windows 10 存取裝置上的 Windows 虛擬桌面資源。

## <a name="install-the-microsoft-store-client"></a>安裝 Microsoft Store 用戶端

您可以為目前的使用者安裝用戶端，而不需要系統管理員許可權。 或者，您的系統管理員可以安裝和設定用戶端，讓裝置上的所有使用者都可以存取它。

安裝後，您可藉由搜尋遠端桌面，從 [開始] 功能表啟動用戶端。

若要開始使用，請 [從 Microsoft Store 下載並安裝用戶端](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS)。

## <a name="subscribe-to-a-workspace"></a>訂閱工作區

訂閱系統管理員提供的工作區，以取得您可以在電腦上存取的受控資源清單。

若要訂閱工作區：

1. 在 [連接中心] 畫面中，依序按一下 [ **+ 新增**] 和 [ **工作區**]。
2. 在系統管理員提供的工作區 URL 欄位中輸入工作區 URL。工作區 URL 可以是 URL 或電子郵件地址。
   
   - 如果您使用的是工作區 URL，請使用您的系統管理員提供給您的 URL。
   - 如果您是從 Windows 虛擬桌面連線，請使用下列其中一個 Url，視您使用的服務版本而定：
       - Windows 虛擬桌面 (傳統) ： `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx` 。
       - Windows 虛擬 `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery` 桌面：
  
3. 請按一下 [ **訂閱**]。
4. 出現提示時，請提供您的認證。
5. 訂閱之後，工作區應該會顯示在 [連接中心] 中。

您可以根據系統管理員所做的變更來新增、變更或移除工作區。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用 Microsoft Store 用戶端，請參閱 [Microsoft Store 用戶端入門](/windows-server/remote/remote-desktop-services/clients/windows/)。