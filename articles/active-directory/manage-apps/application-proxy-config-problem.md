---
title: 建立應用程式 Proxy 應用程式時發生問題 | Microsoft Docs
description: 如何針對在 Azure AD 管理入口網站中建立應用程式 Proxy 應用程式時所發生的問題進行疑難排解
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2593f80db4c271d6ae4773f324cc9777e5400550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764956"
---
# <a name="problem-creating-an-application-proxy-application"></a>建立應用程式 Proxy 應用程式時發生問題 

以下是建立新的應用程式 Proxy 應用程式時會遇到的一些常見問題。

## <a name="recommended-documents"></a>建議的文件 

若要深入了解透過管理入口網站建立應用程式 Proxy 應用程式，請參閱[使用 Azure AD 應用程式 Proxy 發佈應用程式](application-proxy-add-on-premises-application.md)。

如果您依照該文件中的步驟，並在建立應用程式時發生錯誤，請查看錯誤詳細資料，以取得修正應用程式的資訊及建議。 大部分的錯誤訊息都包含建議的修正。 

## <a name="specific-things-to-check"></a>要檢查的特定事項

若要避免常見的錯誤，請確認：

-   您是具有建立應用程式 Proxy 應用程式權限的系統管理員

-   內部 URL 是唯一的

-   外部 URL 是唯一的

-   URL 開頭為 http 或 https，且結尾為 “/”

-   URL 應該是網域名稱，而非 IP 位址

當您建立應用程式時，錯誤訊息應該會在右上角顯示。 您也可以選取通知圖示來查看錯誤訊息。

   ![通知提示](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>接下來的步驟
[在 Azure 入口網站中啟用應用程式 Proxy](application-proxy-add-on-premises-application.md)
