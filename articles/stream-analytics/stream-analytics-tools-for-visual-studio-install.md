---
title: 安裝適用於 Visual Studio 的 Azure 串流分析工具
description: 本文介紹了安裝要求以及如何為 Visual Studio 設置 Azure 流分析工具。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: fa3990d2239da71fb27f4c9a06699f758fcfe7a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354373"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>安裝適用於 Visual Studio 的 Azure 串流分析工具

Visual Studio 2019 和 Visual Studio 2017 支援 Azure 資料湖和流分析工具。 本文說明如何安裝和解除安裝這些工具。

有關使用這些工具的詳細資訊，請參閱[快速入門：使用 Visual Studio 創建 Azure 流分析作業](stream-analytics-quick-create-vs.md)。

## <a name="install"></a>安裝

視覺化工作室企業版（終極/高級）、專業版和社區版支援這些工具。 適用于 Mac 的 Express 版和視覺化工作室不支援它們。

我們建議視覺工作室 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>2019 年和 2017 年為視覺工作室安裝<a name="recommended-visual-studio-2019-and-2017"></a>

Azure 資料湖和流分析工具是**Azure 開發和****資料存儲和處理**工作負荷的一部分。 在安裝過程中啟用這兩個工作負載之一。 如果已安裝 Visual Studio，**Tools** > 請選擇"**工具獲取工具和功能**"以添加工作負載。

下載[Visual Studio 2019（預覽版 2 或以上）或 Visual Studio 2017（15.3 或以上），](https://www.visualstudio.com/)並按照說明進行安裝。

選擇**資料存儲和處理**工作負載，如下所示：

![已選取資料儲存體和處理工作負載](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

選擇**Azure 開發**工作負荷，如下所示：

![已選取 Azure 開發工作負載](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

添加工作負荷後，更新工具。 此過程是指 Visual Studio 2019：

1. 選擇**擴展** > **管理擴展**。

1. 在 **"管理擴展"中**，選擇 **"更新"** 並選擇**Azure 資料湖和流分析工具**。

1. 選擇 **"更新"** 以安裝最新的擴展。

![Visual Studio 的擴充功能和更新](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>2015 年和 2013 年為視覺工作室安裝<a name="visual-studio-2015-2013"></a>

視覺化工作室企業版（終極/高級）、專業版和社區版支援這些工具。 快速版不支援它們。

* 安裝 Visual Studio 2015 或 Visual Studio 2013 Update 4。
* 使用 [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) 安裝 Microsoft Azure SDK for .NET 2.7.1 版或更新版本。
* 安裝[微軟 Azure 資料湖和流分析工具視覺化工作室](https://www.microsoft.com/en-us/download/details.aspx?id=49504)。

## <a name="update"></a>更新<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

對於 Visual Studio 2019 和 Visual Studio 2017，新版本提醒將顯示為 Visual Studio 通知。

對於 Visual Studio 2015 和 Visual Studio 2013，這些工具會自動檢查新版本。 按照說明安裝最新版本。

## <a name="uninstall"></a>解除安裝

您可以卸載 Azure 資料湖和流分析工具。 對於視覺工作室 2019 或視覺工作室 2017， 選擇**工具** > **獲取工具和功能**. 在 **"修改**"中，取消選擇**Azure 資料湖和流分析工具**。 它出現在**資料存儲和處理**工作負荷或**Azure 開發**工作負荷下。

要從 Visual Studio 2015 或 Visual Studio 2013 卸載，請轉到**控制台** > **程式和功能**。 卸載**微軟 Azure 資料湖和流分析工具視覺化工作室**。
