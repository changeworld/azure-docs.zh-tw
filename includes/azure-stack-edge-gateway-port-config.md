---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/16/2019
ms.author: alkohli
ms.openlocfilehash: baf18ae0263215e6ff83570557255d06c3117fd4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89083382"
---
| 連接埠號碼| 內或外 | 連接埠範圍| 必要|   注意 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|外|WAN |否|輸出連接埠用於網際網路存取以擷取更新。 <br>輸出 Web Proxy 可由使用者設定。 |
| TCP 443 (HTTPS)|外|WAN|是|輸出連接埠用來存取雲端中的資料。<br>輸出 Web Proxy 可由使用者設定。|
| UDP 123 (NTP)|外|WAN|在某些情況下<br>請參閱附註|只有當您使用網際網路型 NTP 伺服器時，才需要此連接埠。  |   
| UDP 53 (DNS)|外|WAN|在某些情況下<br>請參閱附註|只有當您使用網際網路型 DNS 伺服器時，才需要此連接埠。<br>我們建議使用本機 DNS 伺服器。 |
| TCP 5985 (WinRM)|Out/In|LAN|在某些情況下<br>請參閱附註|需要此連接埠才能透過 HTTP 上的遠端 PowerShell 連線到裝置。  |
| UDP 67 (DHCP)|外|LAN|在某些情況下<br>請參閱附註|只有當您使用本機 DHCP 伺服器時，才需要此連接埠。  |
| TCP 80 (HTTP)|Out/In|LAN|是|此連接埠是裝置上用於本機管理本機 UI 的輸入連接埠。 <br>透過 HTTP 存取本機 UI 會自動重新導向至 HTTPS。  |
| TCP 443 (HTTPS)|Out/In|LAN|是|此連接埠是裝置上用於本機管理本機 UI 的輸入連接埠。 此埠也用來將 Azure Resource Manager 連接到裝置本機 Api、透過 REST Api 連接 Blob 儲存體，以及將 (STS) 的安全性權杖服務，透過存取和重新整理權杖進行驗證。|
| TCP 445 (SMB)|位於|LAN|在某些情況下<br>請參閱附註|只有當您透過 SMB 連線時，才需要此連接埠。 |
| TCP 2049 (NFS)|位於|LAN|在某些情況下<br>請參閱附註|只有當您透過 NFS 連線時，才需要此連接埠。 |


