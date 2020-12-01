---
title: 使用 Excel 來連接到 Azure Analysis Services | Microsoft Docs
description: 了解如何使用 Excel 來連接到 Azure Analysis Services 伺服器。 一旦連線之後，使用者就可以建立樞紐分析表以流覽資料。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c91cfe24aa7a5dd224fd1aed31b6b0dee44e687f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352808"
---
# <a name="connect-with-excel"></a>使用 Excel 進行連接

在您建立伺服器並將表格式模型部署至該伺服器之後，用戶端即可連線及開始瀏覽資料。 

## <a name="before-you-begin"></a>開始之前

您登入的帳戶必須屬於模型資料庫角色，且至少具有讀取權限。 若要深入了解，請參閱[驗證和使用者權限](analysis-services-manage-users.md)。 

## <a name="connect-in-excel"></a>在 Excel 中連線

使用 Excel 2016 和更新版本中的 [取得資料]，可支援在 Excel 中連線到伺服器。 不支援在 Power Pivot 中使用 [匯入資料表精靈] 連線。 

1. 在 Excel 的 [**資料**] 功能區中，按一下 [從資料庫 **取得資料**]  >  **From Database**  >  **Analysis Services**。

2. 在 [資料連線精靈] 的 [伺服器名稱] 中，輸入包含通訊協定和 URI 的伺服器名稱。 例如 asazure://westcentralus.asazure.windows.net/advworks。 然後在 [登入認證] 中，選取 [使用下列的使用者名稱和密碼]，接著輸入組織使用者名稱 (例如 nancy@adventureworks.com) 和密碼。

    > [!IMPORTANT]
    > 如果您是使用 Microsoft 帳戶、Live ID、Yahoo、Gmail 等登入，或者是必須使用多重要素驗證登入，密碼欄位請空白。 您按 [下一步] 之後系統會提示您輸入密碼。 

    ![從 Excel 登入來進行連接](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. 在 [選取資料庫和資料表] 中，選取資料庫和模型或檢視方塊，然後按一下 [完成]。
   
    ![從 Excel 選取模型來進行連接](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>另請參閱

[用戶端程式庫](/analysis-services/client-libraries?view=azure-analysis-services-current)   
[管理您的伺服器](analysis-services-manage.md)
