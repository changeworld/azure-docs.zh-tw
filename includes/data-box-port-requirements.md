---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67839750"
---
| 連接埠號碼| 內或外 | 連接埠範圍| 必要| 備註 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|位於|LAN|Yes|此埠是用來透過 HTTP 連線至資料箱的 Blog 儲存體 REST Api。 如果未連接到 REST Api，這會自動重新導向至本機 web UI （8443）。 |
| TCP 443 (HTTPS)|位於|LAN|Yes|此埠是用來透過 HTTPS 連接到資料箱的 Blog 儲存體 REST Api。 如果未連接到 REST Api，這會自動重新導向至本機 web UI （8443）。 |
| TCP 8443 （HTTPS-Alt）|位於|LAN|Yes|這是 HTTPS 的替代埠，會在連線到本機 web UI 以進行裝置管理時使用。 |
| TCP 445 (SMB)|Out/In|LAN|在某些情況下<br>請參閱附註|只有當您透過 SMB 連線時，才需要此埠。 |
| TCP 2049 (NFS)|Out/In|LAN|在某些情況下<br>請參閱附註|只有當您透過 NFS 連線時，才需要此埠。 |
| TCP 111 （NFS）|Out/In|LAN|在某些情況下<br>請參閱附註|此埠僅用於 rpcbind/埠對應，而且只有在透過 NFS 連線時才需要。  |
