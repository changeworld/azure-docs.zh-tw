---
title: VPN 閘道：排除 VPN 用戶端故障 - Azure AD 身份驗證
description: 故障排除 VPN 閘道 P2S Azure AD 身份驗證用戶端
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 8871e92f0911c4d3cbcc1772bef1daeb5c70b5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74151966"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>排除 Azure AD 身份驗證 VPN 用戶端的故障

本文可説明您使用點對點 VPN 和 Azure 活動目錄身份驗證對 VPN 用戶端進行故障排除以連接到虛擬網路。

## <a name="view-status-log"></a><a name="status"></a>查看狀態日誌

查看錯誤訊息的狀態日誌。

![記錄](./media/troubleshoot-ad-vpn-client/1.png)

1. 按一下用戶端視窗右下角的箭頭圖示以顯示**狀態日誌**。
2. 檢查日誌中可能存在指示問題的錯誤。
3. 錯誤訊息顯示為紅色。

## <a name="clear-sign-in-information"></a><a name="clear"></a>清除登錄資訊

清除登錄資訊。

![登入](./media/troubleshoot-ad-vpn-client/2.png)

1. 選擇... 要疑難排解的設定檔旁邊。 選擇**配置 ->清除已保存的帳戶**。
2. 選取 [儲存]****。
3. 嘗試連線。
4. 如果連接仍然失敗，請繼續下一節。

## <a name="run-diagnostics"></a><a name="diagnostics"></a>執行診斷

在 VPN 用戶端上運行診斷。

![診斷](./media/troubleshoot-ad-vpn-client/3.png)

1. 按一下 **...** 要運行診斷的設定檔旁邊。 選擇**診斷 -> 運行診斷**。
2. 用戶端將運行一系列測試並顯示測試結果

   * 互聯網接入 – 檢查用戶端是否具有互聯網連接
   * 用戶端憑據 - 檢查 Azure 活動目錄身份驗證終結點是否可以訪問
   * 伺服器可解析 - 聯繫 DNS 伺服器以解決配置的 VPN 伺服器的 IP 位址
   * 伺服器可訪問 - 檢查 VPN 伺服器是否正在回應
3. 如果任何測試失敗，請與網路系統管理員聯繫以解決此問題。
4. 下一節將介紹如何根據需要收集日誌。

## <a name="collect-client-log-files"></a><a name="logfiles"></a>收集用戶端日誌檔

收集 VPN 用戶端日誌檔。 日誌檔可以通過您選擇的方法發送給支援/管理員。 例如，電子郵件。

1. 按一下"..." 要運行診斷的設定檔旁邊。 選擇**診斷 -> 顯示日誌目錄**。

   ![顯示日誌](./media/troubleshoot-ad-vpn-client/4.png)
2. Windows 資源管理器將打開到包含日誌檔的資料夾。

   ![查看檔](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>後續步驟

有關詳細資訊，請參閱[為使用 Azure AD 身份驗證的 P2S 開放 VPN 連接創建 Azure 活動目錄租戶](openvpn-azure-ad-tenant.md)。