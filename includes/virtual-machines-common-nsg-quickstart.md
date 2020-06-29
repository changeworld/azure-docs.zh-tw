---
title: 包含檔案
description: 包含檔案
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2020
ms.locfileid: "67173949"
---
您可以透過在子網路或 VM 網路介面上建立網路篩選，對 Azure 中的虛擬機器 (VM) 開啟連接埠或建立端點。 您可將控制輸入與輸出流量的這些篩選器放在可接收流量的資源所附加的網路安全性群組上。

此文章中的範例示範如何建立會使用標準 TCP 連接埠 80 的網路篩選器 (假設您已經在 VM 上啟動適當的服務並開啟任何 OS 防火牆規則)。

建立已設定為在標準 TCP 連接埠 80 上接聽要求的 VM 之後，您可以：

1. 建立網路安全性群組。

2. 建立可允許流的連入安全性規則，並為下列設定指派值：

   - **目的地連接埠範圍**：80

   - **來源連接埠範圍**: * (允許任何來源連接埠)

   - **優先順序值**：輸入小於 65,500 且優先順序高於預設捕捉所有流量拒絕連入規則的值。

3. 讓網路安全性群組與 VM 網路介面或子網路產生關聯。

雖然此範例使用簡單的規則來允許 HTTP 流量，您也可以使用網路安全性群組與規則來建立更複雜的網路設定。 




