---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67839750"
---
| 連接埠號碼| 內或外 | 連接埠範圍| 必要| 注意 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|在|LAN|是|此埠用於通過 HTTP 連接到資料盒博客存儲 REST API。 如果未連接到 REST API，則此 API 會自動重定向到 8443 的本地 Web UI。 |
| TCP 443 (HTTPS)|在|LAN|是|此埠用於通過 HTTPS 連接到資料盒博客存儲 REST API。 如果未連接到 REST API，則此 API 會自動重定向到 8443 的本地 Web UI。 |
| TCP 8443 （HTTPS-Alt）|在|LAN|是|這是 HTTPS 的替代埠，用於連接到本地 Web UI 以進行裝置管理。 |
| TCP 445 (SMB)|Out/In|LAN|在某些情況下<br>請參閱附註|僅當通過 SMB 連接時，才需要此埠。 |
| TCP 2049 (NFS)|Out/In|LAN|在某些情況下<br>請參閱附註|僅當通過 NFS 進行連接時，才需要此埠。 |
| TCP 111 （NFS）|Out/In|LAN|在某些情況下<br>請參閱附註|此埠用於 rpcbind/埠映射，並且僅在通過 NFS 進行連接時才需要。  |
