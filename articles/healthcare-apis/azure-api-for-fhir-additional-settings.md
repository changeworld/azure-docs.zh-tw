---
title: Azure API for FHIR 的其他設定
description: 您可以為 Azure API for FHIR 設定的其他設定總覽
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: matjazl
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: b9949ab4cfa42553e5a8d28244b8f621f09334d1
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398159"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Azure API for FHIR 的其他設定

在本操作指南中，我們將探討您可能想要在 Azure API for FHIR 中設定的其他設定。 還有其他頁面深入探討更多詳細資料。

## <a name="configure-database-settings"></a>設定資料庫設定

Azure API for FHIR 會使用資料庫來儲存其資料。 基礎資料庫的效能取決於在服務布建期間選取的要求單位數目 (RU) ，或在服務布建之後，于資料庫設定中選取的數目。

您必須布建輸送量，以確保您的資料庫隨時都能使用足夠的系統資源。 您的應用程式需要多少 ru 取決於您所執行的作業。 作業可以從簡單的讀取和寫入範圍，到更複雜的查詢。

如需有關如何變更預設設定的詳細資訊，請參閱 [設定資料庫設定](configure-database.md)。

## <a name="access-control"></a>存取控制

Azure API for FHIR 只會允許授權的使用者存取 FHIR API。 您可以透過兩種不同的機制來設定授權的使用者。 設定存取控制的主要和建議方式是使用 [azure 角色型存取控制 (AZURE RBAC)](../role-based-access-control/index.yml)，可透過 **存取控制 (IAM)** 分頁來存取。 如果您想要使用與訂用帳戶相關聯的 Azure Active Directory 租使用者來保護資料平面存取，Azure RBAC 才適用。 如果您想要使用不同的租使用者，Azure API for FHIR 提供本機 FHIR 資料平面存取控制機制。 使用本機 RBAC 機制時，設定選項的功能不會很豐富。 如需詳細資料，請選擇下列其中一個選項：

* [適用于 FHIR 資料平面的 AZURE RBAC](configure-azure-rbac.md)。 當您使用與訂用帳戶相關聯的 Azure Active Directory 租使用者時，這是慣用的選項。
* [本機 FHIR 資料平面存取控制](configure-local-rbac.md)。 只有當您需要針對資料平面存取控制使用外部 Azure Active Directory 租使用者時，才使用此選項。 

## <a name="enable-diagnostic-logging"></a>啟用診斷記錄
您可能會想要在安裝過程中啟用診斷記錄，以監視您的服務，並針對合規性目的提供正確的報告。 如需有關如何設定診斷記錄的詳細資訊，請參閱如何設定診斷記錄的操作 [指南](enable-diagnostic-logging.md) ，以及一些範例查詢。 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>使用自訂標頭將資料新增至 audit 記錄
在 Azure API for FHIR 中，您可能會想要在記錄檔中包含其他資訊（來自呼叫系統）。 若要加入此資訊，您可以使用自訂標頭。

您可以使用自訂標頭來擷取數個類型的資訊。 例如：

* 識別或授權資訊
* 呼叫端的來源
* 原始組織
* 用戶端系統詳細資料 (電子健康記錄、病患入口網站)

若要將此資料新增至您的 audit 記錄，請參閱 [使用自訂 HTTP 標頭將資料新增至 Audit 記錄](use-custom-headers.md) 操作指南。

## <a name="next-steps"></a>後續步驟

在本操作指南中，您會設定 Azure API for FHIR 的其他設定。

接下來，請查看一系列的教學課程，以建立可讀取 FHIR 資料的 web 應用程式。

>[!div class="nextstepaction"]
>[部署 JavaScript 應用程式](tutorial-web-app-fhir-server.md)