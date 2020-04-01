---
title: 排除遠端桌面客戶端 Windows 虛擬桌面 - Azure
description: 在 Windows 虛擬桌面租戶環境中設置用戶端連接時,如何解決問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 595762e6e8f22dddff30f1cff8c4bb79e89624b1
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473843"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>排除遠端桌面用戶端故障

本文介紹了遠端桌面用戶端的常見問題以及如何解決這些問題。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>適用於 Windows 7 或 Windows 10 的遠端桌面用戶端停止回應或無法開啟

從版本 1.2.790 開始,可以從"關於"頁或使用命令重置用戶數據。

使用以下命令刪除用戶數據、還原預設設置並取消訂閱所有工作區。

```cmd
msrdcw.exe /reset [/f]
```

如果您使用的是早期版本的遠端桌面用戶端,我們建議您卸載並重新安裝該用戶端。

## <a name="web-client-wont-open"></a>Web 客戶端無法開啟

首先,通過在瀏覽器中打開另一個網站來測試您的互聯網連接;例如[,www.bing.com](https://www.bing.com)。

使用**nslookup**確認 DNS 可以解析 FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

嘗試與其他用戶端(如 Windows 7 或 Windows 10 的遠端桌面用戶端)連接,並檢查是否可以打開 Web 用戶端。

### <a name="opening-another-site-fails"></a>開啟其他網站失敗

這通常是由網路連接問題或網路中斷引起的。 我們建議您聯繫網路支援。

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup 無法解析名稱

這通常是由網路連接問題或網路中斷引起的。 我們建議您聯繫網路支援。

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>用戶端無法連線,但網路上的其他用戶端可以連接

如果您的瀏覽器在使用 Web 用戶端時開始啟動或停止工作,請按照以下說明進行故障排除:

1. 重新啟動瀏覽器。
2. 清除瀏覽器 Cookie。 請參考[網頁資源管理員中的 Cookie 檔案](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
3. 清除瀏覽器快取。 請參考[瀏覽器的透明瀏覽器快取](https://binged.it/2RKyfdU)。
4. 在專用模式下打開瀏覽器。

## <a name="web-client-stops-responding-or-disconnects"></a>Web 用戶端停止回應或斷線連線

請嘗試使用其他瀏覽器或客戶端進行連接。

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>其他瀏覽器與客戶端也發生故障或無法開啟

如果問題即使在您切換瀏覽器后仍然存在,問題可能不是您的瀏覽器,而是您的網路。 我們建議您聯繫網路支援。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web 客戶端不斷提示認證

如果 Web 客戶端不斷提示認證,請按照以下說明操作:

1. 確認 Web 用戶端 URL 正確。
2. 確認您使用的認證適用於與 URL 關聯的 Windows 虛擬桌面環境。
3. 清除瀏覽器 Cookie。 有關詳細資訊,請參閱如何在[Internet 資源管理員中移除 Cookie 檔](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
4. 清除瀏覽器快取。 關於詳細資訊,請參閱[清除瀏覽器的瀏覽器快取](https://binged.it/2RKyfdU)。
5. 在專用模式下打開瀏覽器。

## <a name="next-steps"></a>後續步驟

- 有關 Windows 虛擬桌面和升級追蹤的故障排除概述,請參閱[故障排除概述、回饋和支援](troubleshoot-set-up-overview.md)。
- 要在 Windows 虛擬桌面環境中建立租戶和主機池時解決問題,請參閱[租戶和主機池創建](troubleshoot-set-up-issues.md)。
- 在 Windows 虛擬桌面中設定虛擬機器 (VM) 時解決問題,請參考[工作階段主機虛擬機器設定](troubleshoot-vm-configuration.md)。
- 要解決在 Windows 虛擬桌面中使用 PowerShell 時的問題,請參閱[Windows 虛擬桌面電源外殼](troubleshoot-powershell.md)。
- 要完成疑難解答教程,請參閱[教程:解決資源管理器範本部署的疑難解答](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。