---
title: 包含檔案
description: 包含檔案
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943887"
---
## <a name="object-model"></a>物件模型

使用者存取權杖可讓您授權應用程式的使用者直接連線到您的 Azure 通訊服務資源。 您會在伺服器上產生這些權杖，將其傳遞回用戶端應用程式，然後用來初始化通訊服務用戶端程式庫。 若要達到此目的，您應該建置可驗證使用者的信任服務端點，並發行使用者存取權杖。 此服務應維護應用程式使用者身分識別與 Azure 通訊服務使用者之間的持續性對應。

CommunicationIdentityClient 類別提供所有使用者和存取權杖的相關功能。 您可以使用連接字串將其具現化，然後使將其用來管理使用者，並發行存取權杖。

> [!WARNING]
> 權杖是敏感性資料，因為它們會授與使用者資源的存取權限。 因此，務必保護權杖不遭洩漏。 您應該建立一個信任的服務端點，只向您應用程式的授權使用者發行存取權杖。 如果您將使用者存取權杖快取到備份存放區，則強烈建議您使用加密。

### <a name="access-token-scopes"></a>存取權杖範圍

範圍可讓您指定使用者存取權杖將能夠授權的確切 Azure 通訊服務功能。 範圍會套用至個別的使用者存取權杖。 Azure 通訊服務支援下列使用者存取權杖範圍：

| Name   | 描述                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | 授與參與聊天的能力                                         |
| `VoIP` | 授與使用通話用戶端程式庫進行和接收 VOIP 通話的能力* |
| `Pstn` | 授與使用通話用戶端程式庫進行 PSTN 通話的能力*           |

\*尚不支援功能，即將推出

如果您想要移除使用者存取某些特定功能的能力，您應該先[撤銷任何現有的存取權杖](#revoke-user-access-tokens) (這些存取權杖可能包含不想要的範圍)，然後再以一組較有限制的範圍來發行新權杖。
