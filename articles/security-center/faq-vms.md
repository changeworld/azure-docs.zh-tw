---
title: Azure 資訊安全中心常見問題-虛擬機器的相關問題
description: Azure 資訊安全中心中虛擬機器的常見問題，這項產品可協助您預防、偵測及回應威脅
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 0b7817539a26b7ebfa24efd086edf8b0ef82eae3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80436016"
---
# <a name="faq---questions-about-virtual-machines"></a>常見問題-虛擬機器的相關問題


## <a name="what-types-of-virtual-machines-are-supported"></a>支援哪些類型的虛擬機器？

對於使用[傳統與 Resource Manager 部署模型](../azure-classic-rm.md)建立的虛擬機器 (VM)，提供監視和建議。

如需支援之平台的清單，請參閱 [Azure 資訊安全中心支援的平台](security-center-os-coverage.md)。


## <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>為什麼 Azure 資訊安全中心無法辨識我的 Azure VM 上所執行的反惡意程式碼軟體解決方案？

Azure 資訊安全中心能辨識透過 Azure 擴充功能安裝的反惡意程式碼軟體。 例如，資訊安全中心無法偵測您在提供的映像中預先安裝的反惡意程式碼軟體，或是您使用自己的程序 (例如組態管理系統) 在虛擬機器上安裝的反惡意程式碼軟體。


## <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>為什麼我會從 VM 收到「遺失掃描資料」訊息？

當沒有 VM 的掃瞄資料時會顯示此訊息。 在 Azure 資訊安全中心中啟用資料收集之後，可能需要花費一些時間 (少於一小時) 才能填入掃描資料。 在初始填入掃描資料之後，您可能會收到此訊息，因為完全沒有掃描資料或是沒有最近的掃描資料。 掃描不會填入處於停止狀態之 VM 的資料。 如果最近沒有填入掃描資料 (根據 Windows 代理程式的保留原則，預設值為 30 天)，可能也會顯示此訊息。


## <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>資訊安全中心多久掃描作業系統弱點、系統更新和端點保護問題一次？

以下是安全性中心掃描弱點、更新和問題的延遲時間：

- 作業系統安全性設定：在 48 小時內更新資料
- 系統更新：在 24 小時內更新資料
- 端點保護問題：在 8 小時內更新資料

「資訊安全中心」通常會每小時掃描一次是否有新資料，並相應地重新整理建議。 

> [!NOTE]
> 資訊安全中心使用 Log Analytics 代理程式來收集和儲存資料。 如需詳細資訊，請參閱 [Azure 資訊安全中心平台移轉](security-center-platform-migration.md)。


## <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>為什麼我會收到「VM 代理程式已遺失」訊息？

VM 代理程式必須安裝在 VM 上，才能啟用資料收集。 預設會為從 Azure Marketplace 部署的 VM 安裝「VM 代理程式」。 如需如何在其他 VM 上安裝 VM 代理程式的資訊，請參閱部落格文章 [VM Agent and Extensions (VM 代理程式和擴充功能)](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)。