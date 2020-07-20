---
title: 針對遠端桌面用戶端 Windows 虛擬桌面進行疑難排解-Azure
description: 如何解決在 Windows 虛擬桌面租用戶環境中設定用戶端連線時的問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76348a0aee48cc6d768e8233e376f266eca31917
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83005994"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>針對遠端桌面用戶端進行疑難排解

本文說明遠端桌面用戶端的常見問題，以及如何修正這些問題。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>適用于 Windows 7 或 Windows 10 的遠端桌面用戶端停止回應或無法開啟

從版本1.2.790 開始，您可以從 [關於] 頁面或使用命令來重設使用者資料。

使用下列命令來移除您的使用者資料、還原預設設定，並取消訂閱所有工作區。

```cmd
msrdcw.exe /reset [/f]
```

如果您使用的是舊版的遠端桌面用戶端，我們建議您卸載並重新安裝用戶端。

## <a name="web-client-wont-open"></a>Web 用戶端不會開啟

首先，在瀏覽器中開啟另一個網站，以測試您的網際網路連線;例如， [www.bing.com](https://www.bing.com)。

使用**nslookup**確認 DNS 可以解析 FQDN：

```cmd
nslookup rdweb.wvd.microsoft.com
```

請嘗試與其他用戶端連線，例如適用于 Windows 7 或 Windows 10 的遠端桌面用戶端，並檢查您是否可以開啟網頁用戶端。

### <a name="opening-another-site-fails"></a>開啟另一個網站失敗

這通常是因網路連線問題或網路中斷所造成。 我們建議您洽詢網路支援。

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup 無法解析名稱

這通常是因網路連線問題或網路中斷所造成。 我們建議您洽詢網路支援。

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>您的用戶端無法連線，但您網路上的其他用戶端可以連接

如果您的瀏覽器在使用 web 用戶端時開始作用或停止運作，請遵循下列指示進行疑難排解：

1. 重新啟動瀏覽器。
2. 清除瀏覽器 cookie。 請參閱[如何刪除 Internet Explorer 中的 cookie](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)檔案。
3. 清除瀏覽器快取。 請參閱[清除瀏覽器的瀏覽器](https://binged.it/2RKyfdU)快取。
4. 以私用模式開啟瀏覽器。

## <a name="web-client-does-not-show-my-resources"></a>Web 用戶端不會顯示我的資源

首先，請檢查您所使用的 Azure Active Directory 帳戶。 如果您已使用 Azure Active Directory 不同于 Winodws 虛擬桌面的帳戶登入，您應該簽署或使用私用瀏覽器視窗。

如果您使用 Windows 虛擬桌面秋季2019版，請使用本文中的 web 用戶端連結來[連線到您](./virtual-desktop-fall-2019/connect-web-2019.md)的資源。

## <a name="web-client-stops-responding-or-disconnects"></a>Web 用戶端停止回應或中斷連線

嘗試使用另一個瀏覽器或用戶端進行連接。

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>其他瀏覽器和用戶端也會故障或無法開啟

如果即使在切換瀏覽器之後仍然發生問題，問題可能不在您的瀏覽器中，而是在您的網路上。 我們建議您洽詢網路支援。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web 用戶端會持續提示輸入認證

如果 Web 用戶端持續提示輸入認證，請遵循下列指示：

1. 確認 web 用戶端 URL 是否正確。
2. 確認您所使用的認證適用于與 URL 系結的 Windows 虛擬桌面環境。
3. 清除瀏覽器 cookie。 如需詳細資訊，請參閱[如何刪除 Internet Explorer 中的 cookie](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)檔案。
4. 清除瀏覽器快取。 如需詳細資訊，請參閱[清除瀏覽器的瀏覽器](https://binged.it/2RKyfdU)快取。
5. 以私用模式開啟瀏覽器。

## <a name="next-steps"></a>後續步驟

- 如需 Windows 虛擬桌面疑難排解和擴大追蹤的概觀，請參閱[疑難排解概觀、意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對在 Windows 虛擬桌面環境中建立 Windows 虛擬桌面環境和主機集區時的問題進行疑難排解，請參閱[環境和主機集區建立](troubleshoot-set-up-issues.md)。
- 若要針對在 Windows 虛擬桌面中設定虛擬機器 (VM) 時的問題進行疑難排解，請參閱[工作階段主機虛擬機器設定](troubleshoot-vm-configuration.md)。
- 若要針對使用 PowerShell 搭配 Windows 虛擬桌面時的問題進行疑難排解，請參閱 [Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
