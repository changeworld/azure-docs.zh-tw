---
title: 快速入門 - 設定 Azure Analysis Services 伺服器防火牆 | Microsoft Docs
description: 此快速入門可協助您使用 Azure 入口網站，來設定 Azure Analysis Services 伺服器的防火牆。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e4953137cf939c35c6ac73fe51ca43eca6e99edc
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192444"
---
# <a name="quickstart-configure-server-firewall---portal"></a>快速入門：設定伺服器防火牆 - 入口網站

本快速入門可協助您設定 Azure Analysis Services 伺服器的防火牆。 針對存取伺服器的電腦啟用防火牆並設定 IP 位址範圍，是保護伺服器和資料的重要環節。

## <a name="prerequisites"></a>Prerequisites

- 訂用帳戶中的 Analysis Services 伺服器。 若要深入了解，請參閱[快速入門：建立伺服器 - 入口網站](analysis-services-create-server.md)或[快速入門：建立伺服器 - PowerShell](analysis-services-create-powershell.md)
- 用戶端電腦的一或多個 IP 位址範圍 (如有需要)。

> [!NOTE]
> 啟用防火牆時，即使在「允許從 Power BI 存取」設定設為「開啟」，目前也不支援從 Microsoft 雲端德國的 Power BI Premium 中進行資料匯入 (重新整理) 和編頁報告連線。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站 

[登入入口網站](https://portal.azure.com)

## <a name="configure-a-firewall"></a>設定防火牆

1. 按一下您的伺服器，以開啟 [概觀] 頁面。 
2. 在 [設定] > [防火牆] > [啟用防火牆] 中，選取 [開啟]。
3. 若要啟用 Power BI 和 Power BI Premium 的連線，在**允許從 Power BI 存取**中，選取 [開啟]。  
4. (選用) 指定一或多個 IP 位址範圍。 輸入每個範圍的名稱、起始和結束 IP 位址。 防火牆規則名稱應該限制在 128 個字元內，而且只能包含大寫字元、小寫字元、數字、底線和連字號。 不允許使用空格和其他特殊字元。
5. 按一下 [儲存]。

     ![防火牆設定](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>清除資源

不再需要 IP 位址範圍時請加以刪除，或停用防火牆。

## <a name="next-steps"></a>後續步驟
在本快速入門中，您已了解如何為您的伺服器設定防火牆。 現在您已有伺服器，並使用防火牆加以保護，接下來您可以從入口網站為其新增基本範例資料模型。 範例模型有助於您了解如何設定模型資料庫角色以及測試用戶端連線。 若要深入了解，請繼續進行新增範例模型的教學課程。

> [!div class="nextstepaction"]
> [教學課程：將範例模型新增至您的伺服器](analysis-services-create-sample-model.md)
