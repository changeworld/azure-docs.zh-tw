---
title: 升級 Azure 備份代理程式
description: 這本資訊說明您應該升級 Azure 備份代理程式的原因，以及下載升級的位置。
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197110"
---
## <a name="upgrade-the-mars-agent"></a>升級 MARS 代理程式

2\.0.9083.0 版以下的 Microsoft Azure 復原服務（MARS）代理程式版本與 Azure 存取控制服務有相依性。 MARS 代理程式也稱為 Azure 備份代理程式。

在2018中，Microsoft 已[淘汰 Azure 存取控制服務](../articles/active-directory/azuread-dev/active-directory-acs-migration.md)。 自2018年3月19日起，下列2.0.9083.0 版的所有 MARS 代理程式版本都會遇到備份失敗。 若要避免或解決備份失敗，請[將 MARS 代理程式升級至最新版本](https://go.microsoft.com/fwlink/?linkid=229525)。 若要識別需要 MARS 代理程式升級的伺服器，請遵循[備份 blog 中用於升級 MARS 代理](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/)程式的步驟。

MARS 代理程式可用來將檔案和資料夾，以及系統狀態資料備份至 Azure。 System Center DPM 和 Azure 備份伺服器使用 MARS 代理程式將資料備份到 Azure。
