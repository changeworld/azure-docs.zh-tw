---
title: 將 Windows 防火牆資料連線到 Azure Sentinel |Microsoft Docs
description: 在 Azure Sentinel 中啟用 Windows 防火牆連接器，輕鬆地從已安裝 Log Analytics 代理程式的 Windows 機器串流防火牆事件。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: d33ba0dbb62cd7206829ed9ae580ea2aa3334bcf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559469"
---
# <a name="connect-windows-firewall"></a>連線 Windows 防火牆



Windows 防火牆連接器可讓您輕鬆地連接您的 Windows 防火牆記錄（如果它們已連線到您的 Azure Sentinel 工作區）。 此連線可讓您查看儀表板、建立自訂警示，以及改善調查。 這可讓您深入瞭解組織的網路，並改善您的安全性作業功能。 此解決方案會從已安裝 Log Analytics 代理程式的 Windows 電腦收集 Windows 防火牆事件。 


> [!NOTE]
> - 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。
> - 如果 Azure Sentinel 和 Azure 資訊安全中心收集到相同的工作區，則不需要透過此連接器啟用 Windows 防火牆解決方案。 如果您仍然啟用它，它不會造成重複的資料。 

## <a name="enable-the-connector"></a>啟用連接器 

1. 在 Azure Sentinel 入口網站中，選取 [**資料連線器**]，然後按一下 [ **Windows 防火牆**] 磚。 
1.  如果您的 Windows 機器位於 Azure：
    1. 按一下 [**在 Azure Windows 虛擬機器上安裝代理程式**]。
    1. 在 [**虛擬機器**] 清單中，選取您想要串流至 Azure Sentinel 的 Windows 電腦。 請確定這是 Windows VM。
    1. 在針對該 VM 開啟的視窗中，按一下 **[** 連線]。  
    1. 按一下 [ **Windows 防火牆連接器**] 視窗中的 [**啟用**]。 

2. 如果您的 Windows 電腦不是 Azure VM：
    1. 按一下 [**在非 Azure 電腦上安裝代理程式**]。
    1. 在 [**直接代理程式**] 視窗中，選取 [**下載 windows 代理程式（64位）** ] 或 [**下載 windows 代理程式（32位）**]。
    1. 在您的 Windows 電腦上安裝代理程式。 複製 [**工作區識別碼**]、[**主要金鑰**] 和 [**次要金鑰**]，並在安裝期間出現提示時使用。

4. 選取您想要串流的資料類型。
5. 按一下 [**安裝解決方案**]。
6. 若要在 Log Analytics 中使用適用于 Windows 防火牆的相關架構，請搜尋**SecurityEvent**。

## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Windows 防火牆連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

