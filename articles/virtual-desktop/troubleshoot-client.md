---
title: 疑難排解遠端桌面用戶端 Windows 虛擬桌面-Azure
description: 如何解決在 Windows 虛擬桌面租用戶環境中設定用戶端連線時的問題。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 097c97d16cf62793d03ac42662267e0553383bc1
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539615"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>疑難排解遠端桌面用戶端

本文描述遠端桌面用戶端的常見問題，以及如何修正這些問題。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Windows 7 或 Windows 10 的遠端桌面用戶端停止回應或無法開啟

從版本1.2.790 開始，您可以從 [關於] 頁面或使用命令重設使用者資料。

使用下列命令來移除您的使用者資料、還原預設設定，以及取消訂閱所有工作區。

```cmd
msrdcw.exe /reset [/f]
```

如果您使用的是較舊版本的遠端桌面用戶端，建議您卸載並重新安裝用戶端。

## <a name="web-client-wont-open"></a>Web 用戶端將不會開啟

首先，請在您的瀏覽器中開啟另一個網站來測試您的網際網路連線;例如， [www.bing.com](https://www.bing.com)。

使用 **nslookup** 確認 DNS 可以解析 FQDN：

```cmd
nslookup rdweb.wvd.microsoft.com
```

嘗試與其他用戶端（例如 Windows 7 或 Windows 10 的遠端桌面用戶端）連線，並查看是否可以開啟 web 用戶端。

### <a name="cant-open-other-websites-while-connected-to-the-web-client"></a>連線至 web 用戶端時無法開啟其他網站

如果您在連線至 web 用戶端時無法開啟其他網站，可能是網路連線問題或網路中斷。 建議您洽詢網路支援。

### <a name="nslookup-cant-resolve-the-name"></a>Nslookup 無法解析名稱

如果 nslookup 無法解析名稱，則可能是網路連線問題或網路中斷。 建議您洽詢網路支援。

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>您的用戶端無法連線，但您網路上的其他用戶端可以連線到

當您使用 web 用戶端時，如果您的瀏覽器開始作用或停止運作，請依照下列指示進行疑難排解：

1. 重新啟動瀏覽器。
2. 清除瀏覽器 cookie。 請參閱 [如何在 Internet Explorer 中刪除 cookie](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)檔。
3. 清除瀏覽器快取。 請參閱 [瀏覽器的清除瀏覽器](https://binged.it/2RKyfdU)快取。
4. 以私用模式開啟瀏覽器。

## <a name="client-doesnt-show-my-resources"></a>用戶端不會顯示我的資源

首先，檢查您要使用的 Azure Active Directory 帳戶。 如果您已使用不同於您想要用於 Windows 虛擬桌面的 Azure Active Directory 帳戶登入，請登出或使用私人瀏覽器視窗。

如果您是使用 Windows 虛擬桌面 (傳統) ，請使用本文中的 web 用戶端連結來連線至 [您的資源](./virtual-desktop-fall-2019/connect-web-2019.md) 。

如果無法運作，請確定您的應用程式群組與工作區相關聯。

## <a name="web-client-stops-responding-or-disconnects"></a>Web 用戶端停止回應或中斷連線

請嘗試使用另一個瀏覽器或用戶端進行連接。

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>其他瀏覽器和用戶端也無法正常運作或無法開啟

如果您在切換瀏覽器之後仍繼續發生問題，則問題可能不在您的瀏覽器中，而是在您的網路中。 建議您洽詢網路支援。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web 用戶端會持續提示輸入認證

如果 Web 用戶端持續提示輸入認證，請依照下列指示進行：

1. 確認網頁用戶端 URL 正確無誤。
2. 確認您所使用的認證適用于與 URL 系結的 Windows 虛擬桌面環境。
3. 清除瀏覽器 cookie。 如需詳細資訊，請參閱 [如何在 Internet Explorer 中刪除 cookie](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)檔。
4. 清除瀏覽器快取。 如需詳細資訊，請參閱清除瀏覽器 [的瀏覽器](https://binged.it/2RKyfdU)快取。
5. 以私用模式開啟瀏覽器。

## <a name="windows-client-blocks-windows-virtual-desktop-classic-feed"></a>Windows 用戶端會封鎖 Windows 虛擬桌面 (傳統) 摘要

如果 Windows 用戶端摘要不會顯示 Windows 虛擬桌面 (傳統) 應用程式，請遵循下列指示：

1. 檢查條件式存取原則是否包含與 Windows 虛擬桌面 (傳統) 相關聯的應用程式識別碼。
2. 檢查條件式存取原則是否封鎖除了 Windows 虛擬桌面 (傳統) 應用程式識別碼以外的所有存取。 若是如此，您必須將應用程式識別碼 **9cdead84-a844-4324-93f2-b2e6bb768d07** 新增至原則，以允許用戶端探索摘要。

如果您在清單中找不到 [應用程式識別碼] 9cdead84-a844-4324-93f2-b2e6bb768d07，您將需要註冊 Windows 虛擬桌面資源提供者。 註冊資源提供者：

1. 登入 Azure 入口網站。
2. 移至 [ **訂** 用帳戶]，然後選取您的訂用帳戶。
3. 在頁面左側的功能表中，選取 [ **資源提供者**]。
4. 尋找並選取 [ **DesktopVirtualization**]，然後選取 [ **重新註冊**]。

## <a name="next-steps"></a>後續步驟

- 如需 Windows 虛擬桌面疑難排解和擴大追蹤的概觀，請參閱[疑難排解概觀、意見反應和支援](troubleshoot-set-up-overview.md)。
- 若要針對在 Windows 虛擬桌面環境中建立 Windows 虛擬桌面環境和主機集區時的問題進行疑難排解，請參閱[環境和主機集區建立](troubleshoot-set-up-issues.md)。
- 若要針對在 Windows 虛擬桌面中設定虛擬機器 (VM) 時的問題進行疑難排解，請參閱[工作階段主機虛擬機器設定](troubleshoot-vm-configuration.md)。
- 若要針對與 Windows 虛擬桌面代理程式或會話連線相關的問題進行疑難排解，請參閱針對 [常見的 Windows 虛擬桌面代理程式問題進行疑難排解](troubleshoot-agent.md)。
- 若要針對使用 PowerShell 搭配 Windows 虛擬桌面時的問題進行疑難排解，請參閱 [Windows 虛擬桌面 PowerShell](troubleshoot-powershell.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
