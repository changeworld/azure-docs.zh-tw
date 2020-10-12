---
title: 在 Azure Sentinel 中從警示建立事件 | Microsoft Docs
description: 了解如何在 Azure Sentinel 中從警示建立事件。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: fabf847b672f4dbf3e680a21bc7015655c612552
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906311"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>自動從 Microsoft 安全性警示建立事件

在連接到 Azure Sentinel 的 Microsoft 安全性解決方案（例如 Microsoft Cloud App Security 和適用于身分識別 (的 Microsoft Defender 之前 Azure ATP) ）所觸發的警示，請勿在 Azure Sentinel 中自動建立事件。 根據預設，當您將 Microsoft 解決方案連線到 Azure Sentinel 時，任何在該服務中產生的警示都會以原始資料的形式儲存在 Azure Sentinel 中 (在 Azure Sentinel 工作區的 [安全性警示] 資料表中)。 然後您可以使用該資料，就像您連線到 Sentinel 中的任何其他原始資料一樣。

您可以遵循本文中的指示，輕鬆地將 Azure Sentinel 設定為每次在已連線 Microsoft 安全性解決方案中觸發警示時自動建立事件。

## <a name="prerequisites"></a>必要條件
您必須[連線 Microsoft 安全性解決方案](connect-data-sources.md#data-connection-methods)，才能從安全性服務警示啟用事件建立。

## <a name="using-microsoft-security-incident-creation-analytics-rules"></a>使用 Microsoft 安全性事件建立分析規則

使用 Azure Sentinel 中提供的內建規則，選擇哪些已連線的 Microsoft 安全性解決方案應該即時自動建立 Azure Sentinel 事件。 您也可以編輯這些規則來定義更具體的選項，以便篩選 Microsoft 安全性解決方案所產生的哪些警示應該在 Azure Sentinel 中建立事件。 例如，您可以選擇只從高嚴重性的 Azure Defender (先前 Azure 資訊安全中心) 警示，自動建立 Azure Sentinel 事件。

1. 在 Azure 入口網站的 [Azure Sentinel] 底下，選取 [分析]。

1. 選取 [ **規則範本** ] 索引標籤，以查看所有內建分析規則。

    ![應用程式範本](media/incidents-from-alerts/rule-templates.png)

1. 選擇您要使用的 [Microsoft 安全性] 分析規則範本，然後按一下 [建立規則]。

    ![安全性分析規則](media/incidents-from-alerts/security-analytics-rule.png)

1. 您可以修改規則詳細資料，然後選擇篩選將會依警示嚴重性或依警示名稱內含文字建立事件的警示。  
      
    例如，如果您選擇 [ **Azure defender** (可能仍會被稱為 [ **Microsoft Security Service** ] 欄位中的 [ *Azure 資訊安全中心*) ]，然後在 [**依嚴重性篩選**] 欄位中選擇 [**高**]，只有高嚴重性的 Azure Defender 警示會自動在 Azure Sentinel 中建立事件。  

    ![建立規則精靈](media/incidents-from-alerts/create-rule-wizard.png)

1. 按一下 [+建立]，然後選取 [Microsoft 事件建立規則]，也可以建立新的 [Microsoft 安全性] 規則，以篩選來自不同 Microsoft 安全性服務的警示。

    ![事件建立規則](media/incidents-from-alerts/incident-creation-rule.png)

  您可以為每個**microsoft 安全性服務**類型建立一個以上的**microsoft 安全性**分析規則。 這不會建立重複的事件，因為每個規則都當作篩選條件使用。 即使警示符合一個以上的 **Microsoft 安全性** 分析規則，它只會建立一個 Azure Sentinel 事件。

## <a name="enable-incident-generation-automatically-during-connection"></a>在連線期間自動啟用事件產生
 當您連線到 Microsoft 安全性解決方案時，您可以選擇是否要讓來自安全性解決方案的警示自動在 Azure Sentinel 中自動產生事件。

1. 連線 Microsoft 安全性解決方案資料來源。 

   ![產生安全性事件](media/incidents-from-alerts/generate-security-incidents.png)

1. 在 [ **建立事件** ] 底下，選取 [ **啟用** ] 以啟用預設分析規則，此規則會自動從已連線的安全性服務中產生的警示建立事件。 接著，您可以在 [分析] 下編輯此規則，然後編輯 [有效規則]。

## <a name="next-steps"></a>後續步驟

- 若要開始使用 Azure Sentinel，您需要 Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/free/)。
- 了解如何[將資料上架到 Azure Sentinel](quickstart-onboard.md)，並[掌握您的資料和潛在威脅](quickstart-get-visibility.md)。
