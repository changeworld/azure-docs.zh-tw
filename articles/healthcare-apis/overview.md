---
title: 什麼是 Azure API for FHIR？ - Azure API for FHIR
description: Azure API for FHIR 可讓您透過 FHIR API 快速交換資料。 透過受控雲端服務，內嵌、管理及保存受保護的健康情況資訊 PHI。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: aca0d67326a5a0488d0108efa9acd0d01c7788cd
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "84819928"
---
# <a name="what-is-azure-api-for-fhirreg"></a>什麼是 Azure API for FHIR&reg;？

Azure API for FHIR 可讓您透過快速健康照護互通資源 (FHIR®) API 快速交換資料，且在雲端中受到受控平台即服務 (PaaS) 供應項目的支援。 這可讓任何使用健康情況資料的人員更輕鬆地擷取、管理和保存雲端中受保護的健康情況資訊 [PHI](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html)： 

- 在幾分鐘內佈建於雲端的受控 FHIR 服務 
- 在 Azure 中以企業級 FHIR®為基礎的端點，可供資料存取並以 FHIR®格式儲存
- 高效能、低延遲
- 在符合規範的雲端環境中安全管理受保護的健康情況資料 (PHI)
- 適用於行動和 Web 實作的 SMART on FHIR
- 使用角色型存取控制 (RBAC) 大規模控制自己的資料
- 針對每個資料存放區中的存取、建立、修改和讀取進行稽核記錄追蹤

Azure API for FHIR 可讓您在短短幾分鐘內建立及部署 FHIR 服務，以充分利用雲端的彈性範圍。  您只需要支付所需的輸送量和儲存體費用。 無論您管理的資料集大小為何，支援 Azure API for FHIR 的 Azure 服務都是針對快速效能而設計的。

FHIR API 與符合規範的資料存放區可讓您安全地連線到任何採用 FHIR API 的系統並與其互動。  Microsoft 會承擔 PaaS 供應項目中的作業、維護、更新和合規性需求，以便釋出您自己的作業和開發資源。 

下列影片將介紹 Azure API for FHIR 的概觀：

>[!VIDEO https://www.youtube.com/embed/5vS7Iq9vpXE]

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>透過 FHIR 運用資料的威力

健康照護產業會快速將健康情況資料轉換成 [FHIR&reg;](https://hl7.org/fhir) (快速健康照護互通資源) 的新興標準。 FHIR 可透過標準化語意和資料交換，啟用健全、可擴充的資料模型，讓所有使用 FHIR 的系統能一起運作。  將資料轉換為 FHIR，可讓您快速連線現有的資料來源，例如電子健康情況記錄系統或研究資料庫。 FHIR 也可讓您在行動和網頁程式開發的現代化實作中，快速地交換資料。 最重要的是，FHIR 可以簡化資料內嵌，並利用分析和機器學習工具加速開發。  

### <a name="securely-manage-health-data-in-the-cloud"></a>安全地管理雲端中的健康情況資料

Azure API for FHIR 可讓您透過以 HL7 FHIR 規格為基礎的一致、RESTful、FHIR API 來交換資料。 其由 Azure 中的受控 PaaS 供應項目提供支援，也會提供可擴充且安全的環境，讓您以原生 FHIR 格式管理和儲存受保護的健康情況資訊 (PHI) 資料。  

### <a name="free-up-your-resources-to-innovate"></a>釋放您的資源以進行創新

您可投入資源來建置及執行自己的 FHIR 服務，但透過 Azure API for FHIR，Microsoft 會承擔作業、維護、更新和合規性需求的工作負載，讓您能釋放自己的作業和開發資源。

### <a name="enable-interoperability-with-fhir"></a>啟用與 FHIR 的互通性

使用 Azure API for FHIR 可讓您與任何利用 FHIR API 進行讀取、寫入、搜尋和其他功能的系統連線。  其可作為功能強大的工具，以利用電子醫療記錄、臨床醫生和病患儀表板、遠端監視程式中的臨床資料，或利用系統外部具有 FHIR API 的資料庫，進行整合、正規化及應用機器學習。

### <a name="control-data-access-at-scale"></a>大規模控制資料存取

您可以控制您的資料。 角色型存取控制 (RBAC) 可讓您管理資料的儲存和存取方式。  提供更高的安全性並減輕系統管理工作負載，而您可根據您為環境所建立的角色定義，決定誰可以存取您所建立的資料集。  

### <a name="audit-logs-and-tracking"></a>稽核記錄和追蹤 

使用內建的稽核記錄，快速追蹤資料的去處。 追蹤每個資料存放區中的存取、建立、修改和讀取

### <a name="secure-your-data"></a>保護您的資料

利用無與倫比的安全性情報來保護您的 PHI。  您的資料隔離到每個 API 執行個體的唯一資料庫，並使用多重區域容錯移轉進行保護。 Azure API for FHIR 會為您的資料實作多層式、深度防禦和進階的威脅防護。  

## <a name="applications-for-a-fhir-service"></a>FHIR 服務的應用程式

FHIR 伺服器是健康情況資料互通性的重要工具。  Azure API for FHIR 已設計為您可快速建立、部署及開始使用的 API 和服務。  隨著 FHIR 標準在健康照護方面擴展，使用案例會持續成長，但是在下列初始客戶應用程式中， Azure API for FHIR 很實用： 

- **啟動/IOT 和應用程式開發：** 開發以病患或提供者為中心的應用程式 (行動或 Web) 的客戶，可以利用 Azure API for FHIR 作為完全受控的後端服務。 Azure API for FHIR 提供寶貴的資源，讓客戶可以在專為健康情況資料設計的安全雲端環境中管理資料和交換資料、運用 SMART on FHIR 實作指導方針，以及讓所有提供者系統都能使用其技術 (例如，大部分的 EHR 都已啟用 FHIR 讀取 API)。   
- **健康照護生態系統：** 雖然 EHR 在許多臨床設定中都是主要「真相來源」，但提供者不常會有多個資料庫彼此連線或以不同的格式儲存資料。  利用位於這些系統之上的 Azure API for FHIR 即服務，可讓您將 FHIR 格式的資料標準化。  這有助於在具有一致資料格式的多個系統之間啟用資料交換。 

- **研究：** 健康照護研究人員通常會尋找 FHIR 標準，而 Azure API for FHIR 很實用，因為其會將一般 FHIR 資料模型的資料正規化，並減輕機器學習和資料共用的工作負載。
透過 Azure API for FHIR 交換資料會提供稽核記錄和存取控制，協助控制資料流程以及誰可存取哪些資料類型。 

## <a name="fhir-from-microsoft"></a>來自 Microsoft 的 FHIR

來自 Microsoft 的 FHIR 功能有兩種設定：

* Azure API for FHIR – Azure 中的 PaaS 供應項目，可輕鬆地佈建在 Azure 入口網站中並由 Microsoft 管理。
* 適用於 Azure 的 FHIR 伺服器 – 可在您的 Azure 訂用帳戶中部署的開放原始碼專案，在 GitHub (https://github.com/Microsoft/fhir-server ) 上可取得。

對於需要擴充或自訂 FHIR 伺服器，或需要存取基礎服務 (例如資料庫) 的使用案例 (不經過 FHIR API)，開發人員應該選擇適用於 Azure 的開放原始碼 FHIR 伺服器。   如需實作可供生產環境使用的現成 FHIR API 和後端服務 (其中持續性資料只能透過 FHIR API 存取)，開發人員應選擇 Azure API for FHIR。

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure API for FHIR，請遵循 5 分鐘的快速入門來部署 Azure API for FHIR。

>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。
