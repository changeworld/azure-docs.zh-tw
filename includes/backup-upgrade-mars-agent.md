---
title: 升級 Azure 備份代理程式
description: 此資訊解釋了為什麼應升級 Azure 備份代理，以及下載升級的位置。
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673202"
---
## <a name="upgrade-the-mars-agent"></a>升級 MARS 代理

低於 2.0.9083.0 的 Microsoft Azure 恢復服務 （MARS） 代理版本依賴于 Azure 存取控制服務。 MARS 代理也稱為 Azure 備份代理。

2018 年，微軟[棄用 Azure 存取控制服務](../articles/active-directory/azuread-dev/active-directory-acs-migration.md)。 從 2018 年 3 月 19 日開始，低於 2.0.9083.0 的所有版本的 MARS 代理都將遇到備份故障。 為了避免或解決備份故障，[請將 MARS 代理升級到最新版本](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent)。 要標識需要 MARS 代理升級的伺服器，請按照[升級 Microsoft Azure 恢復服務 （MARS） 代理](../articles/backup/upgrade-mars-agent.md)中的步驟操作。

MARS 代理用於將檔和資料夾以及系統狀態資料備份到 Azure。 系統中心 DPM 和 Azure 備份伺服器使用 MARS 代理將資料備份到 Azure。
