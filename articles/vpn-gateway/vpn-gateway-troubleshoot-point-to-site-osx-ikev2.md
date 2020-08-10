---
title: Azure VPN 閘道：對點對站連線進行疑難排解： Mac OS X 用戶端
description: 瞭解如何使用原生 VPN 用戶端和 IKEv2，針對來自 Mac OS X 的點對站連線問題進行疑難排解。
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: 58250980081388b78ebb32fb01b84beb8d86c1c2
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88030654"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>針對來自 Mac OS X VPN 用戶端的點對站 VPN 連線問題進行疑難排解

本文將使用原生 VPN 用戶端和 IKEv2 來協助您針對來自 Mac OS X 的「點對站」連線問題進行疑難排解。 Mac 中 IKEv2 的 VPN 用戶端非常基本，不允許太多的自訂。 只有四個設定需要檢查：

* 伺服器位址
* 遠端識別碼
* 本機識別碼
* 驗證設定
* OS 版本 (10.11 或更新版本)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a> 針對憑證型驗證問題進行疑難排解
1. 檢查 VPN 用戶端設定。 按 Command 鍵 + Shift 鍵以移至 [網路設定]****，然後輸入 "VPN" 以檢查 VPN 用戶端設定。 從清單中，按一下需要調查的 VPN 項目。

   ![IKEv2 憑證型驗證](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. 確認 [伺服器位址]**** 是完整的 FQDN 且包含 cloudapp.net。
3. [遠端識別碼]**** 應該與「伺服器位址」(閘道 FQDN) 相同。
4. [本機識別碼]**** 應該與用戶端憑證的**主體**相同。
5. 按一下 [認證設定]**** 以開啟 [認證設定] 頁面。

   ![驗證設定](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. 確認已從下拉式清單中選取 [憑證]****。
7. 按一下 [選取]**** 按鈕，並確認已選取正確的憑證。 按一下 [好]**** 以儲存所有變更。

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>針對使用者名稱和密碼驗證問題進行疑難排解

1. 檢查 VPN 用戶端設定。 按 Command 鍵 + Shift 鍵以移至 [網路設定]****，然後輸入 "VPN" 以檢查 VPN 用戶端設定。 從清單中，按一下需要調查的 VPN 項目。

   ![IKEv2 使用者名稱密碼](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. 確認 [伺服器位址]**** 是完整的 FQDN 且包含 cloudapp.net。
3. [遠端識別碼]**** 應該與「伺服器位址」(閘道 FQDN) 相同。
4. [本機識別碼]**** 可以空白。
5. 按一下 [認證設定]**** 按鈕，然後確認已從下拉式清單中選取 [使用者名稱]。

   ![驗證設定](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. 確認所輸入的認證正確。

## <a name="additional-steps"></a><a name="additional"></a>其他步驟

如果您嘗試上述步驟且所有項目都設定正確，請下載 [Wireshark](https://www.wireshark.org/#download) 並執行封包擷取。

1. 依據 isakmp** 進行篩選，然後查看 **IKE_SA** 封包。 您應該能夠在 [Payload: Security Association] \(承載: 安全性關聯\)**** 底下查看 SA 提案詳細資料。 
2. 確認用戶端和伺服器有一個通用集合。

   ![封包](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. 如果網路追蹤沒有伺服器回應，請確認您已在 Azure 入口網站網站的 [Azure 閘道設定] 頁面上啟用 IKEv2 通訊協定。

## <a name="next-steps"></a>後續步驟
如需其他協助，請參閱 [Microsoft 支援服務](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
