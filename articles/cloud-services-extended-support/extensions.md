---
title: '適用于雲端服務的擴充功能 (延伸支援) '
description: '適用于雲端服務的擴充功能 (延伸支援) '
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c2eddf75f99b751773220ae677d66fe8c09abb0e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744360"
---
# <a name="extensions-for-cloud-services-extended-support"></a>適用于雲端服務的擴充功能 (延伸支援) 

擴充功能是小型的應用程式，可在角色上提供部署後設定和自動化工作。 例如，您可以使用遠端桌面延伸模組，在雲端服務 (擴充支援) 部署期間，啟用角色中的遠端桌面連線。  

## <a name="remote-desktop-extension"></a>遠端桌面擴充功能

遠端桌面可讓您存取 Azure 內執行中角色的桌面。 您可以使用遠端桌面連線，在應用程式執行時對其進行疑難排解和診斷問題。

您可以在開發期間在您的角色中啟用遠端桌面連線，方法是在您的服務定義中包含遠端桌面模組，或透過遠端桌面擴充功能。 

如需詳細資訊，請參閱[從 Azure 入口網站設定遠端桌面](enable-rdp.md)。

## <a name="windows-azure-diagnostics-extension"></a>Windows Azure 診斷擴充功能

您可以監視任何雲端服務的關鍵效能計量。 每個雲端服務角色都會收集最少資料：CPU 使用量、網路使用量和磁碟使用狀況。 如果雲端服務已將 Azure 診斷延伸模組套用至某個角色，該角色就可以收集額外的資料點。 

使用基本監視，角色執行個體的效能計數器資料會依 3 分鐘的間隔進行取樣和收集。 此基本監視資料不會儲存在儲存體帳戶中，而且沒有任何與其建立關聯的額外成本。 

使用進階監視，其他計量會依 5 分鐘、1 小時和 12 小時的間隔進行取樣和收集。 彙總的資料會儲存在儲存體帳戶和資料表中，並會在 10 天之後予以清除。 所使用的儲存體帳戶是依角色所設定；您可以對不同的角色使用不同的儲存體帳戶。 

您可以透過[PowerShell](deploy-powershell.md)或[ARM 範本](deploy-template.md)，為雲端服務啟用 (延伸支援) 的 Windows Azure 診斷擴充功能


## <a name="next-steps"></a>後續步驟 
- 請參閱雲端服務的 [部署必要條件](deploy-prerequisite.md) (延伸支援) 。
- 請 [參閱雲端服務的常見問題，](faq.md) (延伸支援) 。
- 使用 [Azure 入口網站](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [範本](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)，將雲端服務部署 (延伸支援) 。