---
title: Azure Digital Twins 解決方案的安全性
titleSuffix: Azure Digital Twins
description: 瞭解 Azure 數位 Twins 的安全性最佳作法。
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0f1f9e17646c4432d9c1103b3c579fc6bb0d2f13
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85833159"
---
# <a name="secure-azure-digital-twins-with-role-based-access-control"></a>使用以角色為基礎的存取控制來保護 Azure 數位 Twins

為了安全起見，Azure 數位 Twins 可對您部署中的特定資料、資源和動作進行精確的存取控制。 它會透過名為**角色型存取控制（RBAC）** 的細微角色和版權管理策略來執行此工作。 您可以在[這裡](../role-based-access-control/overview.md)閱讀適用于 Azure RBAC 的一般原則。

## <a name="rbac-through-azure-ad"></a>RBAC 到 Azure AD

RBAC 會透過與[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) （Azure AD）的整合提供給 Azure 數位 Twins。

您可以使用 RBAC 將許可權授與*安全性主體*，這可能是使用者、群組或應用程式服務主體。 安全性主體會由 Azure AD 進行驗證，並接收傳回的 OAuth 2.0 權杖。 此權杖可用來授權對 Azure 數位 Twins 實例的存取要求。

## <a name="authentication-and-authorization"></a>驗證與授權

有了 Azure AD，存取是兩個步驟的程式。 當安全性主體（使用者、群組或應用程式）嘗試存取 Azure 數位 Twins 時，要求必須*經過驗證*和*授權*。 

1. 首先，安全性主體的身分識別已*通過驗證*，並傳回 OAuth 2.0 權杖。
2. 接下來，權杖會作為要求的一部分傳遞至 Azure 數位 Twins 服務，以*授權*存取指定的資源。

驗證步驟需要在執行時間包含 OAuth 2.0 存取權杖的任何應用程式要求。 如果應用程式在 Azure 實體（例如[Azure Functions](../azure-functions/functions-overview.md)應用程式）內執行，它可以使用**受控識別**來存取資源。 在下一節中閱讀更多關於受控識別的資訊。

授權步驟需要將 RBAC 角色指派給安全性主體。 指派給安全性主體的角色會決定主體將擁有的許可權。 Azure 數位 Twins 提供 RBAC 角色，其中包含 Azure 數位 Twins 資源的許可權集。 本文稍後將說明這些角色。

若要深入瞭解 Azure 中支援的角色和角色指派，請參閱瞭解 Azure RBAC 檔中[的不同角色](../role-based-access-control/rbac-and-directory-admin-roles.md)。

### <a name="authentication-with-managed-identities"></a>使用受控識別進行驗證

[適用于 azure 資源的受控](../active-directory/managed-identities-azure-resources/overview.md)識別是一個跨 Azure 功能，可讓您建立與應用程式程式碼執行所在之部署相關聯的安全身分識別。 接著，您可以將該身分識別與存取控制角色建立關聯，以授與自訂許可權以存取您的應用程式所需的特定 Azure 資源。

使用受控識別，Azure 平台就能管理此執行階段識別。 您不需要為了識別本身或您需要存取的資源，在應用程式程式碼或設定中儲存及保護存取金鑰。 在 Azure App Service 應用程式中執行的 Azure 數位 Twins 用戶端應用程式不需要處理 SAS 規則和金鑰，或任何其他存取權杖。 用戶端應用程式只需要 Azure 數位 Twins 命名空間的端點位址。 當應用程式連接時，Azure 數位 Twins 會將受管理的實體內容系結至用戶端。 一旦與受控識別相關聯，您的 Azure 數位 Twins 用戶端就可以執行所有授權的作業。 然後藉由將受管理的實體與 Azure 數位 Twins RBAC 角色產生關聯來授與授權（如下所述）。

### <a name="authorization-rbac-roles-for-azure-digital-twins"></a>授權： Azure 數位 Twins 的 RBAC 角色

Azure 提供下列內建的 RBAC 角色，以授權 Azure 數位 Twins 資源的存取權：
* Azure 數位 Twins 擁有者（預覽）-使用此角色可透過 Azure 數位 Twins 資源提供完整存取權。
* Azure 數位 Twins 讀者（預覽）-使用此角色可提供 Azure 數位 Twins 資源的唯讀存取權。

> [!TIP]
> Azure 數位 Twins 讀者（預覽）角色現在也支援流覽關聯性。

如需如何定義內建角色的詳細資訊，請參閱瞭解 Azure RBAC 檔中的[角色定義](../role-based-access-control/role-definitions.md)。 如需建立自訂 RBAC 角色的詳細資訊，請參閱[Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)。

您可以透過兩種方式來指派角色：
* 透過 Azure 入口網站中 Azure 數位 Twins 的 [存取控制（IAM）] 窗格（請參閱[使用 AZURE RBAC 新增或移除角色指派和 Azure 入口網站](../role-based-access-control/role-assignments-portal.md)）
* 透過 CLI 命令來新增或移除角色

如需如何執行這項操作的詳細步驟，請在[Azure 數位 Twins 教學課程：連接端對端解決方案](tutorial-end-to-end.md)中試試看。

## <a name="permission-scopes"></a>權限範圍

將 RBAC 角色指派給安全性主體之前，請先決定安全性主體應該具備的存取範圍。 最佳做法規定最好只授與最少的可能範圍。

下列清單說明您可以將 Azure 數位 Twins 資源的存取範圍限定于哪些層級。
* 模型：此資源的動作會指示對 Azure 數位 Twins 中上傳的[模型](concepts-models.md)進行控制。
* 查詢數位 Twins 圖形：此資源的動作會決定在 Azure 數位 Twins 圖形內對數位 Twins 執行[查詢作業](concepts-query-language.md)的能力。
* 數位對應項：此資源的動作可讓您控制對應項圖形中[數位 twins](concepts-twins-graph.md)的 CRUD 作業。
* 數位對應項關聯性：此資源的動作會針對對應項圖形中的數位 twins 之間的[關聯](concepts-twins-graph.md)性定義 CRUD 作業的控制。
* 事件路由：此資源的動作會決定將事件從 Azure 數位 Twins[路由傳送](concepts-route-events.md)至端點服務（例如[事件中樞](../event-hubs/event-hubs-about.md)、[事件方格](../event-grid/overview.md)或[服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)）的許可權。

## <a name="next-steps"></a>後續步驟

* 請參閱 how to [：驗證用戶端應用程式](how-to-authenticate-client.md)中的範例用戶端應用程式，以瞭解如何逐步執行這些步驟。

* 深入瞭解[適用于 Azure 的 RBAC](../role-based-access-control/overview.md)。
