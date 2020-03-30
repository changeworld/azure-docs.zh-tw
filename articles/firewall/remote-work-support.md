---
title: Azure 防火牆遠端工作支援
description: 本文介紹 Azure 防火牆如何支援遠端工作力需求。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289637"
---
# <a name="azure-firewall-remote-work-support"></a>Azure 防火牆遠端工作支援

Azure 防火牆是一種託管的基於雲的網路安全服務，可保護 Azure 虛擬網路資源。 它是完全具狀態的防火牆即服務，具有內建的高可用性和不受限制的雲端延展性。 

## <a name="firewall-rules"></a>防火牆規則

您可以使用 Azure 防火牆[DNAT 規則](rule-processing.md)使用 Azure 防火牆保護虛擬桌面基礎結構 （VDI） 入站 RDP 對 Azure 虛擬網路的訪問。 Windows 虛擬桌面 （WVD） 不要求您打開對虛擬網路的任何入站訪問。 但是，您必須允許在虛擬網路中運行的 WVD 虛擬機器的一組出站網路連接。 有關詳細資訊，請參閱什麼是[Windows 虛擬桌面？](../virtual-desktop/overview.md#requirements)

您可以使用 Azure 防火牆應用程式規則配置此出站訪問。 有關詳細資訊，請參閱[教程：使用 Azure 門戶部署和配置 Azure 防火牆](tutorial-firewall-deploy-portal.md)。

## <a name="next-steps"></a>後續步驟

瞭解有關[Windows 虛擬桌面](https://docs.microsoft.com/azure/virtual-desktop/)的更多資訊。