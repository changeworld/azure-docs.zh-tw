---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67173815"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>修改區域網路閘道 IP 位址首碼 - 沒有閘道連線

#### <a name="to-add-additional-address-prefixes"></a>若要新增其他位址首碼：

1. 在 [區域網路閘道] 資源的 [設定]**** 區段中，按一下 [組態]****。
2. 在 [新增其他位址範圍]** 方塊中新增 IP 位址空間。
3. 按一下 [Save] **** 儲存您的設定。

#### <a name="to-remove-address-prefixes"></a>若要移除位址首碼：

1. 在 [區域網路閘道] 資源的 [設定]**** 區段中，按一下 [組態]****。
2. 按一下包含您要移除之首碼的那一行上的 [...]****。
3. 按一下 [移除]****。
4. 按一下 [Save] **** 儲存您的設定。

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>修改區域網路閘道 IP 位址首碼 - 現有閘道連線

如果您有閘道連線並想要新增或移除包含在區域網路閘道中的 IP 位址首碼，您必須依序執行下列步驟。 這會導致您 VPN 連線的停機時間。 修改 IP 位址首碼時，您不需要刪除 VPN 閘道。 您只需要移除連線。

#### <a name="1-remove-the-connection"></a>1. 卸下連接。

1. 在 [區域網路閘道] 資源的 [設定]**** 區段中，按一下 [連線]****。
2. 按一下每個連線那一行上的 [...]****，然後按一下 [刪除]****。
3. 按一下 [Save] **** 儲存您的設定。

#### <a name="2-modify-the-address-prefixes"></a>2. 修改位址首碼。

若要新增其他位址首碼：

1. 在 [區域網路閘道] 資源的 [設定]**** 區段中，按一下 [組態]****。
2. 新增 IP 位址空間。
3. 按一下 [Save] **** 儲存您的設定。

若要移除位址首碼：

1. 在 [區域網路閘道] 資源的 [設定]**** 區段中，按一下 [組態]****。
2. 按一下包含要刪除的首碼的行上的 **...**
3. 按一下 [移除]****。
4. 按一下 [Save] **** 儲存您的設定。

#### <a name="3-recreate-the-connection"></a>3. 重新創建連接。

1. 瀏覽至您 VNet 的虛擬網路閘道。 (而非區域網路閘道。)
2. 在 [虛擬網路閘道] 的 [設定]**** 區段中，按一下 [連線]****。
3. 按一下 [+新增]**** 以開啟 [新增連線]**** 刀鋒視窗。
4. 重新建立您的連線。
5. 按一下 [確定]**** 來建立連線。