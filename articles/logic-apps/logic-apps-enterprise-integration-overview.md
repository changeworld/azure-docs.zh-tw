---
title: B2B 企業整合
description: 了解如何使用 Azure Logic Apps 與企業整合套件來建置適用於企業整合的自動化 B2B 工作流程
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 9356cecb22672ae15505d14b5ac73d5baeef09e0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191364"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>搭配 Azure Logic Apps 與企業整合套件的 B2B 企業整合解決方案

如需企業對企業 (B2B) 解決方案，以及在組織間進行無接縫通訊，您可以使用企業整合套件 (EIP) 搭配 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)來建置可調整的自動化企業整合工作流程。 雖然組織使用不同的通訊協定與格式，但他們能以電子方式來交換訊息。 EIP 會將不同格式轉換成您組織系統可以處理，並且可支援業界標準通訊協定的格式，包括 [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md) 和 [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md)。 您也可以使用加密和數位簽章來改善訊息的安全性。 EIP 支援這些[企業整合連接器](../connectors/apis-list.md#integration-account-connectors)及下列產業標準：

* 電子資料交換 (EDI)
* 企業應用程式整合 (EAI)

如果您熟悉 Microsoft BizTalk Server 或 Azure BizTalk 服務，EIP 遵循的概念與之類似，為的是讓功能更容易使用。 但是，有一個主要差異是 EIP 以「整合帳戶」為基礎，目的是簡化 B2B 通訊中所使用構件的儲存與管理。 這些帳戶是雲端型容器，其中儲存您的所有構件，例如合作夥伴、合約、結構描述、對應與憑證。 

## <a name="why-use-the-enterprise-integration-pack"></a>為什麼使用企業整合套件？

* 透過 EIP，您可以在一個位置 (亦即您的整合帳戶) 儲存您的所有構件。

* 您可以使用 Azure Logic Apps 與連接器來建置 B2B 工作流程，並整合第三方軟體即服務 (SaaS) 應用程式、內部部署應用程式與自訂應用程式。

* 您可以使用 Azure 函式為您的邏輯應用程式建立自訂程式碼。

## <a name="how-do-i-get-started"></a>如何開始使用？

在您開始使用 EIP 建立 B2B 邏輯應用程式工作流程之前，您需要下列項目：

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* [整合帳戶](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，其中包含您要使用的構件

* 若要建立對應和結構描述，您可以使用[適用於 Visual Studio 2015 2.0 和 Visual Studio 2015 的 Microsoft Azure Logic Apps 企業整合工具](https://aka.ms/vsmapsandschemas)。

建立整合帳戶並新增構件之後，您就可以在 Azure 入口網站中建立邏輯應用程式，以開始使用這些構件來建立 B2B 工作流程。 如果您不熟悉邏輯應用程式，請嘗試[建立基本邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用這些構件，您必須先將您的整合帳戶連結到您的邏輯應用程式。 之後，您的邏輯應用程式就可以存取您的整合帳戶。 您也可以使用 Visual Studio 或 [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp) 來建立、管理及部署邏輯應用程式。

以下是開始建立 B2B 邏輯應用程式的概括步驟：

![建立 B2B 邏輯應用程式的必要條件](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>立即試用

[部署可完整運作的範例邏輯應用程式來傳送和接收 AS2 訊息](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>後續步驟

* [建立交易夥伴](logic-apps-enterprise-integration-partners.md)
* [建立合約](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [新增結構描述](logic-apps-enterprise-integration-schemas.md)
* [新增對應](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [從 BizTalk 服務移轉](../logic-apps/logic-apps-move-from-mabs.md)
