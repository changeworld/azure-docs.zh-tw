---
title: 連接到 Office 365 影片
description: 使用 Azure Logic Apps 自動化在 Office 365 中管理影片的作業和工作流程
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75665780"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>使用 Azure Logic Apps 管理 Office365 Video 中的影片

連接至 Office 365 影片，以取得 Office 365 影片的相關資訊、影片清單等。 有了 Office 365 影片，您可以：

* 根據您從 Office 365 影片所取得的資料，來建置您的商務流程。 

* 檢查影片入口網站狀態、取得頻道影片清單等動作。 這些動作會收到回應，然後輸出能讓其他動作使用的資料。 

例如，您可以使用 Bing 搜尋連接器來搜尋 Office 365 影片，然後使用 Office 365 影片連接器取得該影片的相關資訊。 如果影片符合您的需求，您可以將該影片張貼在 Facebook 上。

您現在可以開始建立邏輯應用程式，請參閱[建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="connect-to-office365-video"></a>連接到 Office365 影片

當您將這個連接器新增到邏輯應用程式時，您必須登入您的 Office 365 影片帳戶，並允許邏輯應用程式連線到您的帳戶。

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

當您建立連線之後，請輸入 Office 365 影片的屬性，例租用戶名稱或頻道識別碼。 

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/office365videoconnector/)的所有限制。

## <a name="next-steps"></a>後續步驟

* 了解其他 [Logic Apps 連接器](../connectors/apis-list.md)