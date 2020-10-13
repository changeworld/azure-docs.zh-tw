---
title: Azure Digital Twins 解決方案的安全性
titleSuffix: Azure Digital Twins
description: 瞭解 Azure 數位 Twins 的安全性最佳作法。
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 4dc768b92a9ffbeafc31ef81d065237f47fb645e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331874"
---
# <a name="secure-azure-digital-twins"></a>保護 Azure 數位 Twins

為了安全起見，Azure 數位 Twins 可讓您精確地控制部署中的特定資料、資源和動作。 它會透過名為 **角色型存取控制 **的細微角色和版權管理策略來 (RBAC) 。 您可以在 [這裡](../role-based-access-control/overview.md)閱讀 Azure RBAC 的一般原則。

Azure 數位 Twins 也支援靜態資料的加密。

## <a name="granting-permissions-with-rbac"></a>使用 RBAC 授與許可權

RBAC 會透過與 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 的整合，提供給 Azure 數位 Twins。

您可以使用 RBAC 將許可權授與 *安全性主體，該安全性主體*可能是使用者、群組或應用程式服務主體。 安全性主體是由 Azure AD 進行驗證，並接收傳回的 OAuth 2.0 權杖。 此權杖可以用來授權對 Azure 數位 Twins 實例的存取要求。

### <a name="authentication-and-authorization"></a>驗證與授權

使用 Azure AD，存取會有兩個步驟的程式。 當安全性主體 (使用者、群組或應用程式) 嘗試存取 Azure 數位 Twins 時，要求必須 *經過驗證* 和 *授權*。 

1. 首先，會驗證安全性主體的 *身分*識別，並傳回 OAuth 2.0 權杖。
2. 接下來，權杖會作為要求的一部分傳遞至 Azure 數位 Twins 服務，以 *授權* 存取指定的資源。

驗證步驟需要在執行時間將任何應用程式要求包含 OAuth 2.0 存取權杖。 如果應用程式是在 Azure 實體中執行（例如 [Azure Functions](../azure-functions/functions-overview.md) 應用程式），則可以使用 **受控識別** 來存取資源。 在下一節中深入瞭解受控識別。

授權步驟會要求將 Azure 角色指派給安全性主體。 指派給安全性主體的角色會決定主體將擁有的許可權。 Azure 數位 Twins 提供的 Azure 角色包含 Azure 數位 Twins 資源的一組許可權。 本文稍後會說明這些角色。

若要深入瞭解 Azure 中支援的角色和角色指派，請參閱瞭解 Azure RBAC 檔中 [*的不同角色*](../role-based-access-control/rbac-and-directory-admin-roles.md) 。

#### <a name="authentication-with-managed-identities"></a>使用受控識別執行驗證作業

[適用于 azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md) 識別是一種跨 Azure 功能，可讓您建立與應用程式程式碼執行所在之部署相關聯的安全身分識別。 然後，您可以將該身分識別與存取控制角色建立關聯，以授與自訂許可權來存取您應用程式所需的特定 Azure 資源。

使用受控識別，Azure 平台就能管理此執行階段識別。 您不需要為了識別本身或您需要存取的資源，在應用程式程式碼或設定中儲存及保護存取金鑰。 在 Azure App Service 應用程式內執行的 Azure 數位 Twins 用戶端應用程式不需要處理 SAS 規則和金鑰，或任何其他存取權杖。 用戶端應用程式只需要 Azure 數位 Twins 命名空間的端點位址。 當應用程式連線時，Azure 數位 Twins 會將受管理實體的內容系結至用戶端。 一旦與受控識別相關聯，您的 Azure 數位 Twins 用戶端就可以進行所有授權的作業。 然後將受管理的實體與 Azure 數位 Twins Azure 角色建立關聯，以授與授權， (如下) 所述。

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>授權：適用于 Azure 數位 Twins 的 Azure 角色

Azure 提供下列 Azure 內建角色，以授權存取 Azure 數位 Twins 資源：
* *Azure 數位 Twins 擁有者 (預覽版) * –使用此角色可提供 Azure 數位 Twins 資源的完整存取權。
* *Azure 數位 Twins 讀者 (預覽版) * –使用此角色可提供 Azure 數位 Twins 資源的唯讀存取權。

> [!TIP]
> *Azure 數位 Twins 讀者 (預覽版) *角色現在也支援流覽關聯性。

如需如何定義內建角色的詳細資訊，請參閱瞭解 Azure RBAC 檔中的 [*角色定義*](../role-based-access-control/role-definitions.md) 。 如需建立 Azure 自訂角色的詳細資訊，請參閱 [*azure 自訂角色*](../role-based-access-control/custom-roles.md)。

您可以透過兩種方式來指派角色：
* 透過 Azure 入口網站 (中 Azure 數位 Twins 的 [存取控制] (IAM) 窗格，請參閱 [*使用 AZURE RBAC 和 Azure 入口網站新增或移除角色指派*](../role-based-access-control/role-assignments-portal.md)) 
* via CLI 命令以新增或移除角色

如需如何執行這項作業的詳細步驟，請在 Azure 數位 Twins 教學課程 [*：連接端對端解決方案*](tutorial-end-to-end.md)中試用。

### <a name="permission-scopes"></a>權限範圍

將 Azure 角色指派給安全性主體之前，請先決定安全性主體應該具備的存取範圍。 最佳做法規定最好只授與最少的可能範圍。

下列清單說明您可以範圍存取 Azure 數位 Twins 資源的層級。
* 模型：此資源的動作會對在 Azure 數位 Twins 中上傳的 [模型](concepts-models.md) 進行控制。
* 查詢數位 Twins 圖：此資源的動作會決定在 Azure 數位 Twins 圖形內對數位 Twins 執行 [查詢作業](concepts-query-language.md) 的能力。
* 數位對應項：此資源的動作可讓您控制對應項圖形中 [數位 twins](concepts-twins-graph.md) 的 CRUD 作業。
* 數位對應項關聯性：此資源的動作會針對對應項圖形中數位 twins 之間的 [關聯](concepts-twins-graph.md) 性定義 CRUD 作業的控制權。
* 事件路由：此資源的動作會決定將事件從 Azure 數位 Twins [路由傳送](concepts-route-events.md) 至端點服務的許可權，例如 [事件中樞](../event-hubs/event-hubs-about.md)、 [事件方格](../event-grid/overview.md)或 [服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)。

### <a name="troubleshooting-permissions"></a>疑難排解許可權

如果使用者嘗試執行其角色不允許的動作，他們可能會收到來自服務要求讀取的錯誤 `403 (Forbidden)` 。 如需詳細資訊和疑難排解步驟，請參閱 [*疑難排解： Azure 數位 Twins 要求失敗，狀態： 403 (禁止) *](troubleshoot-error-403.md)。

## <a name="encryption-of-data-at-rest"></a>待用資料加密

Azure 數位 Twins 會將待用資料和傳輸中的資料加密提供給資料中心，並在您存取資料中心時將其解密。 這項加密會使用 Microsoft 管理的加密金鑰進行。

## <a name="next-steps"></a>後續步驟

* 請參閱 [*如何：設定實例和驗證*](how-to-set-up-instance-portal.md)，以瞭解這些概念的作用。

* 請參閱 how to [*：撰寫應用程式驗證程式*](how-to-authenticate-client.md)代碼，以瞭解如何從用戶端應用程式程式碼與這些概念互動。

* 深入瞭解 [適用于 Azure 的 RBAC](../role-based-access-control/overview.md)。
