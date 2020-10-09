---
title: 教室實驗室概念 - Azure Lab Services | Microsoft Docs
description: 了解 Lab Services 的基本概念，以及其如何讓您輕鬆地建立和管理實驗室。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 38dd77df7a80ad252b553b6afa8b52d7fee753a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85443701"
---
# <a name="classroom-labs-concepts"></a>教室實驗室概念

下列清單包含重要的 Lab Services 概念和定義：

## <a name="quota"></a>Quota

配額是一種時間限制 (以小時為單位)，教師可針對學生設定以使用實驗室 VM。 此值可設為 0 或特定時數。 如果配額設為 0，學生只能在執行排程時，或教師手動為學生開啟虛擬機器時，使用虛擬機器。  

當學生自行啟動實驗室 VM 時，就會計算配額時數。  如果教師手動為學生啟動實驗室 VM，該學生不會使用配額時數。

## <a name="schedules"></a>排程

排程是教師可為課程建立的時段，讓學生 VM 可供課程時間使用。  排程可以是一次性或週期性。  當排程正在執行時，不會使用配額時數。

排程有三種類型：[標準]、[僅啟動] 和 [僅停止]。

- **標準**。  此排程會在指定的開始時間啟動所有學生 VM，並在指定的停止時間關閉所有學生 VM。
- **僅啟動**。   此排程會在指定的時間啟動所有學生 VM。  直到學生透過 Azure Lab Services 入口網站或「僅停止」排程停止其 VM，學生 VM 才會停止。
- **僅停止**。  此排程將會在指定的時間停止所有學生 VM。  

## <a name="template-virtual-machine"></a>範本虛擬機器

實驗室中的範本虛擬機器是所有使用者的虛擬機器據以建立的基底虛擬機器映像。 訓練員/實驗室建立者會設定範本虛擬機器，並使用他們想提供給訓練出席者進行實驗的軟體進行設定。 當您發佈範本 VM 時，Azure Lab Services 會根據範本 VM 建立或更新實驗室 VM。

## <a name="user-profiles"></a>使用者設定檔

本文描述 Azure 實驗室服務中各種不同的使用者設定檔。

### <a name="lab-account-owner"></a>實驗室帳戶擁有者

通常是擁有 Azure 訂用帳戶的組織雲端資源 IT 系統管理員，並會執行下列工作：

- 為組織設定實驗室帳戶。
- 管理並設定所有實驗室的原則。
- 給予組織中人員在實驗室帳戶下建立實驗室的權限。

### <a name="educator"></a>教育工作者

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
