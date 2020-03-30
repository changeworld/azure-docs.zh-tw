---
title: 管理 Azure 伺服器場中的使用者
description: 本文介紹如何在 Azure FarmBeats 中管理使用者。
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 6ccc29422c6abf1120d13c05d10125efd0871ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502059"
---
# <a name="manage-users"></a>管理使用者

Azure FarmBeats 包括 Azure 活動目錄 （Azure AD） 實例一部分的使用者的使用者管理。 您可以將使用者添加到 Azure FarmBeats 實例以訪問 API、查看生成的地圖以及從伺服器場訪問感應器遙測。

## <a name="prerequisites"></a>Prerequisites

- 需要安裝 Azure 伺服器場節拍。 有關詳細資訊，請參閱安裝[Azure 伺服器場節拍](install-azure-farmbeats.md)。
- 要從 Azure FarmBeats 實例中添加或刪除的使用者的電子郵件標識。

## <a name="manage-azure-farmbeats-users"></a>管理 Azure 伺服器場使用者

Azure FarmBeats 使用 Azure AD 進行身份驗證、存取控制和角色。 您可以將 Azure AD 租戶中的使用者添加為 Azure FarmBeats 中的使用者。

> [!NOTE]
> 如果使用者不是 Azure AD 租戶使用者，請按照 **"添加 Azure AD 使用者"** 部分中的說明完成設置。

Azure 伺服器場節拍支援兩種類型的使用者角色：

 - **管理員**：完全訪問 Azure 伺服器場資料資料集 API。 此角色中的使用者可以查詢所有 Azure FarmBeats Datahub 物件，並從伺服器場節拍加速器執行所有操作。
 - **唯讀**：對伺服器場節拍資料集 API 的唯讀訪問。 使用者可以查看 Datahub API、加速器儀表板和地圖。 具有唯讀存取權限的使用者無法執行生成地圖、關聯設備或創建伺服器場等操作。

## <a name="add-users-to-azure-farmbeats"></a>將使用者添加到 Azure 伺服器場節拍

要將使用者添加到 Azure 伺服器場節拍：

1. 登錄到加速器，然後選擇 **"設置"** 圖示。
2. 選擇**存取控制**。

    !["伺服器場設置"窗格](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. 輸入要授予存取權限的使用者的電子郵件 ID。
4. 選擇所需的角色、**管理員**或**唯讀**。
5. 選擇 **"添加角色**"。

添加的使用者現在可以訪問 Azure FarmBeats（Datahub 和加速器）。

## <a name="delete-users-from-azure-farmbeats"></a>從 Azure 伺服器場刪除使用者

要從 Azure 伺服器場節拍系統中刪除使用者，請執行以下準備：

1. 登錄到加速器，然後選擇 **"設置"** 圖示。
2. 選擇**存取控制**。
3. 選擇 **"刪除**"。

   使用者將從系統中刪除。 您將收到以下確認訊息：

   ![Azure 農場節拍確認訊息](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>新增 Azure AD 使用者

> [!NOTE]
> Azure FarmBeats 使用者需要存在於 Azure AD 租戶中，然後才能將其分配給應用程式和角色。 如果使用者在 Azure AD 租戶中不存在，請按照本節中的說明操作。 如果 Azure AD 租戶中已存在使用者，請跳過說明。

按照步驟將使用者添加到 Azure AD：

1. 登錄到 Azure[門戶](https://portal.azure.com/)。
2. 在右上角，選擇您的帳戶，然後切換到與 FarmBeats 關聯的 Azure AD 租戶。
3. 選擇**Azure 活動目錄** > **使用者**。

    將顯示 Azure AD 使用者的清單。

4. 要將使用者添加到目錄，請選擇 **"新建使用者**"。 要添加外部使用者，請選擇 **"新建來賓使用者**"。

    !["所有使用者"窗格](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. 選擇新使用者的名稱，然後完成該使用者所需的欄位。
6. 選取 [建立]****。

有關管理 Azure AD 使用者的資訊，請參閱[在 Azure AD 中添加或刪除使用者](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/)。

## <a name="next-steps"></a>後續步驟

您已成功將使用者添加到 Azure FarmBeats 實例。 現在，學習如何[創建和管理農場](manage-farms-in-azure-farmbeats.md#create-farms)。
