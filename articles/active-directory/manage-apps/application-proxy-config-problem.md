---
title: 建立應用程式 Proxy 應用程式時發生問題 | Microsoft Docs
description: 如何針對在 Azure AD 管理入口網站中建立應用程式 Proxy 應用程式時所發生的問題進行疑難排解
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104b98cba0948ec5d0896877e54eab1e7cd4049f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "65825809"
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

## <a name="next-steps"></a>後續步驟
[在 Azure 入口網站中啟用應用程式 Proxy](application-proxy-add-on-premises-application.md)
