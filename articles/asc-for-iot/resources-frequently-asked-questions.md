---
title: 常見問題集
description: 查找有關IoT功能和服務的Azure安全中心常見問題的解答。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 02736b63b900600c51eda8666aff0117b9307824
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310775"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>適用於 IoT 的 Azure 資訊安全中心的常見問題集

本文提供了有關 IoT Azure 安全中心的常見問題和答案的清單。

## <a name="does-azure-provide-support-for-iot-security"></a>Azure 是否支援 IoT 安全性?

Azure 提供了一個整合檢視,用於監視和管理IoT安全性,作為透過Azure安全中心提供整體安全解決方案的一部分。 如果您是應用程式開發人員,則可以使用 IoT 中心檢視來管理 IoT 應用程式安全性。

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>Azure 對 IoT 安全性的獨特價值主張是什麼?

面向 IoT 的 Azure 安全中心使企業能夠將其現有的網路安全視圖擴展到其整個 IoT 解決方案。 Azure 提供了業務解決方案的端到端視圖,使您能夠根據企業安全狀況和收集的數據執行與業務相關的操作和決策。 使用 Azure IoT、Azure IoT 邊緣和 Azure 安全中心群組安全性使您能夠使用所需的安全性創建所需的解決方案。

## <a name="who-is-azure-security-center-for-iot-made-for"></a>為誰製作的 IoT Azure 安全中心?

IoT 的 Azure 安全中心整合在 Azure IoT 中心安全中,並為日常業務解決方案安全操作提供管理。 Azure IoT 安全中心還整合到 Azure 安全中心功能中,並提供整合檢視,作為整體安全解決方案的一部分進行監視和管理 IoT 安全。

## <a name="how-does-azure-security-center-for-iot-compare-to-the-competition"></a>Azure IoT 安全中心與競爭對手相比如何?

雖然其他解決方案提供了一組功能,允許客戶創建自己的解決方案,但IoT Azure安全中心提供了獨特的端到端IoT安全解決方案,可在所有相關Azure資源的安全性範圍內提供廣泛的檢視。 Azure 支援快速部署和與IoT中心模組孿生完全整合,以便輕鬆與現有設備管理工具整合。

## <a name="do-i-have-to-be-an-azure-security-center-customer-to-use-this-service"></a>使用此服務,我必須是 Azure 安全中心客戶嗎?

否,但建議使用。 如果沒有 Azure 安全中心,IoT Azure 安全中心接收有限的連接資源數據,並提供對潛在攻擊面、威脅和潛在攻擊的有限分析。

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>我必須是 Azure IoT 客戶嗎?

是。 IoT 的 Azure 安全中心依賴於 Azure IoT 連接和基礎結構。

## <a name="do-i-have-to-install-an-agent"></a>我必須安裝代理嗎?

為了啟用適用於 IoT 的 Microsoft Azure 安全中心,在 IoT 設備上安裝代理不是強制性的。 您可以選擇以下三個選項,根據您的選擇獲得不同級別的安全監視和管理功能:

1. 安裝 IoT 安全代理的 Azure 安全中心,無論是否進行修改。 此選項提供對設備行為和訪問的最高級別增強安全見解。

1. 創建您自己的代理並實現適用於IoT安全訊息架構的Microsoft Azure安全中心。 此選項允許在設備安全代理之上使用 Microsoft Azure 安全中心進行 IoT 分析工具。

1. IoT 設備上沒有安全代理安裝。 此選項支援 IoT 中心通訊監視,降低了安全監視和管理功能。

## <a name="what-does-the-azure-security-center-for-iot-agent-do"></a>IoT 代理的 Azure 安全中心的作用是什麼?

IoT 代理 Azure 安全中心為設備配置、行為和訪問(通過掃描配置)、進程&連接提供設備級威脅覆蓋。 IoT 安全代理的 Azure 安全中心不掃描與業務相關的數據或活動。

## <a name="where-can-i-get-the-azure-security-center-for-iot-security-agent"></a>在哪裡可以獲取適用於 IoT 安全代理的 Azure 安全中心?

IoT 安全代理的 Azure 安全中心是開源的,在 GitHub 上提供https://github.com/Azure/Azure-Security-IoT32 位元和 64 位元 Windows 和 Linux 版本: .

## <a name="where-does-the-azure-security-center-for-iot-agent-get-installed"></a>IoT 代理的 Azure 安全中心安裝在哪裡?

