---
title: Azure Stack Edge Pro，Azure 資料箱 Gateway 的記錄支援票證 |Microsoft Docs
description: 瞭解如何記錄 Azure Stack Edge Pro 或資料箱閘道訂單相關問題的支援要求。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 9df729277934e074e1e8cc92126e777336708586
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904513"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-pro-and-azure-data-box-gateway"></a>開啟 Azure Stack Edge Pro 和 Azure 資料箱閘道的支援票證

本文適用于 Azure Stack Edge Pro 和 Azure 資料箱閘道，這兩個閘道都是由 Azure Stack Edge Pro/Azure 資料箱閘道服務所管理。 如果您的服務遇到任何問題，您可以建立技術支援的服務要求。 本文將引導您：

* 如何建立支援要求。
* 如何在入口網站上管理支援要求的生命週期。

## <a name="create-a-support-request"></a>建立支援要求

執行下列步驟來建立支援要求：

1. 移至您的 Azure Stack Edge Pro 或資料箱閘道訂單。 瀏覽至 [支援 + 疑難排解] 區段，然後按一下 [新增支援要求]。

2. 在 [新增支援要求]的 [基本] 索引標籤上，執行下列步驟：

    1. 從 [問題類型] 下拉式清單中，選取 [技術]。
    2. 選擇您的**訂用帳戶**。
    3. 在 [服務] 底下，勾選 [我的服務]。 從下拉式清單中，選取 [ **Azure Stack Edge Pro 和資料箱閘道**]。
    4. 選取您的**資源**。 這會對應至訂單的名稱。
    5. 對於您遇到的問題提供簡短**摘要**。 
    6. 選取您的**問題類型**。
    7. 根據您選取的問題類型，選擇對應的**問題子類型**。
    8. 完成時，選取 下一步:**解決方案 >>** 。

        ![基本概念](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. 在 [詳細資料] 索引標籤上，採取下列步驟：

    1. 提供問題開始日期與時間。
    2. 提供問題的**描述**。
    3. 在 [檔案上傳] 中，選取資料夾圖示以瀏覽至任何其他您想要上傳的檔案。
    4. 勾選 [共用診斷資訊]。
    5. 根據您的訂閱，系統會自動填入**支援方案**。
    6. 從下拉式清單中選取 [嚴重性]。
    7. 指定**偏好的連絡方式**。
    8. 系統會根據您的訂閱方案自動選擇**回應時間**。
    9. 提供您偏好的支援語言。
    10. 在 [連絡人資訊] 中，提供您的姓名、電子郵件、電話、選用連絡方法、國家/區域。 Microsoft 支援服務會利用此資訊來連絡您，以取得進一步資訊、進行診斷及解決。 
    11. 完成時，選取 下一步:**檢閱 + 建立 >>** 。

        ![問題](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. 在 [檢閱 + 建立] 索引標籤上，查看與支援票證相關的資訊。 選取 [建立]。 

    ![問題](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    建立支援票證之後，支援工程師會儘速連絡您來處理您的要求。

## <a name="get-hardware-support"></a>獲得硬體支援

此資訊僅適用於 Azure Stack 裝置。 報告硬體問題的程式如下所示：

1. 從 Azure 入口網站開啟硬體問題的支援票證。 在 [問題類型] 底下，選取 [Azure Stack 硬體]。 選擇 [問題子類型] 的 [硬體失敗]。

    ![硬體問題](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    建立支援票證之後，支援工程師會儘速連絡您來處理您的要求。

2. 如果 Microsoft 支援服務判斷這是硬體問題，則會發生下列其中一個動作：

    * 傳送失敗硬體元件的現場可更換單元 (FRU)。 目前，電源供應器裝置和硬碟是唯一支援的 FRU。
    * 只有 FRU 會在下一個工作天內更換，其他所有單元則需要等到完整的系統更換 (FSR) 運送。

3. 如果支援票證在當地時間下午4:30 (星期一到星期五) 之前發出，則現場技術人員會在下一個工作天前往您的位置，以執行 FRU 或完整的裝置更換。

## <a name="manage-a-support-request"></a>管理支援要求

建立支援票證之後，您便可以從入口網站中管理票證的生命週期。

### <a name="to-manage-your-support-requests"></a>若要管理支援要求

1. 若要移至說明和支援頁面，請瀏覽至 [瀏覽] > [說明 + 支援]。

    ![管理支援要求](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. [說明 + 支援] 中會以表格式清單列出 [最近的支援要求]。

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. 選取並按一下支援要求。 您可以檢視此要求的狀態與詳細資料。 如果您想要追蹤此要求，請按一下 [+ New message]\(+ 新訊息\)。

## <a name="next-steps"></a>後續步驟

瞭解如何針對 [Azure Stack Edge Pro 的相關問題進行疑難排解](azure-stack-edge-troubleshoot.md)。
瞭解[如何針對 Data Box Gateway 的相關問題進行疑難排解](data-box-gateway-troubleshoot.md)。
