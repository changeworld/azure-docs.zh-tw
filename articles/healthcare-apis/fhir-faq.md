---
title: 有關 Azure 中 FHIR 服務的常見問題-Azure API for FHIR
description: 取得 Azure API for FHIR 常見問題的解答，例如 FHIR Api 背後資料的儲存位置，以及版本支援。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870978"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>關於 Azure API for FHIR 的常見問題

## <a name="what-is-fhir"></a>什麼是 FHIR？
快速健康照護互通資源（FHIR）是一項互通性標準，目的是要在不同的健全狀況系統之間交換醫療保健資料。 此標準是由 HL7 組織所開發，並由世界各地的醫療保健組織採用。 可用的最新 FHIR 版本為 R4 （第4版）。 Azure API for FHIR 支援 R4，而且也支援先前的版本 STU3 （試用版的標準3）。 如需 FHIR 的詳細資訊，請造訪[HL7.org](http://hl7.org/fhir/summary.html)。

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>FHIR Api 背後的資料會儲存在 Azure 中嗎？

是，資料會儲存在 Azure 中的受控資料庫。 Azure API for FHIR 不會提供基礎資料存放區的直接存取權。

## <a name="what-identity-provider-do-you-support"></a>您支援哪些身分識別提供者？

我們目前支援 Microsoft Azure Active Directory 做為身分識別提供者。

## <a name="what-fhir-version-do-you-support"></a>您支援哪些 FHIR 版本？

在適用于 Azure 的 Azure API for FHIR （PaaS）和 FHIR 伺服器（開放原始碼）上，我們支援版本4.0.0 和3.0.1。

如需詳細資訊，請參閱[支援的功能](fhir-features-supported.md)。 閱讀[HL7 FHIR 版本歷程記錄](https://hl7.org/fhir/R4/history.html)版本之間變更的內容。

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>適用于 Azure 的開放原始碼 Microsoft FHIR 伺服器與 Azure API for FHIR 之間有何差異？

Azure API for FHIR 是 Azure 開放原始碼 Microsoft FHIR Server 的託管和受控版本。 在受控服務中，Microsoft 提供所有維護和更新。 

當您執行適用于 Azure 的 FHIR 伺服器時，您可以直接存取基礎服務。 但是，如果您要儲存 PHI 資料，您也必須負責維護和補救伺服器，以及所有必要的相容性工作。

從開發的觀點來看，每項功能會先部署到適用于 Azure 的開放原始碼 Microsoft FHIR Server。 在開放原始碼中驗證後，它就會發行到 PaaS Azure API for FHIR 解決方案。 以開放原始碼和 PaaS 發行的時間，取決於功能和其他藍圖優先順序的複雜性。 

## <a name="what-is-smart-on-fhir"></a>什麼是 FHIR 的智慧型功能？

FHIR 上的智慧型（可替換醫學應用程式和可重複使用的技術）是一組開放的規格，可將合作夥伴應用程式與 FHIR 伺服器和其他健全狀況 IT 系統（例如電子健康記錄和健康狀態資訊交換）整合。 藉由建立智慧型 on FHIR 應用程式，您可以確保您的應用程式可由不同系統的眾多存取和利用。
驗證和 Azure API for FHIR。 若要深入瞭解智慧型，請造訪[智慧型健全狀況](https://smarthealthit.org/)。

## <a name="next-steps"></a>後續步驟

在本文中，您已閱讀一些關於 Azure API for FHIR 的常見問題。 閱讀適用于 Azure 的 FHIR 伺服器中支援的功能：
 
>[!div class="nextstepaction"]
>[支援的 FHIR 功能](fhir-features-supported.md)