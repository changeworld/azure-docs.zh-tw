---
title: 針對連線問題進行疑難排解-適用於 MySQL 的 Azure 資料庫彈性的伺服器
description: 瞭解如何對適用於 MySQL 的 Azure 資料庫彈性伺服器的連接問題進行疑難排解。
keywords: mysql 連接，連接字串，連線問題，持續性錯誤，連接錯誤
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: b328da01cd3b2ecb3eb5f183296eab808de27399
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933896"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>針對適用於 MySQL 的 Azure 資料庫-彈性伺服器的連接問題進行疑難排解

> [!IMPORTANT]
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

連線問題可能由各種因素造成，包括：

* 防火牆設定
* 連線逾時
* 不正確的登入資訊
* 在某些適用於 MySQL 的 Azure 資料庫彈性的伺服器資源上達到上限

在本文中，我們將討論如何疑難排解一些常見的錯誤，以及解決這些錯誤的步驟。

## <a name="troubleshoot-common-errors"></a>對常見錯誤進行疑難排解

如果應用程式持續無法連線到適用於 MySQL 的 Azure 資料庫彈性的伺服器，通常會指出下列其中一項問題：

* 使用 TLS/SSL 的加密連線：彈性伺服器僅支援使用傳輸層安全性 (TLS 1.2) 的加密連線，以及 **tls 1.0 和 tls 1.1 的所有連入連線都會遭到拒絕**。 您無法停用或變更 TLS 版本。 [在適用於 MySQL 的 Azure 資料庫彈性的伺服器中，深入瞭解如何使用傳輸層安全性 (TLS 1.2) 加密的連接](./how-to-connect-tls-ssl.md)。
- *私人存取 (VNet 整合) *中有彈性的伺服器：請確定您是從與彈性伺服器相同的虛擬網路內連接。 請參閱 [適用於 MySQL 的 Azure 資料庫彈性伺服器中的虛擬網路]<!--(./concepts-networking-virtual-network.md)-->
- 具有 *公用存取權的彈性伺服器 (允許的 IP 位址) *，請確定已將防火牆設定為允許來自您用戶端的連接。 請參閱 [使用 Azure 入口網站建立和管理彈性的伺服器防火牆規則](./how-to-manage-firewall-portal.md)。
* 用戶端防火牆設定：用戶端上的防火牆必須允許連線至您的資料庫伺服器。 也必須允許您無法連到的伺服器 IP 位址和連接埠，在某些防火牆中，還要允許應用程式名稱，例如 MySQL。
* 使用者錯誤：您可能在連接字串中輸入錯誤的連接參數，例如伺服器名稱。

### <a name="resolve-connectivity-issues"></a>解決連線問題

* 若要深入瞭解加密的連接，請參閱 [使用傳輸層安全性 (TLS 1.2 > 適用於 MySQL 的 Azure 資料庫) 的加密](./how-to-connect-tls-ssl.md) 連線。
* 如果您使用 **公用存取 (允許的 ip 位址) **，請將 [防火牆規則](./how-to-manage-firewall-portal.md) 設定為允許用戶端 ip 位址。 (僅適用於臨時性的測試目的) 請使用 0.0.0.0 作為起始 IP 位址並使用 255.255.255.255 作為結束 IP 位址來設定防火牆規則。 這樣會開放伺服器供所有 IP 位址存取。 若這樣可解決您的連線問題，請移除此規則並針對已適當限制的 IP 位址或位址範圍建立防火牆規則。
* 在用戶端與網際網路之間的所有防火牆上，確定開放連接埠 3306 供輸出連線使用。
* 請確認您的連接字串和其他連線設定。 請參閱 Azure 入口網站中適用于您的伺服器的 [ **連接字串** ] 頁面中的預先定義連接字串（適用于通用語言的）。

## <a name="next-steps"></a>下一步
- [使用 MySQL 工作臺在適用於 MySQL 的 Azure 資料庫彈性的伺服器中連接及查詢資料](./connect-workbench.md)。
- [使用 PHP 在適用於 MySQL 的 Azure 資料庫彈性的伺服器中連接和查詢資料](./connect-php.md)。
- [使用 Python 在適用於 MySQL 的 Azure 資料庫彈性的伺服器中連接和查詢資料](./connect-python.md)。
