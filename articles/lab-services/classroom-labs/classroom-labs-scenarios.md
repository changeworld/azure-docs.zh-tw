---
title: 使用課堂實驗室進行培訓 - Azure 實驗室服務
description: 本文介紹如何使用 Azure 開發人員測試實驗室在 Azure 上為訓練方案創建實驗室。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 155806222f9e11fec177487b7147d81054ac06ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717995"
---
# <a name="use-classroom-labs-for-trainings"></a>使用課堂實驗室進行培訓
您可以設置一個培訓實驗室。 Azure 實驗室服務的課堂實驗室允許您為培訓創建實驗室，其中每個學員使用相同和隔離的環境進行培訓。 您可以新增原則，以確保訓練環境只會在受訓者需要時才提供給他們使用，且包含足夠的訓練所需資源，例如虛擬機器。 

![課堂實驗室](../media/classroom-labs-scenarios/classroom.png)

課堂實驗室滿足在任何虛擬環境中進行培訓所需的以下要求： 

- 受訓者可以快速佈建其訓練環境
- 每台訓練用機器應該相同
- 受訓者無法看到其他受訓者所建立的 VM
- 確保受訓者無法取得超過訓練所需數量的 VM 以控制成本，並在受訓者不使用 VM 時將其關閉
- 輕易地與每位受訓者共用訓練實驗室
- 一再重複使用訓練實驗室

在本文中，您將瞭解各種 Azure 實驗室服務功能，這些功能可用於滿足前面描述的培訓要求，以及為設置培訓實驗室而可以遵循的詳細步驟。  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>以實驗室帳戶管理員身份創建實驗室帳戶
使用 Azure 實驗室服務的第一步是在 Azure 門戶中創建實驗室帳戶。 實驗室帳戶管理員創建實驗室帳戶後，管理員會將希望創建實驗室的使用者添加到**實驗室建立者**角色。 培訓師創建帶有虛擬機器的實驗室，供學生為其所教授的課程進行練習。 有關詳細資訊，請參閱[創建和管理實驗室帳戶](how-to-manage-lab-accounts.md)。

## <a name="create-and-manage-classroom-labs"></a>建立和管理教室實驗室
培訓師是實驗室帳戶中實驗室建立者角色的成員，可以在實驗室帳戶中創建一個或多個實驗室。 使用在課程中執行練習所需的所有軟體創建和配置範本 VM。 從可用映射中選擇現成的映射以創建教室實驗室，然後通過安裝實驗室所需的軟體對其進行自訂。 有關詳細資訊，請參閱[創建和管理教室實驗室](how-to-manage-classroom-labs.md)。

## <a name="configure-usage-settings-and-policies"></a>設定使用方式設定和原則
實驗室建立者可以將使用者添加到實驗室，獲取註冊連結以發送給實驗室使用者，設置策略，如設置每個使用者的單個配額、更新實驗室中可用的 VM 數量等。 有關詳細資訊，請參閱[配置使用設置和策略](how-to-configure-student-usage.md)。

## <a name="create-and-manage-schedules"></a>建立和管理排程
排程可讓您設定教室實驗室，使實驗室中的 VM 可在指定的時間自動啟動和關閉。 您可以定義一次性排程或週期性排程。 有關詳細資訊，請參閱[創建和管理教室實驗室的時程表](how-to-create-schedules.md)。

## <a name="set-up-and-publish-a-template-vm"></a>設置和發佈範本 VM
實驗室中的範本是所有使用者的虛擬機器據以建立的基底虛擬機器映像。 設置範本 VM，以便它完全使用要提供給培訓參與者的內容進行配置。 您可以提供要給實驗室使用者查看的範本名稱與描述。 然後，發佈範本以便讓範本 VM 的執行個體可供實驗室使用者使用。 當您發佈範本時，Azure 實驗室服務會使用範本在實驗室中建立 VM。 在此程序中建立的 VM 數目與實驗室中允許的使用者數量上限相同 (此上限可在實驗室的使用原則中設定)。 所有虛擬機器都有與範本相同的設定。 有關詳細資訊，請參閱[設置和發佈範本虛擬機器](how-to-create-manage-template.md)。 

## <a name="use-vms-in-the-classroom-lab"></a>在教室實驗室中使用 VM
學生或培訓學員註冊到實驗室，並連接到 VM 為課程執行練習。 有關詳細資訊，請參閱[如何訪問教室實驗室](how-to-use-classroom-lab.md)。

## <a name="next-steps"></a>後續步驟
首先，在課堂實驗室中創建實驗室帳戶，請按照本文中的說明：[教程：使用 Azure 實驗室服務設置實驗室帳戶](tutorial-setup-lab-account.md)。