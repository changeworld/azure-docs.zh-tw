---
title: 使用者預配到 Azure AD 庫應用需要數小時或更長時間
description: 如何查明佈建至應用程式為什麼比您預期的更久
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22d19733fbeb162fba55dd732c10e552879c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522640"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>將使用者佈建至 Azure AD 資源庫應用程式花費數小時以上

首次為應用程式啟用自動預配時，初始週期可能需要 20 分鐘到幾個小時，具體取決於 Azure AD 目錄的大小和預配範圍內的使用者數。 

初始週期之後的後續同步速度更快，因為預配服務存儲的浮水印，表示初始週期後兩個系統的狀態，從而提高後續同步的性能。

## <a name="how-to-improve-provisioning-performance"></a>如何改善佈建的效能

如果初始週期需要幾個小時以上，您可以執行一項改進性能操作：

-   **使用者範圍設定篩選條件。** 範圍設定篩選條件可讓您根據特定的屬性值篩選出使用者，以微調佈建服務從 Azure AD 擷取的資料。 如需範圍設定篩選條件的詳細資訊，請參閱[含範圍篩選器的屬性型應用程式佈建](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="next-steps"></a>後續步驟
[使用 Azure 活動目錄自動將使用者預配和取消預配到 SaaS 應用程式](user-provisioning.md)

