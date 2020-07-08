---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2689e81b089147dfc913fb119e0a499d60574b60
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050404"
---
在此步驟中，您會使用在相同網路上執行的用戶端應用程式來測試可用性群組接聽程式。

用戶端連線能力具有下列需求：

* 對接聽程式的用戶端連線必須來自位於與裝載 AlwaysOn 可用性複本的機器不同雲端服務的機器。
* 如果 Always On 複本位於其他子網路中，用戶端必須在連接字串中指定 *MultisubnetFailover=True* 。 此狀況會導致對各種子網路中的複本進行平行連線嘗試。 此案例包含跨區域的 Always On 可用性群組部署。

其中一個範例是從相同 Azure 虛擬網路中的其中一部 VM (但不是裝載複本的 VM) 連線到接聽程式。 完成這項測試的簡單方法是嘗試將 SQL Server Management Studio 連線到可用性群組接聽程式。 另一個簡單的方法是執行[SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx)，如下所示：

```console
sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15
```

> [!NOTE]
> 如果 EndpointPort 值為 1433**，則不需要在呼叫中指定。 前一個呼叫也假設用戶端電腦已加入相同的網域，而且已使用 Windows 驗證授與呼叫端對資料庫的權限。
> 
> 

測試接聽程式時，請務必容錯移轉可用性群組，以確定用戶端可以跨容錯移轉連線至接聽程式。

