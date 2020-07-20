---
title: Azure API for FHIR 的其他設定
description: 概述您可以為 Azure API for FHIR 設定的其他設定
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: mihansen
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: 896d5bafd879ff3ba09bd5b8922cde4cd8345689
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871218"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Azure API for FHIR 的其他設定

在本操作指南中，我們將探討您可能想要在 Azure API for FHIR 中設定的其他設定。 還有其他頁面可深入探討更多詳細資料。

## <a name="configure-database-settings"></a>設定資料庫設定

Azure API for FHIR 使用資料庫來儲存其資料。 基礎資料庫的效能取決於服務布建期間選取的要求單位（RU）數目，或服務布建之後，在資料庫設定中所選的數量。

您必須布建輸送量，以確保您的資料庫隨時都能使用足夠的系統資源。 您的應用程式所需的 ru 數目取決於您所執行的作業。 作業的範圍可以從簡單的讀取和寫入到更複雜的查詢。

如需如何變更預設設定的詳細資訊，請參閱[設定資料庫設定](configure-database.md)。

## <a name="access-control"></a>存取控制

Azure API for FHIR 只會允許授權的使用者存取 FHIR API。 您可以透過兩個不同的機制來設定授權的使用者。 設定存取控制的主要和建議方式是使用以[Azure 角色為基礎的存取控制（RBAC）](https://docs.microsoft.com/azure/role-based-access-control/)，這可透過 [**存取控制（IAM）** ] 分頁來存取。 只有當您想要使用與訂用帳戶相關聯的 Azure Active Directory 租使用者來保護資料平面存取權時，Azure RBAC 才適用。 如果您想要使用不同的租使用者，Azure API for FHIR 會提供本機 FHIR 資料平面存取控制機制。 使用本機 RBAC 機制時，設定選項的功能並不豐富。 如需詳細資訊，請選擇下列其中一個選項：

* [適用于 FHIR 資料平面的 AZURE RBAC](configure-azure-rbac.md)。 當您使用與訂用帳戶相關聯的 Azure Active Directory 租使用者時，這是慣用的選項。
* [本機 FHIR 資料平面存取控制](configure-local-rbac.md)。 只有當您需要使用外部 Azure Active Directory 租使用者來進行資料平面存取控制時，才使用此選項。 

## <a name="enable-diagnostic-logging"></a>啟用診斷記錄
您可能想要啟用診斷記錄做為安裝程式的一部分，以便監視您的服務，並針對合規性目的進行精確的報告。 如需如何設定診斷記錄的詳細資訊，請參閱如何設定診斷記錄的操作[指南](enable-diagnostic-logging.md)，以及一些範例查詢。 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>使用自訂標頭將資料新增至 audit 記錄
在 Azure API for FHIR 中，您可能會想要在記錄檔中包含其他資訊，這是來自呼叫系統。 若要加入此資訊，您可以使用自訂標頭。

您可以使用自訂標頭來擷取數個類型的資訊。 例如：

* 識別或授權資訊
* 呼叫端的來源
* 原始組織
* 用戶端系統詳細資料 (電子健康記錄、病患入口網站)

若要將此資料新增至您的 audit 記錄，請參閱[使用自訂 HTTP 標頭將資料新增至 Audit logs](use-custom-headers.md)的操作指南。

## <a name="next-steps"></a>後續步驟

在本操作指南中，您會設定 Azure API for FHIR 的其他設定。

接下來，請參閱一系列的教學課程，以建立可讀取 FHIR 資料的 web 應用程式。

>[!div class="nextstepaction"]
>[部署 JavaScript 應用程式](tutorial-web-app-fhir-server.md)