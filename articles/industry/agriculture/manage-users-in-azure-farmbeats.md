---
title: 管理 Azure FarmBeats 中的使用者
description: 本文說明如何在 Azure FarmBeats 中管理使用者。
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 6ccc29422c6abf1120d13c05d10125efd0871ca6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "79502059"
---
# <a name="manage-users"></a>管理使用者

針對屬於 Azure Active Directory (Azure AD) 實例的人員，Azure FarmBeats 包含使用者管理。 您可以將使用者新增至 Azure FarmBeats 實例，以存取 Api、查看所產生的對應，以及從伺服器陣列存取感應器遙測。

## <a name="prerequisites"></a>必要條件

- 需要安裝 Azure FarmBeats。 如需詳細資訊，請參閱 [安裝 Azure FarmBeats](install-azure-farmbeats.md)。
- 您要在 Azure FarmBeats 實例中新增或移除之使用者的電子郵件識別碼。

## <a name="manage-azure-farmbeats-users"></a>管理 Azure FarmBeats 使用者

Azure FarmBeats 會使用 Azure AD 來進行驗證、存取控制和角色。 您可以將 Azure AD 租使用者中的使用者新增為 Azure FarmBeats 中的使用者。

> [!NOTE]
> 如果使用者不是 Azure AD 租使用者使用者，請遵循 **新增 Azure AD 使用者** 一節中的指示來完成設定。

Azure FarmBeats 支援兩種類型的使用者角色：

 - 系統**管理員**： Azure FarmBeats Datahub api 的完整存取權。 此角色中的使用者可以查詢所有 Azure FarmBeats Datahub 物件，並從 FarmBeats 加速器執行所有作業。
 - **唯讀**： FarmBeats Datahub api 的唯讀存取權。 使用者可以查看 Datahub Api、加速器儀表板和地圖。 具有唯讀存取權的使用者無法執行諸如產生對應、裝置建立關聯，或建立伺服器陣列等作業。

## <a name="add-users-to-azure-farmbeats"></a>將使用者新增至 Azure FarmBeats

若要將使用者新增至 Azure FarmBeats：

1. 登入 [加速器]，然後選取 [ **設定** ] 圖示。
2. 選取 [ **存取控制**]。

    ![[伺服器陣列設定] 窗格](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. 輸入您想要授與存取權之使用者的電子郵件識別碼。
4. 選取所需的角色： [ **管理員** ] 或 [ **唯讀**]。
5. 選取 [ **新增角色**]。

新增的使用者現在可以存取 Azure FarmBeats， (Datahub 和加速器) 。

## <a name="delete-users-from-azure-farmbeats"></a>從 Azure FarmBeats 刪除使用者

若要從 Azure FarmBeats 系統中移除使用者：

1. 登入 [加速器]，然後選取 [ **設定** ] 圖示。
2. 選取 [ **存取控制**]。
3. 選取 [刪除]  。

   系統會從系統中刪除使用者。 您將會收到下列確認訊息：

   ![Azure FarmBeats 確認訊息](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>新增 Azure AD 使用者

> [!NOTE]
> Azure FarmBeats 使用者必須存在於 Azure AD 租使用者中，才能將其指派給應用程式和角色。 如果使用者不存在於 Azure AD 租使用者中，請遵循本節中的指示。 如果使用者已存在 Azure AD 租使用者中，請略過指示。

遵循將使用者新增至 Azure AD 的步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在右上方選取您的帳戶，然後切換至與 FarmBeats 相關聯的 Azure AD 租使用者。
3. 選取 [Azure Active Directory]  >  [使用者]。

    Azure AD 的使用者清單隨即顯示。

4. 若要將使用者新增至目錄，請選取 [ **新增使用者**]。 若要新增外部使用者，請選取 [ **新增來賓使用者**]。

    ![[所有使用者] 窗格](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. 選取新使用者的名稱，然後完成該使用者的必要欄位。
6. 選取 [建立]  。

如需管理 Azure AD 使用者的相關資訊，請參閱 [Azure AD 中的新增或刪除使用者](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/)。

## <a name="next-steps"></a>後續步驟

您已成功將使用者新增至您的 Azure FarmBeats 實例。 現在，請瞭解如何 [建立和管理伺服器](manage-farms-in-azure-farmbeats.md#create-farms)陣列。
