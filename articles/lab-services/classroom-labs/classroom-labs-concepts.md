---
title: 課堂實驗室概念 - Azure 實驗室服務 |微軟文檔
description: 瞭解實驗室服務的基本概念，以及如何輕鬆創建和管理實驗室。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: spelluru
ms.openlocfilehash: 15fd3c18d059466c2b2bd5e2431013f393092b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526224"
---
# <a name="classroom-labs-concepts"></a>教室實驗室概念

以下清單包含關鍵的實驗室服務概念和定義：

## <a name="quota"></a>Quota

配額是教師可以為學生設置使用實驗室 VM 的時間限制（以小時計）。 它可以設置為 0，也可以設置為特定小時數。 如果配額設置為 0，則學生只能在計畫運行時或教師手動打開學生的虛擬機器時使用虛擬機器。  

當學生自行啟動實驗室 VM 時，將計算配額小時數。  如果教師手動啟動學生的實驗 VM，則該學生不會使用配額小時。

## <a name="schedules"></a>排程

計畫是教師可以為班級創建的時間槽，以便學生 VM 可用於上課時間。  計畫可以是一次性的，也可以是週期性。  計畫運行時不使用配額小時。

有三種類型的計畫：標準計畫、僅開始計畫僅停止計畫。

- **標準**。  此計畫將在指定的開始時間啟動所有學生 VM，並在指定的停止時間關閉所有學生 VM。
- **僅開始**。   此計畫將在指定時間啟動所有學生 VM。  學生 VM 不會停止，直到學生通過 Azure 實驗室服務門戶停止其 VM 或僅執行停止計畫。
- **僅停止**。  此計畫將在指定時間停止所有學生 VM。  

## <a name="template-virtual-machine"></a>範本虛擬機器

實驗室中的範本虛擬機器是創建所有使用者虛擬機器的基本虛擬機器映射。 培訓師/實驗室建立者設置範本虛擬機器，並將其配置到他們想要提供給培訓學員進行實驗室操作的軟體中。 發佈範本 VM 時，Azure 實驗室服務會基於範本 VM 創建或更新實驗室 VM。

## <a name="user-profiles"></a>使用者設定檔

本文描述 Azure 實驗室服務中各種不同的使用者設定檔。

### <a name="lab-account-owner"></a>實驗室帳戶擁有者

通常會是擁有 Azure 訂用帳戶的組織雲端資源 IT 系統管理員，並會執行下列工作：

- 為組織設定實驗室帳戶。
- 管理並設定所有實驗室的原則。
- 給予組織中人員在實驗室帳戶下建立實驗室的權限。

### <a name="professor"></a>教授

通常，教師或線上訓練人員等使用者會在實驗室帳戶下建立教室實驗室。 教育工作者會執行下列工作：

- 建立教室實驗室。
- 在實驗室中建立虛擬機器。
- 在虛擬機器上安裝適當的軟體。
- 指定可以存取實驗室的人員。
- 為學生提供實驗室的註冊連結。

### <a name="student"></a>學生

學生會執行下列工作：

- 使用實驗室使用者從實驗室建立者處收到的註冊連結來註冊至實驗室。
- 連線至實驗室中的虛擬機器，並用它來執行課堂作業、指定作業和專案。

## <a name="next-steps"></a>後續步驟

開始使用 Azure 實驗室服務設定建立教室實驗室所需的實驗室帳戶：

- [設定實驗室帳戶](tutorial-setup-lab-account.md)
