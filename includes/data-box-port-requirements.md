---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 948f5399ceb931d701341c2cf378482ede94979d
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200281"
---
| 連接埠號碼| 內或外 | 連接埠範圍| 必要| 附註 |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|位於|LAN|是|此埠是用來透過 HTTP 連線至資料箱的 Blog 儲存體 REST Api。 如果未連接到 REST Api，這會自動重新導向至本機 web UI （8443）。 |
| TCP 443 (HTTPS)|位於|LAN|是|此埠是用來透過 HTTPS 連接到資料箱的 Blog 儲存體 REST Api。 如果未連接到 REST Api，這會自動重新導向至本機 web UI （8443）。 |
| TCP 8443 (HTTPS-Alt) |位於|LAN|是|這是 HTTPS 的替代埠，會在連線到本機 web UI 以進行裝置管理時使用。 |
| TCP 445 (SMB)|Out/In|LAN|在某些情況下<br>請參閱附註|只有當您透過 SMB 連線時，才需要此埠。 |
| TCP 2049 (NFS)|Out/In|LAN|在某些情況下<br>請參閱附註|只有當您透過 NFS 連線時，才需要此埠。 |
| TCP 111 (NFS) |Out/In|LAN|在某些情況下<br>請參閱附註|此埠僅用於 rpcbind/埠對應，而且只有在透過 NFS 連線時才需要。  |
