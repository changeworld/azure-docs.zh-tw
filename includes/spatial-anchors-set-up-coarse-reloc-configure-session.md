---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 41c0a6292bf7369a30fbb1e4bd474e70ffaedcb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999724"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>設定雲端空間錨點工作階段

接下來，我們將會負責設定雲端空間錨點工作階段。 在第一行中，我們會在工作階段上設定感應器提供者。 從現在開始，我們在工作階段期間建立的任何錨點都會與一組感應器讀數相關聯。 接下來，我們會將接近裝置的尋找準則具現化，並將其初始化以符合應用程式需求。 最後，我們會藉由從接近裝置的準則建立監看員，指示工作階段在尋找錨點時使用感應器資料。