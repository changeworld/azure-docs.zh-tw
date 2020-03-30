---
title: 在 Azure DevTest Labs 中將標記新增到實驗室 | Microsoft Docs
description: 了解如何在 Azure DevTest Labs 中將標記新增到實驗室
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 1d7175d000eebd3f68665e7f097bbe3f68025bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302792"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中將標記新增到實驗室

您可以建立自訂標記並套用到 DevTest Labs 資源，以利用邏輯方式分類資源。 之後，您可以快速而輕鬆地查看訂用帳戶中所有加上標記的資源。 當您需要整理資源以進行計費或管理時，標記就能發揮功用。

支援標記的資源包括

* 計算 VM
* NIC
* IP 位址
* 負載平衡器
* 儲存體帳戶
* 受控磁碟

您可以在[建立實驗室](devtest-lab-create-lab.md)時套用標記，稍後再透過 [組態和設定] 下方的 [標記] 刀鋒視窗進行管理。

每個標記由**名稱**/**值**對組成。 例如，您可以建立名稱為 *costcenter*，值為 *34543* 的標記。 這類標記能協助您在日後辨識實驗室資源，以便向組織的此特定領域收費。 您可以選擇方便讓自己整理訂用帳戶的名稱和值。

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>在現有實驗室中管理標記的步驟

1. 登錄到 Azure[門戶](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 視需要選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。 您的實驗室可能已經顯示在 **"所有資源**"下的儀表板上。
1. 從實驗室清單中，選取您想要新增或管理標記的實驗室。
1. 在實驗室的 [概觀]**** 區域中選取 [組態和原則]****。

    ![組態和原則按鈕](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. 在左側的 [管理] 下方**** 選取 [標記]****。
1. 要為此實驗室創建新標記，請輸入**名稱**/**值**對並選擇 **"保存**"。 您也可以從清單選取現有標記，以便檢視或管理與該標記相關的資源。

    ![管理標記](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> 在實驗室級別創建的標記流經實驗室在訂閱中旋轉的所有計費資源。 例如，實驗室級標記流向實驗室 VM 的基礎計算 VM。 實驗室級別標記顯示在成本管理的標籤篩選器中。

## <a name="understanding-limitations-to-tags"></a>了解標記的限制

標籤具有下列限制：

* 每個資源或資源群組最多都可以有 15 個標記名稱/值組。 此限制只適用於直接套用至資源群組或資源的標記。 資源群組可以包含許多資源，其各自有 15 個標記名稱/值組。
* 標記名稱上限為 512 個字元，且標記值上限為 256 字元。 儲存體帳戶的標記名稱上限為 128 個字元，且標記值上限為 256 個字元。
* 資源群組中的資源不會繼承套用至該資源群組的標籤。

[使用標記來組織 Azure 資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)提供在 Azure 中使用標記的更多詳細資料，包括如何使用 PowerShell 或 Azure CLI 管理標記。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>後續步驟
* 您可以使用自訂原則，在訂用帳戶內套用限制和慣例。 您所定義的原則可能需要所有資源都具有特定標籤的值。 如需詳細資訊，請參閱[設定原則和排程](devtest-lab-set-lab-policy.md)。
* 流覽[DevTest 實驗室 Azure 資源管理器快速入門範本庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)。
