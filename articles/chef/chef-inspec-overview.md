---
title: 針對 Azure 基礎結構的合規性自動化使用 InSpec
description: 了解如何使用 InSpec 來偵測 Azure 部署中的問題
keywords: azure, chef, devops, virtual machines, overview, automate, inspec, 虛擬機器, 概觀, 自動化
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158236"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>針對 Azure 基礎結構的合規性自動化使用 InSpec

[InSpec](https://www.chef.io/inspec/) \(英文\) 是 Chef 的開放原始碼語言，描述可以在軟體工程師、作業和安全性工程師之間共用的安全性與合規性規則。 InSpec 的運作方式是將您基礎結構的實際狀態，與您在方便讀取和方便寫入的 InSpec 程式碼中所表示的理想狀態兩者進行比較。 InSpec 會偵測違規，並且以報告形式顯示調查結果，但可讓您控制補救措施。

可以使用 InSpec 驗證訂閱中的資源和資源組的狀態，包括虛擬機器、網路設定、Azure 活動目錄設置等。

本文說明使用 InSpec 讓 Azure 上的安全性和合規性更簡單的優點。

## <a name="make-compliance-easy-to-understand-and-assess"></a>讓合規性易於了解和評估

在試算表或 Word 文檔中編寫的合規性文檔使要求可以解釋。 使用 InSpec，您就可以將需求轉換為可建立版本、可執行、人類看得懂的程式碼。 程式碼能取代應該要評估哪些項目的討論，有利於具有明確意圖的有形測試。

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>偵測全面的問題並且對其補救措施設定優先權

InSpec 的無代理程式偵測模式可讓您快速大規模評估您的暴露等級。 影響/嚴重性評分的內建中繼資料，可協助判斷要著重哪個區域的補救措施。 您也可以快速地撰寫規則並立即推出，以回應新的弱點或法規。

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>使用策略來賓配置審核 Azure 虛擬機器

Azure 直接支援使用 Chef InSpec 定義通過 Azure[策略來賓配置](/azure/governance/policy/concepts/guest-configuration)審核 Azure 虛擬機器。 來賓配置將 Linux 虛擬機器評估為提供的 Chef InSpec 定義，並通過 Azure 策略報告合規性。 這些審核的結果也通過 Azure 監視器日誌報告;啟用警報和其他自動化方案。

## <a name="satisfy-audits"></a>滿足稽核要求

有了 InSpec，您就可以隨時回應稽核問題，而不只是以每季或每年等預先決定的間隔來回應。 藉由持續執行 InSpec 測試，您會進入稽核循環，了解您確切的合規性狀態和記錄，而不是對稽核員的發現感到驚訝。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [在 Azure 雲外殼中試用 InSpec](https://shell.azure.com)