---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/06/2020
ms.author: alkohli
ms.openlocfilehash: b9ff5968b4bb406f1a96780985b5c6fe64ca976c
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505898"
---
| 連接埠號碼| 內或外 | 連接埠範圍| 必要| 備註 |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|位於|LAN|是|此埠是用來透過 HTTP 連接到資料箱 Blob 儲存體 REST Api。 如果未連線至 REST Api，則會自動重新導向至本機 web UI （8443）。 |
| TCP 443 (HTTPS)|位於|LAN|是|此埠是用來透過 HTTPS 連接到資料箱 Blob 儲存體 REST Api。 如果未連線至 REST Api，則會自動重新導向至本機 web UI （8443）。 |
| TCP 8443 (HTTPS-Alt) |位於|LAN|是|這是 HTTPS 的替代埠，可在連線至本機 web UI 進行裝置管理時使用。 |
| TCP 445 (SMB)|Out/In|LAN|在某些情況下<br>請參閱附註|只有當您透過 SMB 連線時，才需要此埠。 |
| TCP 2049 (NFS)|Out/In|LAN|在某些情況下<br>請參閱附註|只有當您透過 NFS 連線時，才需要此埠。 |
| TCP 111 (NFS) |Out/In|LAN|在某些情況下<br>請參閱附註|此埠僅適用于 rpcbind/埠對應，而且只有在透過 NFS 連接時才需要。  |
