---
title: XML 消息和一般檔案
description: 使用企業集成包在 Azure 邏輯應用中處理、驗證和轉換 XML 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792144"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>採用 Enterprise Integration Pack 的 Azure Logic Apps 中的 X12 訊息和一般檔案

在[Azure 邏輯應用中](logic-apps-overview.md)，可以使用企業集成包處理發送和接收的 XML 消息。 如果您使用了 BizTalk 伺服器，企業集成包提供了類似的功能來轉換和驗證郵件、處理一般檔案，甚至使用 XPath 來豐富或從郵件中提取特定屬性。 如果您是此空間的新增功能，這些功能將擴展邏輯應用工作流中處理消息的方式。 例如，如果您有企業對企業 （B2B） 方案並使用特定的 XML 架構，則可以使用企業集成包來增強公司處理這些消息的方式。

例如，企業集成包包括以下功能：

* [XML 驗證](logic-apps-enterprise-integration-xml-validation.md)：根據特定架構驗證傳入或傳出的 XML 消息。

* [XML 轉換](logic-apps-enterprise-integration-transform.md)：使用映射根據您的要求或合作夥伴的要求轉換或自訂 XML 消息。

* [一般檔案編碼和一般檔案解碼](logic-apps-enterprise-integration-flatfile.md)：編碼或解碼一般檔案。

  例如，SAP 接受並以一般檔案格式傳送 IDOC 檔案。 許多整合平台會建立 XML 訊息，包括邏輯應用程式。 因此，您可以建立使用一般檔案編碼器將 XML 檔案「轉換」為一般檔案的邏輯應用程式。

* [XPath](workflow-definition-language-functions-reference.md#xpath)： 豐富消息並從消息中提取特定屬性。 然後您可以使用擷取的屬性將訊息傳送至目的地或中繼端點。

## <a name="sample"></a>範例

通過使用 Azure 邏輯應用中的 XML 功能[部署完全運行的邏輯應用](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline)（GitHub 示例）。

## <a name="next-steps"></a>後續步驟

瞭解有關[企業集成包的更多](logic-apps-enterprise-integration-overview.md)
