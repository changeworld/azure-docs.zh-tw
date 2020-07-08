---
title: 安裝適用於 Visual Studio 的 Azure 串流分析工具
description: 本文說明安裝需求，以及如何設定適用于 Visual Studio 的 Azure 串流分析工具。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/22/2018
ms.openlocfilehash: 464e8fbf624dfa84ec54bf6a7e0ce202d047e15d
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045105"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>安裝適用於 Visual Studio 的 Azure 串流分析工具

Visual Studio 2019 和 Visual Studio 2017 支援 Azure Data Lake 和串流分析工具。 本文說明如何安裝和解除安裝這些工具。

如需使用這些工具的詳細資訊，請參閱[快速入門：使用 Visual Studio 建立 Azure 串流分析作業](stream-analytics-quick-create-vs.md)。

## <a name="install"></a>安裝

Visual Studio Enterprise （終極/Premium）、Professional 和社區版本都支援工具。 Express edition 和 Visual Studio for Mac 不支援它們。

建議使用 Visual Studio 2019。

### <a name="install-for-visual-studio-2019-and-2017"></a>針對 Visual Studio 2019 和2017安裝<a name="recommended-visual-studio-2019-and-2017"></a>

Azure Data Lake 和串流分析工具是**Azure 開發**和**資料儲存和處理**工作負載的一部分。 請在安裝期間啟用這兩個工作負載的其中一個。 如果已安裝 Visual Studio，請選取 [**工具**] [  >  **取得工具和功能**] 來新增工作負載。

下載[Visual Studio 2019 （Preview 2 或更新版本）或 Visual Studio 2017 （15.3 或](https://www.visualstudio.com/)更新版本），並遵循指示來安裝。

選取**資料儲存和處理**工作負載，如下所示：

![已選取資料儲存體和處理工作負載](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

選取 [ **Azure 開發**] 工作負載，如下所示：

![已選取 Azure 開發工作負載](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

新增工作負載之後，請更新工具。 此程式是指 Visual Studio 2019：

1. 選取 [**擴充**功能] [  >  **管理擴充**功能]。

1. 在 [**管理擴充**功能] 中，選取 [**更新**]，然後選擇**Azure Data Lake 和串流分析工具**。

1. 選取 [**更新**] 以安裝最新的延伸模組。

![Visual Studio 的擴充功能和更新](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>針對 Visual Studio 2015 和2013安裝<a name="visual-studio-2015-2013"></a>

Visual Studio Enterprise （終極/Premium）、Professional 和社區版本都支援工具。 Express edition 不支援它們。

* 安裝 Visual Studio 2015 或 Visual Studio 2013 Update 4。
* 使用 [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx) 安裝 Microsoft Azure SDK for .NET 2.7.1 版或更新版本。
* 安裝[適用于 Visual Studio 的 Microsoft Azure Data Lake 和串流分析工具](https://www.microsoft.com/en-us/download/details.aspx?id=49504)。

## <a name="update"></a>更新<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

針對 Visual Studio 2019 和 Visual Studio 2017，新版本的提醒會顯示為 Visual Studio 通知。

針對 Visual Studio 2015 和 Visual Studio 2013，工具會自動檢查是否有新版本。 請遵循指示來安裝最新版本。

## <a name="uninstall"></a>解除安裝

您可以卸載 Azure Data Lake 和串流分析工具。 針對 Visual Studio 2019 或 Visual Studio 2017，請選取 [**工具**] [  >  **取得工具和功能**]。 在 [**修改**] 中，取消選取**Azure Data Lake 和串流分析工具**。 它會出現在 [**資料儲存和處理**工作負載] 或 [ **Azure 開發**] 工作負載之下。

若要從 Visual Studio 2015 或 Visual Studio 2013 卸載，請移至 [**控制台**] [  >  **程式和功能**]。 卸載**Microsoft Azure Data Lake 和串流分析工具以進行 Visual Studio**。
