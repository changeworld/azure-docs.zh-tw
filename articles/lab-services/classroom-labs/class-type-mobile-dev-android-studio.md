---
title: 設置實驗室，教授使用 Android Studio 的移動應用程式開發
titleSuffix: Azure Lab Services
description: 瞭解如何設置實驗室來教授使用 Android Studio 的資料移動應用程式開發類。  本文還將討論在 Azure 中的虛擬機器上使用 Android Studio 時要進行的調整。
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849782"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>設置實驗室，使用 Android Studio 教授資料移動應用程式開發

本文將介紹如何設置介紹性移動應用程式開發類。  此課程著重於可發佈至 [Google Play 商店](https://play.google.com/store/apps)的 Android 行動應用程式。  學生會了解如何使用 [Android Studio](https://developer.android.com/studio) 來建置應用程式。  [Visual Studio 的 Android 模擬器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)可用來在本機測試應用程式。

## <a name="lab-configuration"></a>實驗室組態

要設置此實驗，需要 Azure 訂閱和實驗室帳戶才能開始。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 獲取 Azure 訂閱後，可以在 Azure 實驗室服務中創建新的實驗室帳戶。 有關創建新實驗室帳戶的詳細資訊，請參閱[設置實驗室帳戶的教程](tutorial-setup-lab-account.md)。  您還可以使用現有的實驗室帳戶。

按照[設置的課堂實驗教程](tutorial-setup-classroom-lab.md)創建新實驗室，然後應用以下設置：

| 虛擬機器大小 | 映像 |
| -------------------- | ----- |
| 中等（嵌套虛擬化） | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>範本機配置

範本電腦創建完成後，[啟動電腦並連接到電腦](how-to-create-manage-template.md#update-a-template-vm)以完成以下任務：

1. 添加超 V 角色
2. 下載並安裝 JAVA。  
3. 下載並安裝適用于 Android 的視覺工作室模擬器。
4. 下載並安裝安卓工作室。
5. 為 Android 工作室配置視覺化工作室模擬器。

## <a name="add-hyper-v-role"></a>添加超 V 角色

為了成功安裝適用于 Android 的視覺化工作室模擬器，必須啟用 Hyper-V。  按照[如何在範本虛擬機器文章中啟用嵌套虛擬化](how-to-enable-nested-virtualization-template-vm.md)的說明進行操作。

## <a name="install-java"></a>安裝 Java

安卓工作室需要JAVA。  按照以下步驟下載最新版本的 JAVA。

1. 導航到[JAVA 下載頁](https://www.java.com/download/)。 按一下**JAVA 下載**按鈕。
2. 在 64 位 JAVA 網頁的 Windows 上，按一下標記為"**同意"和"開始免費下載"的**按鈕。
3. 當**出現 JAVA 安裝程式**時，按一下"**安裝**"。
4. 等待安裝程式標題更改為 JAVA**安裝程式 = 完成**。  按一下"**關閉**"按鈕。

## <a name="install-visual-studio-emulator-for-android"></a>安裝適用于 Android 的視覺工作室模擬器

要在本地測試 Android 應用程式，它必須使用 Android 設備的虛擬化版本。  有幾個Android模擬器，將允許開發人員測試他們的應用程式從他們的機器。  我們使用 Visual Studio 模擬器用於 Android，因為它是支援嵌套虛擬化的模擬器。  由於實驗室服務 VM 已經是虛擬機器，因此我們需要支援嵌套虛擬化的模擬程式。  Android Studio 的內置模擬程式不支援嵌套虛擬化。  要查看哪些模擬程式支援嵌套虛擬化，請參閱[模擬器性能的硬體加速（Hyper-V & HAXM）。](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration)

使用以下說明下載並安裝適用于 Android 的視覺化工作室模擬器。

1. 導航到 Android 主頁[的視覺工作室模擬器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)。
2. 按一下 **"下載模擬器"** 按鈕。
3. 下載 vs_emulatorsetup.exe 時，請運行可執行檔。
4. 當出現"視覺化工作室設置"對話方塊時，按一下"**安裝**"按鈕。
5. 等待安裝程式完成。  按一下"**立即重新開機"** 按鈕以重新開機電腦並完成安裝。

在使用 Android Studio 部署應用程式之前，請先啟動模擬程式。  有關 Android 的視覺工作室模擬器的詳細資訊，請參閱[Android 文檔的視覺工作室模擬器](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)。

## <a name="install-android-studio"></a>安裝安卓工作室

按照下面的說明下載和安裝[Android工作室](https://developer.android.com/studio)。

1. 導航到[Android工作室下載頁面](https://developer.android.com/studio#downloads)。  
    > [!NOTE]
    > 此網站不支援 Internet 資源管理器。
2. 按一下 Windows （64 位） 可執行檔 Android 工作室包。
3. 閱讀快顯視窗中寫入的法律術語。  準備繼續時，請檢查**我已閱讀並同意上述條款及條件**核取方塊，然後按一下 **"為 Windows 下載 Android 工作室**"按鈕。
4. 一旦在 Android Studio 設置上下載可執行檔，請運行可執行檔。
5. 在**Android 工作室安裝程式**的 **"歡迎訪問 Android 工作室設置"** 頁面上，按一下 **"下一步**"。
6. 在 **"配置設置"** 頁上，按一下"**下一步**"。
7. 在 **"選擇開始功能表資料夾"** 頁上，按一下"**安裝**"。
8. 等待設置完成。
9. 在 **"安裝完成"** 頁上，按一下"**下一步**"。
10. 在 **"完成 Android 工作室設置**"頁面上。  按一下 **[完成]**。
11. Android 工作室將在設置完成後自動啟動。
12. 在 **"導入 Android 設置從..."** 對話方塊中，選擇 **"不導入設置**"。 按一下 [確定]****。
13. 在**Android 工作室設置嚮導****的歡迎**頁面上，按一下 **"下一步**"。
14. 在 **"安裝類型"** 頁上，選擇 **"標準**"。 按 [下一步]****。
15. 在 **"選擇 UI 主題"** 頁上，選擇所需的主題。 按 [下一步]****。
16. 在"**驗證設置"** 頁上，按一下 **"下一步**"。
17. 在"**下載元件"** 頁上，等待下載所有元件。  按一下 **[完成]**。

    > [!IMPORTANT]
    > 預計 HAXM 安裝失敗。  HAXM 不支援嵌套虛擬化，這就是為什麼我們在本文前面為 Android 安裝了 Visual Studio 模擬器的原因。

18. 設置嚮導完成後，將顯示"**歡迎使用 Android Studio"** 對話方塊。

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>為 Android 配置 Android 工作室和視覺化工作室模擬器

安卓工作室幾乎可以投入使用。  我們仍然需要告訴Visual Studio 模擬器的 Android 安裝 Android SDK 的位置。  這將使在 Visual Studio 中運行的任何模擬器都作為 Android 工作室調試的部署目標顯示。

我們需要設置一個特定的登錄機碼，告訴Android Sdk所在的Android視覺工作室模擬器。  要設置所需的登錄機碼，請參閱下面的腳本。  下面的 PowerShell 腳本假定 Android Sdk 的預設安裝位置。  如果您在其他位置安裝了 Android Sdk，請在運行腳本之前修改`$androidSdkPath`的值。

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> 重新開機 Android 和 Android 工作室的視覺工作室模擬器，以便使用新設置。

在視覺化工作室模擬器中啟動所需的版本。  它將在 Android 工作室中作為 Android 應用的部署目標顯示。  Android Studio 專案的最低版本必須支援在 Android 的視覺化工作室模擬器中運行的版本。  現在，您已準備好使用 Android 工作室和 Visual Studio 模擬器創建和調試專案。  如果您有任何問題，請參閱 Android 模擬器故障排除。

## <a name="cost"></a>成本

如果您想估計本實驗的成本，可以按照下面的示例操作。
對於一個25個學生，有20小時的預定上課時間和10小時的家庭作業或作業配額，實驗室的價格是  

25\*名學生（20 個計畫 + 10 個配額） 小時 = 55 個實驗室單位 = 每小時 0.01 USD = 412.5 USD

有關定價的更多詳細資訊，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="next-steps"></a>後續步驟

後續步驟是設置任何實驗室的常見步驟。

- [創建和管理範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設置配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設置計畫](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [電子郵件註冊連結給學生](how-to-configure-student-usage.md#send-invitations-to-users)
