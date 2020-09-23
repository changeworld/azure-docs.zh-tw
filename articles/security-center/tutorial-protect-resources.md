---
title: 存取與應用程式控制教學課程 - Azure 資訊安全中心
description: 此教學課程說明如何設定 Just-in-Time VM 存取原則和應用程式控制原則。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 56dd74fba46aa8b79c94b1460996bb6edb1ff93f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904608"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>教學課程：使用 Azure 資訊安全中心保護您的資源
資訊安全中心使用存取和應用程式控制原則來阻擋惡意活動，以限制您暴露於威脅的風險。 Just-in-Time (JIT) 虛擬機器 (VM) 存取透過讓您拒絕對 VM 的持續存取，進而減少您暴露於攻擊的風險。 不過，您可以只在需要的時候，提供對 VM 的受控制及稽核的存取。 自適性應用程式控制透過控制可在 VM 上執行的應用程式，進而協助強化 VM 以抵禦惡意軟體。 資訊安全中心會利用機器學習服務來分析在 VM 中執行的程序，並協助您利用此情報來套用列入允許清單規則。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 設定 Just-In-Time VM 存取原則
> * 設定應用程式控制原則

## <a name="prerequisites"></a>Prerequisites
若要逐步執行本教學課程中涵蓋的功能，您必須啟用 Azure Defender。 您可以免費試用 Azure Defender。 如需相關資訊，請參閱[試用 Azure Defender](security-center-pricing.md)。

## <a name="manage-vm-access"></a>管理 VM 存取
JIT VM 存取可用於鎖定 Azure VM 的連入流量，進而降低暴露於攻擊的風險，同時讓您視需要輕鬆地連線至 VM。

管理連接埠不需要隨時保持開啟。 只有在連線至 VM 時 (例如進行執行管理或維修工作)，才需要將管理連接埠開啟。 啟用 Just-In-Time 之後，資訊安全中心會使用「網路安全性群組」(NSG) 規則，限制對管理連接埠的存取，讓攻擊者無法將這些連接埠當作攻擊目標。

請遵循[使用 Just-In-Time 存取保護您的管理連接埠](security-center-just-in-time.md)中的指導。

## <a name="harden-vms-against-malware"></a>強化 VM 以抵禦惡意軟體
自適性應用程式控制可協助您定義一組可以在設定之資源群組上執行的應用程式，再加上其他的好處可共同協助強化您的 VM 以抵禦惡意軟體。 資訊安全中心會利用機器學習服務來分析在 VM 中執行的程序，並協助您利用此情報來套用列入允許清單規則。

請遵循[使用自適性應用程式控制來減少電腦的攻擊面](security-center-adaptive-application.md)中的指導。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何透過下列方式限制暴露於威脅的風險：

> [!div class="checklist"]
> * 設定 Just-In-Time VM 存取原則，只在需要時提供對 VM 的受控制及稽核的存取
> * 設定自適性應用程式控制原則，以控制哪些應用程式可在您 VM 上執行

請前進到下一個教學課程，以了解如何回應安全性事件。

> [!div class="nextstepaction"]
> [教學課程：回應安全性事件](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
