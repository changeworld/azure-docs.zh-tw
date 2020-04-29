---
title: Azure 防火牆遠端工作支援
description: 本文說明 Azure 防火牆可以如何支援您的遠端工作強制需求。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289637"
---
# <a name="azure-firewall-remote-work-support"></a>Azure 防火牆遠端工作支援

Azure 防火牆是受控的雲端式網路安全性服務，可保護您的 Azure 虛擬網路資源。 它是完全具狀態的防火牆即服務，具有內建的高可用性和不受限制的雲端延展性。 

## <a name="firewall-rules"></a>防火牆規則

您可以使用 Azure 防火牆來保護您的虛擬桌面基礎結構（VDI）使用 Azure 防火牆[DNAT 規則](rule-processing.md)對您 Azure 虛擬網路的輸入 RDP 存取。 Windows 虛擬桌面（WVD）不會要求您開啟虛擬網路的任何輸入存取權。 不過，您必須針對在虛擬網路中執行的 WVD 虛擬機器，允許一組輸出網路連線。 如需詳細資訊，請參閱[什麼是 Windows 虛擬桌面？](../virtual-desktop/overview.md#requirements)

您可以使用 Azure 防火牆應用程式規則來設定此輸出存取。 如需詳細資訊，請參閱[教學課程：使用 Azure 入口網站部署和設定 Azure 防火牆](tutorial-firewall-deploy-portal.md)。

## <a name="next-steps"></a>後續步驟

深入瞭解[Windows 虛擬桌面](https://docs.microsoft.com/azure/virtual-desktop/)。