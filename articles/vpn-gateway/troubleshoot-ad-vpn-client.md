---
title: VPN 閘道：對 VPN 用戶端 Azure AD 驗證進行疑難排解
description: 針對 VPN 閘道 P2S Azure AD authentication 用戶端進行疑難排解
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 56a8514fc2531ba0b18925427814e5bfef7d64bf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84988100"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>針對 Azure AD 驗證 VPN 用戶端進行疑難排解

本文可協助您疑難排解 VPN 用戶端，以使用點對站 VPN 和 Azure Active Directory 驗證來連線到虛擬網路。

## <a name="view-status-log"></a><a name="status"></a>查看狀態記錄

查看錯誤訊息的狀態記錄檔。

![記錄](./media/troubleshoot-ad-vpn-client/1.png)

1. 按一下用戶端視窗右下角的箭號圖示，以顯示**狀態記錄**。
2. 請檢查記錄檔中是否有可能表示問題的錯誤。
3. 錯誤訊息會以紅色顯示。

## <a name="clear-sign-in-information"></a><a name="clear"></a>清除登入資訊

清除登入資訊。

![登入](./media/troubleshoot-ad-vpn-client/2.png)

1. 選取 [...] 在您要進行疑難排解的設定檔旁邊。 選取 [**設定-> 清除已儲存的帳戶**]。
2. 選取 [儲存]。
3. 嘗試連線。
4. 如果連接仍然失敗，請繼續下一節。

## <a name="run-diagnostics"></a><a name="diagnostics"></a>執行診斷

在 VPN 用戶端上執行診斷。

![診斷](./media/troubleshoot-ad-vpn-client/3.png)

1. 按一下 [ **...** ] 在您要執行診斷的設定檔旁邊。 選取 [**診斷-> 執行診斷**]。
2. 用戶端將會執行一系列的測試，並顯示測試的結果。

   * 網際網路存取–查看用戶端是否具有網際網路連線能力
   * 用戶端認證–查看是否可連線到 Azure Active Directory 驗證端點
   * 伺服器可解析–連絡人 DNS 伺服器，以解析已設定之 VPN 伺服器的 IP 位址
   * 伺服器可連線–檢查 VPN 伺服器是否已回應
3. 如果任何測試失敗，請洽詢您的網路系統管理員以解決此問題。
4. 下一節將說明如何收集記錄（如有需要）。

## <a name="collect-client-log-files"></a><a name="logfiles"></a>收集用戶端記錄檔

收集 VPN 用戶端記錄檔。 您可以透過選擇的方法，將記錄檔傳送給支援/系統管理員。 例如，電子郵件。

1. 按一下 [...] 在您要執行診斷的設定檔旁邊。 選取 [**診斷-> 顯示記錄目錄**]。

   ![顯示記錄](./media/troubleshoot-ad-vpn-client/4.png)
2. Windows Explorer 會開啟至包含記錄檔的資料夾。

   ![view file](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[為使用 Azure AD authentication 的 P2S OPEN VPN 連線建立 Azure Active Directory 租](openvpn-azure-ad-tenant.md)使用者。