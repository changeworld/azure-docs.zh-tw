---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 897e36a6c5165549d7809512d0298fa2cfed2fa8
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90606534"
---
1. 選取 [連線到 VPN 網站]  ，以開啟 [連線網站]  頁面。

    ![螢幕擷取畫面顯示虛擬中樞的 [已連線的網站] 窗格，適用於預先共用金鑰和相關聯的設定。](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "連線")

   完成以下欄位：

   * 輸入預先共用金鑰。 如果您未輸入金鑰，Azure 會為您會自動產生一個金鑰。
   * 選取 [通訊協定] 和 [IPsec 設定]。 請參閱 [預設/自訂 IPSec 詳細資料] (https://docs.microsoft.com/azure/virtual-wan/virtual-wan-ipsec)
   * 為 [傳播預設路由]  選取適當選項。 [啟用]  選項允許虛擬中樞將學習預設路由傳播至此連線。 此旗標只會在虛擬 WAN 中樞已經學習預設路由時，將預設路由傳播到連線，因為在中樞中部署防火牆或其他連線的網站已啟用強制通道。 預設路由不是來自虛擬 WAN 中樞。

2. 選取 [連接]  。
3. 幾分鐘後，網站將會顯示連線和連線狀態。

   ![螢幕擷取畫面顯示 VPN 站對站連線和連線能力狀態。](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **連線狀態：** 這是針對 VPN 網站連線至 Azure 中樞 VPN 閘道的連線，在 Azure 資源的狀態。 一旦此控制平面作業成功，Azure VPN 閘道和內部部署 VPN 裝置將繼續建立連線。

   **連線狀態：** 這是在中樞和 VPN 網站中，Azure VPN 閘道之間的實際連線 (資料路徑) 狀態。 可能顯示下列其中任何一個狀態：

    * **未知**：如果後端系統正在轉換成另一個狀態，通常會看到此狀態。
    * **正在連線**：Azure VPN 閘道正嘗試與實際的內部部署 VPN 網站連線。
    * **已連線**：Azure VPN 閘道與內部部署 VPN 網站之間已建立連線。
    * **已中斷連線**：如果基於任何原因 (內部部署或在 Azure 中)，已中斷連線，則會顯示此狀態。
4. 在中樞 VPN 網站內，您可以另外執行下列動作： 

   * 編輯或刪除 VPN 連線。
   * 在 Azure 入口網站中刪除網站。
   * 使用網站旁的內容 (...) 功能表，下載分支專屬的設定，以取得 Azure 端的詳細資料。 如果您想要下載中樞內所有連線網站的設定，請選取頂端功能表的 [下載 VPN 設定]  。
