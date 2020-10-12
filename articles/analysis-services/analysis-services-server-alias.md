---
title: Azure Analysis Services 伺服器名稱別名 | Microsoft Docs
description: 了解如何建立 Azure Analysis Services 伺服器名稱別名。 然後，使用者可以使用較短的別名名稱 (而不是伺服器名稱) 來連線至您的伺服器。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 435649c5431ff14461245fee88cebe4a2c571663
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85361433"
---
# <a name="alias-server-names"></a>伺服器名稱別名

藉由使用伺服器名稱別名，使用者將可使用較短的*別名*連線至您的 Azure Analysis Services，而不需使用伺服器名稱。 從用戶端應用程式連線時，會使用 **link://** 通訊協定格式將別名指定為端點。 然後，端點會傳回實際的伺服器名稱以進行連線。

伺服器名稱別名有下列效益：

- 在伺服器之間移轉模型而不影響使用者。 
- 好記的伺服器名稱可方便使用者記憶。 
- 在一天之中的不同時間將使用者導向至不同的伺服器。 
- 將不同區域的使用者導向至距離更接近的執行個體，例如，在使用 Azure 流量管理員時。 

任何傳回有效 Azure Analysis Services 伺服器名稱的 HTTPS 端點，都可作為別名。 端點必須支援經由連接埠 443 的 HTTPS，且不得在 URI 中指定該連接埠。

![使用連結格式的別名](media/analysis-services-alias/aas-alias-browser.png)

從用戶端連線時，伺服器名稱的別名會以 **link://** 通訊協定格式輸入。 例如，在 Power BI Desktop 中：

![Power BI Desktop 連線](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>建立別名

若要建立別名端點，您可以使用任何會傳回有效 Azure Analysis Services 伺服器名稱的方法。 例如，參考 Azure Blob 儲存體中包含實際伺服器名稱的檔案，或建立並發佈 ASP.NET Web Forms 應用程式。

在此範例中，會使用 Visual Studio 建立 ASP.NET Web Forms 應用程式。 頁面參考和使用者控制項會從 default.aspx 頁面移除。 Default.aspx 的內容就是下列頁面指示詞：

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Default.aspx.cs 中的 Page_Load 事件會使用 Response.Write() 方法傳回 Azure Analysis Services 伺服器名稱。

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>另請參閱

[用戶端程式庫](https://docs.microsoft.com/analysis-services/client-libraries?view=azure-analysis-services-current)   
[從 Power BI Desktop 連線](analysis-services-connect-pbi.md)
