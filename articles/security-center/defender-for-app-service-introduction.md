---
title: 適用於 App Service 的 Azure Defender - 優點和功能
description: 瞭解 Azure Defender for App Service 的功能，以及如何在您的訂用帳戶上啟用
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 200e1fd7bfffef403fa459d3de13dc31145b8a33
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796622"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>適用於 App Service 的 Azure Defender 簡介

Azure App Service 是完全受控的平臺，可用於建立和裝載您的 web 應用程式和 Api。 因為平臺是完全受控的，所以您不必擔心基礎結構。 此平台提供管理、監視和運作的深入解析，以符合企業級的效能、安全性和合規需求。 如需詳細資訊，請參閱 [Azure App Service](https://azure.microsoft.com/services/app-service/)。

**適用於 App Service 的 Azure Defender** 使用雲端規模，來識別將透過 App Service 執行的應用程式視為目標的攻擊。 攻擊者會探查 Web 應用程式，以找出弱點並加以入侵。 在路由到特定環境之前，對執行於 Azure 中的應用程式提出的要求會經過數個閘道，並在其中進行檢查和記錄。 接著，此資料會用來識別入侵和攻擊者，並了解後續將使用的新模式。


## <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|版本狀態：|正式上市 (GA)|
|定價：|[適用於 App Service 的 Azure Defender](azure-defender.md) 的計費方式如[定價頁面](security-center-pricing.md)所示<br>[定價和設定] 頁面會列出您 **資源數量** 的實例數目。 該數位是此訂用帳戶上所有 App Service 方案中的計算實例總數，在您開啟 [定價層] 頁面時執行。<br>若要驗證計數，請在 Azure 入口網站中開啟 **App Service 方案** ，並檢查每個方案所使用的計算實例數目。|
|支援的 App Service 方案：|![是](./media/icons/yes-icon.png) 基本、標準、進階、隔離式或 Linux<br>![無](./media/icons/no-icon.png) 免費、共用或使用量<br>[深入了解 App Service 方案](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 國家/地區/主權 (US Gov、中國 Gov、其他 Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-app-service"></a>Azure Defender 的 App Service 有哪些優點？

當您啟用 Azure Defender 的 App Service 時，您會立即受益于此 Azure Defender 方案提供的下列服務：

- **安全資訊** 安全中心會評估您的 App Service 計畫所涵蓋的資源，並根據其發現結果產生安全性建議。 使用這些建議中的詳細指示，強化您的 App Service 資源。

- 偵測 **-Azure** Defender 藉由監視來偵測 App Service 資源的許多威脅：
    - 您的 App Service 執行所在的 VM 實例，以及其管理介面
    - 在 App Service 應用程式中傳送和傳送的要求和回應
    - 基礎沙箱和 Vm
    - App Service 內部記錄-可因應 Azure 以雲端提供者的可見度而提供

作為雲端原生解決方案，Azure Defender 可以識別適用于多個目標的攻擊方法。 例如，從單一主機，很難識別來自一小部分 Ip 的分散式攻擊，並在多部主機上進行類似的端點編目。

記錄資料和基礎結構會在一段過程中告訴您：從攻擊的新攻擊，到客戶電腦中的危害。 因此，即使是在 Web 應用程式遭到惡意探索後才部署資訊安全中心，還是有可能偵測到進行中的攻擊。


## <a name="what-threats-can-azure-defender-for-app-service-detect"></a>Azure Defender 可 App Service 偵測到哪些威脅？

### <a name="threats-by-mitre-attck-tactics"></a>威脅（依 MITRE ATT&CK 策略）

Azure Defender 會監視對您 App Service 資源的許多威脅。 警示幾乎涵蓋 MITRE ATT&CK 策略的完整清單，從前攻擊到命令和控制。 Azure Defender 可以偵測到：

- **攻擊前的威脅** -Defender 可以偵測到多個攻擊者經常用來探查應用程式是否有弱點的弱點掃描器類型執行。

- **初始存取威脅**  - 當已知的惡意 IP 位址連接到您的 Azure App Service FTP 介面時， [Microsoft 威脅情報](https://go.microsoft.com/fwlink/?linkid=2128684)可提供這些警示，包括觸發警示。

- **執行威脅** ： Defender 可以偵測到執行高許可權命令的嘗試、Windows App Service 上的 Linux 命令、無檔案攻擊行為、數位貨幣挖掘工具，以及許多其他可疑且惡意的程式碼執行活動。

### <a name="dangling-dns-detection"></a>無關聯 DNS 偵測

適用于 App Service 的 Azure Defender 也會在 App Service 網站已解除委任時，識別 DNS 註冊機構中剩餘的任何 DNS 專案-這些是所謂的無關聯 DNS 專案。 當您移除網站，而不是從 DNS 註冊機構移除其自訂網域時，DNS 專案會指向不存在的資源，而您的子域很容易接管。 Azure Defender 不會掃描您的 DNS 註冊機構是否 *有現有* 的無關聯 dns 專案;當 App Service 網站已解除委任，而且其自訂網域 (DNS 專案) 未刪除時，就會向您發出警示。

子域接管是組織常見的高嚴重性威脅。 當威脅執行者偵測到無關聯的 DNS 專案時，他們會在目的地位址建立自己的網站。 系統會將適用于組織網域的流量導向至威脅執行者的網站，並可將該流量用於各式各樣的惡意活動。

無論您的網域是否使用 Azure DNS 或外部網域註冊機構進行管理，以及適用于 Windows 和 Linux 上的 App Service，都可以使用無關聯的 DNS 保護。

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="關於探索到的無關聯 DNS 專案的 Azure Defender 警示範例。啟用 App Service 的 Azure Defender，以接收您環境的此警示和其他警示。" lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

深入瞭解無關聯的 DNS 和子域接管的威脅，以 [防止無限制的 dns 專案，並避免子域接管](../security/fundamentals/subdomain-takeover.md)。

如需 Azure App Service 警示的完整清單，請參閱 [警示的參考表](alerts-reference.md#alerts-azureappserv)。

> [!NOTE]
> 如果您的自訂網域未直接指向 App Service 資源，或如果 Defender 尚未監視您網站的流量，因為已啟用無關聯 DNS 保護 (，因為沒有可協助識別自訂網域) 的記錄，所以 defender 可能不會觸發無關聯的 DNS 警示。

## <a name="how-to-protect-your-azure-app-service-web-apps-and-apis"></a>如何保護您的 Azure App Service web 應用程式和 Api

若要使用適用於 App Service 的 Azure Defender 來保護您的 Azure App Service 方案：

1. 確定您有受支援且與專用機器相關聯的 App Service 方案。 支援的方案如前述的[可用性](#availability)所列。

2. 在您的訂用帳戶上啟用 **Azure Defender** ，如 [Azure 資訊安全中心定價](security-center-pricing.md)所述。

    您可以選擇性地啟用 Azure Defender 中的個別方案 (例如適用于 App Service) 的 Azure Defender。

    資訊安全中心會與 App Service 原生整合，因此無須進行部署及上線 - 整合程序是透明的。


## <a name="next-steps"></a>後續步驟

在本文中，您已了解適用於 App Service 的 Azure Defender。 

如需相關內容，請參閱下列文章： 

- 若要將您的警示匯出至 Azure Sentinel、任何協力廠商 SIEM 或任何其他外部工具，請依照[將警示串流至 SIEM、SOAR 或 IT 服務管理解決方案](export-to-siem.md)中的指示操作。
- 如需 Azure Defender for App Service 警示的清單，請參閱 [警示的參考表](alerts-reference.md#alerts-azureappserv)。
- 如需 App Service 方案的詳細資訊，請參閱 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/plans/)。
> [!div class="nextstepaction"]
> [啟用 Azure Defender](security-center-pricing.md#enable-azure-defender)