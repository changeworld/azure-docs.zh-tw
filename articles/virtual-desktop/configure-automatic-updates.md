---
title: 設定 Microsoft 端點 Configuration Manager-Azure
description: 如何設定 Microsoft Endpoint Configuration Manager 在 Windows 虛擬桌面上將軟體更新部署至 Windows 10 企業版多會話。
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: lizross
ms.openlocfilehash: bfc797e0c11a1804d27c6c97fea8cb2ba62dca7a
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010118"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>設定 Microsoft 端點 Configuration Manager

本文說明如何設定 Microsoft Endpoint Configuration Manager 自動將更新套用至執行 Windows 10 企業版多會話的 Windows 虛擬桌面主機。

## <a name="prerequisites"></a>必要條件

若要進行此設定，您需要下列專案：

   - 請確定您已在虛擬機器上安裝 Microsoft Endpoint Configuration Manager 代理程式。
   - 請確定您的 Microsoft Endpoint Configuration Manager 版本至少在分支層級1906。 為獲得最佳結果，請使用分支層級1910或更高版本。

## <a name="configure-the-software-update-point"></a>設定軟體更新點

若要接收 Windows 10 企業版多會話的更新，您必須啟用 Windows Server 1903 版及更新版本，做為 Microsoft 端點 Configuration Manager 中的產品。 如果您使用 Windows Server Update 服務將更新部署至您的系統，此產品設定也適用。

若要接收更新：

1. 開啟 Microsoft Endpoint Configuration Manager 並選取 [**網站**]。
2. 選取 [**設定網站元件**]。
3. 在下拉式功能表中選取 [**軟體更新點**]。
4. 選取 [**產品**] 索引標籤。
5. 選取顯示 [ **Windows Server，1903版及更新**版本] 的核取方塊。
6. 前往**軟體程式庫**  >  **總覽**  >  **軟體更新**  >  **所有軟體更新**，然後選取 [**同步處理軟體更新**]。
7. 檢查**Program Files**  >  **Microsoft Configuration Manager**記錄檔中的 wsyncmgr  >  **Logs** ，確定您的變更已儲存。 同步處理更新可能需要幾分鐘的時間。

## <a name="create-a-query-based-collection"></a>建立以查詢為基礎的集合

若要建立 Windows 10 企業多會話虛擬機器的集合，可使用查詢式集合來識別特定的作業系統 SKU。

若要建立集合：

1. 選取 [**資產與相容性**]。
2. 移至 **[總覽] [**  >  **裝置集合**]，以滑鼠右鍵按一下 [**裝置集合**]，然後從下拉式功能表中選取 [**建立裝置集合**]。
3. 在開啟功能表的 [**一般**] 索引標籤中，于 [**名稱**] 欄位中輸入描述集合的名稱。 在 [**批註**] 欄位中，您可以提供描述集合內容的其他資訊。 在 [**限制集合**] 中，定義您要包含在集合查詢中的電腦。
4. 在 [**成員資格規則**] 索引標籤中，選取 [**新增規則**]，然後選取 [**查詢規則**]，為您的查詢新增規則。
5. 在 [**查詢規則屬性**] 中，輸入規則的名稱，然後選取 [**編輯查詢語句**] 來定義規則的參數。
6. 選取 [**顯示查詢語句**]。
7. 在語句中，輸入下列字串：

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. 選取 **[確定]** 以建立集合。
9. 若要檢查您是否已成功建立集合，請移至 [**資產與相容性**]  >  **[**  >  **裝置集合**]。
