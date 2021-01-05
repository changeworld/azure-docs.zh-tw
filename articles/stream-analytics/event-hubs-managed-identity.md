---
title: '使用受控識別從 Azure 串流分析作業存取事件中樞 (預覽版) '
description: 本文說明如何使用受控識別來驗證您的 Azure 串流分析作業，以 Azure 事件中樞輸入和輸出。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: ca27df7188c5edd1da94fc41707f6c25eb4034bf
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895133"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>使用受控識別從 Azure 串流分析作業存取事件中樞 (預覽版) 

Azure 串流分析支援 Azure 事件中樞輸入和輸出的受控識別驗證。 受控識別消除了以使用者為基礎的驗證方法限制，例如因為密碼變更或每90天發生的使用者權杖到期而需要重新驗證。 當您移除手動驗證的需求時，您的串流分析部署可以完全自動化。  

受控識別是在 Azure Active Directory 中註冊的受控應用程式，代表了指定的串流分析作業。 受控應用程式是用來向目標資源進行驗證，包括位於防火牆或虛擬網路後方 (VNet) 的事件中樞。 如需如何略過防火牆的詳細資訊，請參閱 [允許透過私人端點存取 Azure 事件中樞命名空間](../event-hubs/private-link-service.md#trusted-microsoft-services)。

本文說明如何透過 Azure 入口網站針對串流分析作業的事件中樞輸入或輸出啟用受控識別。在您啟用受控識別之前，您必須先擁有串流分析作業和事件中樞資源。

### <a name="limitation"></a>限制
在預覽期間，使用受控識別驗證模式時，從 Azure 入口網站的事件中樞取樣輸入將無法運作。

## <a name="create-a-managedidentity"></a>建立受控識別  

首先，您會建立 Azure 串流分析作業的受控識別。  

1. 在 Azure 入口網站中，開啟您的 Azure 串流分析作業。  

1. 從左側導覽功能表中，選取位於 [設定] 下的 [ **受控識別**]   。 ** 然後，勾選 [ **使用系統指派的受控識別**] 旁的方塊，   然後選取 [ **儲存**]。

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="系統指派的受控識別":::  

1. 串流分析作業身分識別的服務主體是在 Azure Active Directory 中建立的。 新建立之身分識別的生命週期是由 Azure 管理。 當串流分析作業刪除時，Azure 會自動刪除已與其建立關聯的身分識別 (亦即服務主體)。  

   當您儲存組態時，服務主體的物件識別碼 (OID) 會列為主體識別碼，如下所示：  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="主體識別碼":::

   服務主體與串流分析作業的名稱相同。 例如，如果您的作業名稱是  `MyASAJob` ，服務主體的名稱也是  `MyASAJob` 。  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>授與串流分析作業存取事件中樞的許可權

若要讓串流分析作業使用受控識別來存取您的事件中樞，您所建立的服務主體必須具有事件中樞的特殊許可權。

1. 移至事件中樞內 **(IAM) 的存取控制** 。

1. 選取 [ **+ 新增** ] 和 [ **新增角色指派**]。

1. 在 [ **新增角色指派** ] 頁面上，輸入下列選項：

   |參數|值|
   |---------|-----|
   |角色|Azure 事件中樞資料擁有者|
   |存取權指派對象|使用者、群組或服務主體|
   |選取|輸入您的串流分析作業名稱|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="新增角色指派":::

1. 選取 [ **儲存** ] 並等候一分鐘，讓變更傳播。

您也可以在事件中樞命名空間層級授與此角色，這會自然地將許可權傳播至在其下建立的所有事件中樞。 亦即，命名空間下的所有事件中樞都可作為串流分析作業中的受控身分識別驗證資源使用。

## <a name="create-anevent-hub-input-or-output"></a>建立事件中樞輸入或輸出  

現在您已設定受控識別，您已準備好將事件中樞資源作為輸入或輸出新增至串流分析作業。  

### <a name="add-the-event-hub-as-an-input"></a>新增事件中樞作為輸入 

1. 移至您的串流分析作業，然後流覽至 [**工作拓撲**] 下的 [**輸入**] 頁面。

1. 選取 [ **將資料流程輸入新增至事件中樞] >**。 在 [輸入屬性] 視窗中，搜尋並選取您的事件中樞，然後從 [*驗證模式*] 下拉式功能表中選取 [**受控識別**]。

1. 填寫其餘的屬性，然後選取 [ **儲存**]。

### <a name="add-the-event-hub-as-an-output"></a>新增事件中樞作為輸出

1. 移至您的串流分析作業，然後流覽至 [**作業拓撲**] 下的 [**輸出**] 頁面。

1. 選取 [ **新增 > 事件中樞**]。 在 [輸出屬性] 視窗中，搜尋並選取您的事件中樞，然後從 [*驗證模式*] 下拉式功能表中選取 [**受控識別**]。

1. 填寫其餘的屬性，然後選取 [ **儲存**]。

## <a name="next-steps"></a>後續步驟

* [Azure 串流分析的事件中樞輸出](event-hubs-output.md)
* [來自事件中樞的串流資料](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
