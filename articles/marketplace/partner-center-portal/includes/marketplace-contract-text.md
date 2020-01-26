---
title: 包含檔案
description: 包含 Microsoft 商業 marketplace 標準合約的文字檔
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: ChJenk
manager: evansma
editor: ''
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/22/2020
ms.author: v-chjen
ms.custom: include file
ms.openlocfilehash: 76603ef30cefa14018a7c6b976eb8541b9b608f4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76760878"
---
Microsoft 為商業 marketplace 提供標準合約範本。

- **要使用 Microsoft 商業 marketplace 的標準合約嗎？**

為了簡化客戶的採購程式，並降低軟體廠商的法律複雜度，Microsoft 為 Microsoft 商業市場提供了標準合約，協助加速 marketplace 中的交易。 商業 marketplace 發行者可以選擇在標準合約下提供其軟體，而不是製作自訂的條款及條件，而客戶只需要審查並接受一次。 您可以在這裡找到標準合約： https://go.microsoft.com/fwlink/?linkid=2041178 。

選取 [使用商業 marketplace 的標準合約] 核取方塊，即可選擇使用標準合約，而不是提供您自己的自訂條款及條件。

![使用 [標準合約] 核取方塊](./media/use-standard-contract.png)

> [!NOTE]
> 當您使用 Microsoft 商業 marketplace 的標準合約發佈供應專案之後，就無法使用自己的自訂條款及條件。 這是「或」案例。 您可以根據標準合約**或**您自己的條款及條件來提供您的解決方案。 如果您想要修改標準合約的條款，您可以透過標準合約修訂來執行此動作。

**標準合約修訂**

標準合約修訂可讓發行者針對簡單性選取標準合約條款，並自訂其產品或企業的條款。 如果客戶已審查並接受 Microsoft 標準合約，則只需要審查合約的修訂。

商用 marketplace 發行者有兩種可用的改正：

- 通用修訂：這些修訂通用適用于所有客戶的標準合約。 在購買流程中，每個供應專案的客戶都會看到通用修訂。 客戶必須先接受標準合約和修訂條款，才能使用您的供應專案。
- 自訂改正：這些修訂是標準合約的特殊修訂，僅透過 Azure 租使用者識別碼以特定客戶為目標。 發行者可以選擇想要設為目標的租使用者。 只有租使用者的客戶才會看到供應專案購買流程中的自訂修訂條款。  客戶必須先接受標準合約和修訂條款，才能使用您的供應專案。

>[!NOTE]
> 這兩種類型的改正堆疊在彼此之上。 以自訂修訂為目標的客戶，也會在購買期間取得標準合約的通用修訂。

**Microsoft 商業 Marketplace 標準合約的通用修訂條款**：請在此方塊中輸入通用修訂條款。 您可以提供每個供應專案的單一通用修訂。 您可以在此方塊中輸入不限數目的字元。 這些條款會向客戶顯示在探索和購買流程期間的 AppSource、Azure Marketplace 和/或 Azure 入口網站。

**Microsoft 商業 Marketplace 標準合約的自訂修訂條款**：從選取 [**新增自訂修訂條款**] 開始。 每個供應專案最多可提供10個自訂修訂條款。

- **自訂修訂條款**：在 [自訂修訂條款] 方塊中輸入您的自訂修訂條款。 您可以在此方塊中輸入不限數目的字元。 只有來自您為這些自訂條款指定之租使用者識別碼的客戶，才會在 Azure 入口網站中提供供應專案購買流程中的自訂修訂條款。  
- **租使用者識別碼**（必要）：每個自訂修訂的目標最多可達20個租使用者識別碼。 如果您新增自訂修訂，則必須至少提供一個租使用者識別碼。 租使用者識別碼會識別您在 Azure 中的客戶。 您可以向您的客戶要求此識別碼，他們可以流覽至 portal.azure.com > Azure Active Directory > 內容來找到它。 目錄識別碼值是租使用者識別碼（例如，50c464d3-4930-494c-963c-1e951d15360e）。 您也可以在 [[我的 Microsoft Azure 和 Office 365 租使用者識別碼為何？](https://www.whatismytenantid.com)] 中，使用其功能變數名稱 URL 來查詢組織的租使用者識別碼。
- **描述**（選擇性）：選擇性地提供租使用者識別碼的易記描述，以協助您識別目標為修訂的客戶。

**條款及條件**

如果您想要提供自己的自訂條款及條件，可以選擇在 [條款及條件] 欄位中輸入它們。 您最多可以在此欄位中輸入10000個字元的文字。 如果您的條款及條件需要較長的描述，請在此欄位中輸入單一 URL 連結，以找到您的條款及條件。 它會向客戶顯示為作用中連結。

客戶必須接受這些條款，才能試用您的供應專案。

請記得先**儲存**再繼續進行下一節。
請記得先**儲存**再繼續進行下一節。
