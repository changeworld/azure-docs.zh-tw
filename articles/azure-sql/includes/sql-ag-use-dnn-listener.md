---
title: 針對 SQL Server VM 上的可用性群組，使用分散式網路名稱 (DNN) 接聽程式，而不是 VNN 接聽程式。
description: 用以將客戶重新導向至使用 DNN 接聽程式 (而非 VNN 接聽程式) 的訊息。
ms.topic: include
author: MashaMSFT
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4ea8428ae23537cde584bf6944732f65ebd2f26
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167982"
---
> [!NOTE]
> 在 Windows 2016 和更新版本上使用 SQL Server 2019 CU8 和更新版本的客戶，可改為使用[分散式網路名稱 (DNN) 接聽程式](../virtual-machines/windows/availability-group-distributed-network-name-dnn-listener-configure.md)取代傳統的 VNN 接聽程式和 Azure Load Balancer。 略過本文中建立接聽程式和負載平衡器的其餘步驟。
