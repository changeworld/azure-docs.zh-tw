---
title: 在 Azure 通訊服務上開始使用 Teams 互通性
titleSuffix: An Azure Communication Services quickstart
description: 在本快速入門中，您將了解如何使用 Azure 通訊的聊天用戶端程式庫來加入 Teams 會議
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ea66e4295e8228aa382aa29a46fcca8147dcbc98
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577997"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>快速入門：將聊天應用程式加入至 Teams 會議

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

使用 JavaScript 用戶端程式庫，將您的聊天解決方案連線到 Microsoft Teams，以開始使用 Azure 通訊服務。 

## <a name="prerequisites"></a>Prerequisites 

1.  [Teams 部署](https://docs.microsoft.com/deployoffice/teams-install)。 
2. 作用中的[聊天應用程式](./get-started.md)。 

## <a name="enable-teams-interoperability"></a>啟用 Teams 互通性 

以來賓使用者身分加入 Teams 會議的通訊服務使用者，只有在加入 Teams 會議通話時，才能存取會議的交談內容。 請參閱 [Teams 互通性](../voice-video-calling/get-started-teams-interop.md)文件，以了解如何將通訊服務使用者新增至 Teams 會議通話。

Teams 互通性功能目前為個人預覽版。 若要為您的通訊服務資源啟用這項功能，請傳送電子郵件到 acsfeedback@microsoft.com，並附上： 
1. 通訊服務資源所在 Azure 訂用帳戶的訂用帳戶識別碼。 
2. Teams 租用戶識別碼。 若要取得此識別碼，最簡單的方式是取得並分享小組的連結。 

您必須是這兩個實體的擁有組織成員，才能使用這項功能。 

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除通訊服務訂閱，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。 深入了解如何[清除資源](../create-communication-resource.md#clean-up-resources)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列文章：

- 查看我們的[聊天 Hero 範例](../../samples/chat-hero-sample.md)
- 深入了解[聊天的運作方式](../../concepts/chat/concepts.md)
