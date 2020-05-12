---
title: 課堂實驗室概念-Azure 實驗室服務 |Microsoft Docs
description: 瞭解實驗室服務的基本概念，以及它如何讓您輕鬆地建立和管理實驗室。
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
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: a34af2ef779a41c19d4bfb831360948af1a66724
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115042"
---
# <a name="classroom-labs-concepts"></a>教室實驗室概念

下列清單包含重要的實驗室服務概念和定義：

## <a name="quota"></a>Quota

配額是老師可以設定供學生使用實驗室 VM 的時間限制（以小時為單位）。 可以設定為0，或指定的時數。 如果配額設定為0，只有在執行排程或老師手動開啟學生的虛擬機器時，學生才能使用虛擬機器。  

當學生自行啟動實驗室 VM 時，會計算配額時數。  如果老師手動啟動學生的實驗室 VM，該學生不會使用配額時數。

## <a name="schedules"></a>排程

排程是老師可以為類別建立的時間位置，讓學生 Vm 可供班級時間使用。  排程可以是一次性或週期性。  當排程正在執行時，不會使用配額時數。

排程的類型有三種： [標準]、[僅啟動] 和 [僅停止]。

- **標準**。  此排程會在指定的開始時間啟動所有學生 Vm，並在指定的停止時間關閉所有學生 Vm。
- **僅啟動**。   此排程會在指定的時間啟動所有學生 Vm。  學生 Vm 將不會停止，直到學生透過 Azure 實驗室服務入口網站停止其 VM，或發生「僅停止」排程。
- **僅停止**。  此排程將會在指定的時間停止所有學生 Vm。  

## <a name="template-virtual-machine"></a>範本虛擬機器

實驗室中的範本虛擬機器是基底虛擬機器映射，會從中建立所有使用者的虛擬機器。 訓練員/實驗室建立者會設定範本虛擬機器，並使用他們想要提供給訓練出席者以進行實驗室的軟體進行設定。 當您發佈範本 VM 時，Azure 實驗室服務會根據範本 VM 來建立或更新實驗室 Vm。

## <a name="user-profiles"></a>使用者設定檔

本文描述 Azure 實驗室服務中各種不同的使用者設定檔。

### <a name="lab-account-owner"></a>實驗室帳戶擁有者

一般來說，組織雲端資源的 IT 系統管理員（擁有 Azure 訂用帳戶）會作為實驗室帳戶擁有者，並執行下列工作：

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
