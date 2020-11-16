---
title: 瞭解 Windows 虛擬桌面網路連線能力
titleSuffix: Azure
description: 瞭解 Windows 虛擬桌面網路連線能力
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 4c0017a36d84973a4d99c49a5ea33faeb189b35f
ms.sourcegitcommit: 18046170f21fa1e569a3be75267e791ca9eb67d0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/16/2020
ms.locfileid: "94639184"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>瞭解 Windows 虛擬桌面網路連線能力

Windows 虛擬桌面提供在 Azure 上執行的工作階段主機上裝載用戶端會話的能力。 Microsoft 會代表客戶管理部分的服務，並提供安全的端點來連接用戶端和工作階段主機。 下圖提供 Windows 虛擬桌面所使用網路連線的概要總覽

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Windows 虛擬桌面網路連接的圖表" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>工作階段連線

Windows 虛擬桌面使用遠端桌面通訊協定 (RDP) 來提供網路連線的遠端顯示和輸入功能。 RDP 一開始是隨 Windows NT 4.0 終端機伺服器版本發行，而且會隨著每個 Microsoft Windows 和 Windows Server 版本持續演進。 從一開始，RDP 開發成獨立于其基礎傳輸堆疊，且現今支援多種傳輸類型。

## <a name="reverse-connect-transport"></a>反向連接傳輸

Windows 虛擬桌面使用反向連接傳輸來建立遠端會話，以及用於攜帶 RDP 流量。 不同于內部部署遠端桌面服務部署，反向連接傳輸不會使用 TCP 接聽程式來接收連入的 RDP 連接。 相反地，它會透過 HTTPS 連線，對 Windows 虛擬桌面基礎結構使用輸出連線能力。

## <a name="session-host-communication-channel"></a>工作階段主機通道

在 Windows 虛擬桌面工作階段主機啟動時，遠端桌面代理程式載入器服務會建立 Windows 虛擬桌面代理程式的持續通道。 此通道是以安全傳輸層安全性為基礎， (TLS) 連線，並可作為工作階段主機與 Windows 虛擬桌面基礎結構之間服務訊息交換的匯流排。

## <a name="client-connection-sequence"></a>用戶端連接順序

用戶端連接順序如下所述：

1. 使用支援的 Windows 虛擬桌面用戶端使用者訂閱 Windows 虛擬桌面工作區
2. Azure Active Directory 驗證使用者，並傳回用來列舉使用者可用資源的權杖
3. 用戶端將權杖傳遞給 Windows 虛擬桌面摘要訂用帳戶服務
4. Windows 虛擬桌面摘要訂用帳戶服務驗證權杖
5. Windows 虛擬桌面摘要訂用帳戶服務會將可用的桌面清單和 Remoteapp 傳回用戶端，並以數位簽署的連線設定形式傳送回用戶端
6. 用戶端會將每個可用資源的連接設定儲存在一組 .rdp 檔案中。
7. 當使用者選取要連線的資源時，用戶端會使用相關聯的 .rdp 檔案並建立安全的 TLS 1.2 連線到最接近的 Windows 虛擬桌面閘道實例，並傳遞連接資訊
8. Windows 虛擬桌面閘道會驗證要求，並要求 Windows 虛擬桌面代理人協調連接
9. Windows 虛擬桌面訊息代理程式會識別工作階段主機，並使用先前建立的持續性通道來初始化連接
10. 遠端桌面堆疊會起始與用戶端所使用之相同 Windows 虛擬桌面閘道實例的 TLS 1.2 連線
11. 在用戶端和工作階段主機連線到閘道之後，閘道會開始轉送兩個端點之間的原始資料，這會建立 RDP 的基底反向連接傳輸
12. 設定基本傳輸之後，用戶端會啟動 RDP 交握

## <a name="connection-security"></a>連接安全性

TLS 1.2 用於從用戶端和工作階段主機起始的所有連線到 Windows 虛擬桌面基礎結構元件。
針對反向連接傳輸，用戶端和工作階段主機都會連接到 Windows 虛擬桌面閘道。 建立 TCP 連接之後，用戶端或工作階段主機會驗證 Windows 虛擬桌面閘道的憑證。
建立基底傳輸之後，RDP 會使用工作階段主機的憑證，在用戶端與會話主機之間建立嵌套的 TLS 連線。 根據預設，用於 RDP 加密的憑證是在部署期間由作業系統自行產生的。 如有需要，客戶可以部署企業憑證授權單位單位所發行的集中受控憑證。 如需設定憑證的詳細資訊，請參閱 [Windows Server 檔](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解 Windows 虛擬桌面的頻寬需求，請參閱 [瞭解遠端桌面通訊協定 (RDP) Windows 虛擬桌面的頻寬需求](rdp-bandwidth.md)。
* 若要開始使用適用于 Windows 虛擬桌面的服務品質 (QoS) ，請參閱 [為 Windows 虛擬桌面 (qos) 執行服務品質](rdp-quality-of-service-qos.md)。
