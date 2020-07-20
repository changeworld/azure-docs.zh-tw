---
title: 連接到 Windows 虛擬桌面 Windows 10 或 7-Azure
description: 如何使用 Windows 桌面用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 73811aa58b09e394468596f42e0ff221b2bb240b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261809"
---
# <a name="connect-with-the-windows-desktop-client"></a>與 Windows 桌面用戶端連線

> 適用于： Windows 7、Windows 10 和 Windows 10 IoT 企業版

>[!IMPORTANT]
>此內容適用於具有 Azure Resource Manager Windows 虛擬桌面物件的 2020 年春季更新版。 如果您使用不含 Azure Resource Manager 物件的 Windows 虛擬桌面 2019 年秋季版，請參閱[這篇文章](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md)。
>
> Windows 虛擬桌面 2020 年春季更新版目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議您將其用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以使用 Windows 桌面用戶端，存取 Windows 7、Windows 10 和 Windows 10 IoT 企業版裝置上的 Windows 虛擬桌面資源。

>[!NOTE]
>Windows 用戶端會自動預設為 Windows 虛擬桌面秋季2019版。 不過，如果用戶端偵測到使用者也有 Azure Resource Manager 資源，它會自動新增資源，或通知使用者其可用。

> [!IMPORTANT]
> Windows 虛擬桌面不支援 RemoteApp 和桌面連線 (RADC) 用戶端或遠端桌面連線 (MSTSC) 用戶端。

> [!IMPORTANT]
> Windows 虛擬桌面目前不支援來自 Windows Store 的遠端桌面用戶端。

## <a name="install-the-windows-desktop-client"></a>安裝 Windows 桌面用戶端

選擇符合您 Windows 版本的用戶端：

- [Windows 64 位元](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 位元](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

您可以為目前使用者安裝用戶端，這不需要系統管理員權限，或者您的系統管理員可以安裝和設定用戶端，讓裝置上的所有使用者都可以存取它。

安裝後，您可藉由搜尋**遠端桌面**，從 [開始] 功能表啟動用戶端。

## <a name="subscribe-to-a-workspace"></a>訂閱工作區

訂閱工作區有兩種方式。 用戶端可以嘗試從您的工作或學校帳戶探索可供使用的資源，或者您可以直接指定資源所在的 URL，以免用戶端找不到資源。 訂閱工作區之後，您可以使用下列其中一種方法來啟動資源：

- 移至連線中心，然後按兩下資源來啟動它。
- 您也可以移至 [開始] 功能表並尋找具有工作區名稱的資料夾，或在搜尋列中輸入資源名稱。

### <a name="subscribe-with-a-user-account"></a>訂閱使用者帳戶

1. 在用戶端的主頁面上，選取 [**訂閱**]。
2. 出現提示時，使用您的使用者帳戶登入。
3. 資源將會出現在 [連線中心] 中，並依工作區分組。

### <a name="subscribe-with-a-url"></a>訂閱 URL

1. 從用戶端的主頁面中，選取 [**訂閱 URL**]。
2. 輸入工作區 URL 或您的電子郵件地址：
   - 如果您使用**工作區 URL**，請使用系統管理員提供的帳戶。 如果從 Windows 虛擬桌面存取資源，您可以使用下列其中一個 URL：
     - Windows 虛擬桌面 2019 年秋季版：`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Windows 虛擬桌面 2020 年春季版：`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
   - 如果您要改為使用 [**電子郵件**] 欄位，請輸入您的電子郵件地址。 如果您的系統管理員已設定[電子郵件探索](/windows-server/remote/remote-desktop-services/rds-email-discovery)，這會告訴用戶端搜尋與您的電子郵件地址相關聯的 URL。
3. 選取 [下一步]。
4. 出現提示時，使用您的使用者帳戶登入。
5. 資源應會出現在 [連線中心] 中（依工作區分組）。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用 Windows 桌面用戶端，請參閱[開始使用 Windows 桌面用戶端](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)。