有關安裝與代理佈建資訊,請參閱 GitHub: https://github.com/Azure/Azure-IoT-Security。

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>代理的依賴性和先決條件是什麼?

IoT 的 Azure 安全中心支援各種平臺。 請參閱[支持的設備平臺](how-to-deploy-agent.md),以驗證對特定設備的支援。

## <a name="which-data-is-collected-by-the-agent"></a>代理收集了哪些數據?

代理收集連接、訪問、防火牆配置、&操作系统基线的进程列表。

## <a name="how-much-data-will-the-agent-generate"></a>代理將生成多少數據?

代理數據生成由設備、應用程式、連接類型和客戶代理配置驅動。 由於設備和IoT解決方案之間的可變性很高,我們建議首先在實驗室或測試設置中部署代理,以觀察、學習和設置適合您需求的特定配置,同時測量生成的數據量。 啟動服務後,IoT 代理 Azure 安全中心提供用於最佳化代理輸送量的操作建議,以説明您進行配置和自訂過程。

## <a name="how-can-i-control-my-billing"></a>如何控制我的帳單?

IoT 的 Azure 安全中心提供可設定的代理掃描、資料緩衝區以及創建自訂警報的能力,這些警報會增加或減少代理生成的數據量。

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>代理消息是否會佔用IoT中心配額?

是。 代理傳輸的數據在IoT中心配額中計數。

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>接下來呢？ 我已經安裝了代理,沒有看到任何活動或日誌...

1. 檢查[代理型態是否適合裝置的指定作業系統平臺](how-to-deploy-agent.md)

1. 確認[代理裝置執行](how-to-agent-configuration.md)。

1. 檢查[服務已成功啟用](quickstart-onboard-iot-hub.md)到IoT中心中的 **「安全**」。。

1. 檢查裝置是否[設定在 IoT 中心與 IoT 模組的 Azure 安全中心](quickstart-create-security-twin.md)。

如果活動或日誌仍然不可用,請與 Azure IoT 合作夥伴聯繫以獲取其他説明。

## <a name="what-happens-when-the-internet-connection-stops-working"></a>互聯網連接停止工作時會發生什麼情況?

只要設備正在運行,代理將繼續運行和存儲數據。 數據根據大小配置存儲在安全消息快取中。 當設備恢復連接時,安全消息將恢復發送。

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>如果設備重新啟動,安全代理是否會自行恢復?

安全代理旨在在每個設備重新啟動時自動重新運行。

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>代理是否會影響設備或其他已安裝軟體的性能?

代理消耗機器資源作為任何其他應用程式/進程,不應中斷正常的設備活動。 代理運行設備上的資源消耗與其設置和配置相結合。 我們建議您在包含的環境中測試代理配置,以及與其他 IoT 應用程式和功能的互通性,然後再嘗試在生產環境中部署。

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>我正在對設備進行一些維護。 我可以關閉代理嗎?

無法關閉代理。

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>是否有方法可以測試代理是否正常工作?

如果代理停止通信或無法發送安全消息,則生成**設備處於靜音**警報。

## <a name="can-i-create-my-own-alerts"></a>我可以創建自己的警報嗎?

是。 您可以針對預先確定的行為集(如 IP 位址和打開埠)設置自訂警報。 請參閱[創建自定義警報](quickstart-create-custom-alerts.md),以瞭解有關自定義警報以及如何創建它們的更多詳細資訊。

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>我在哪裡可以看到日誌? 我可以自定義日誌嗎?

- 使用連接的日誌分析工作區查看警報和建議。 在工作區中配置存儲大小和持續時間。

- 來自安全代理的原始數據也可以存儲在日誌分析帳戶中。 在更改此選項的配置之前,請考慮大小、持續時間、存儲要求和相關成本。

## <a name="why-should-i-add-azure-security-center-for-iot-to-the-module-identity-what-is-it-used-for"></a>為什麼要將 IoT 的 Azure 安全中心添加到模組識別中? 用途為何？

IoT 的 Azure 安全中心模組用於代理配置和管理。

## <a name="next-steps"></a>後續步驟

要瞭解有關如何開始使用 IoT Azure 安全中心,請參閱以下文章:

- 閱讀 Azure 安全中心,瞭解 IoT[概述](overview.md)
- 驗證[服務先決條件](service-prerequisites.md)
- 瞭解有關如何[入門](getting-started.md)的更多
- 瞭解[針對 IoT 安全警報的 Azure 安全中心](concept-security-alerts.md)
