---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 71460af42b4da97a578ae5a3e23a714577e71867
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86218403"
---
| 連接埠號碼| 內或外 | 連接埠範圍| 必要|   附註 |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|外|WAN |否|輸出連接埠用於網際網路存取以擷取更新。 <br>輸出 Web Proxy 可由使用者設定。 |
| TCP 443 (HTTPS)|外|WAN|是|輸出連接埠用來存取雲端中的資料。<br>輸出 Web Proxy 可由使用者設定。|
| UDP 123 (NTP)|外|WAN|在某些情況下<br>請參閱附註|只有當您使用網際網路型 NTP 伺服器時，才需要此連接埠。  |   
| UDP 53 (DNS)|外|WAN|在某些情況下<br>請參閱附註|只有當您使用網際網路型 DNS 伺服器時，才需要此連接埠。<br>我們建議使用本機 DNS 伺服器。 |
| TCP 5985 (WinRM)|Out/In|LAN|在某些情況下<br>請參閱附註|需要此連接埠才能透過 HTTP 上的遠端 PowerShell 連線到裝置。  |
| UDP 67 (DHCP)|外|LAN|在某些情況下<br>請參閱附註|只有當您使用本機 DHCP 伺服器時，才需要此連接埠。  |
| TCP 80 (HTTP)|Out/In|LAN|是|此連接埠是裝置上用於本機管理本機 UI 的輸入連接埠。 <br>透過 HTTP 存取本機 UI 會自動重新導向至 HTTPS。  |
| TCP 443 (HTTPS)|Out/In|LAN|是|此連接埠是裝置上用於本機管理本機 UI 的輸入連接埠。 |
| TCP 445 (SMB)|位於|LAN|在某些情況下<br>請參閱附註|只有當您透過 SMB 連線時，才需要此連接埠。 |
| TCP 2049 (NFS)|位於|LAN|在某些情況下<br>請參閱附註|只有當您透過 NFS 連線時，才需要此連接埠。 |
