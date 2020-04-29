---
title: 設定實驗室，使用 Android Studio 來教行動應用程式開發
titleSuffix: Azure Lab Services
description: 瞭解如何設定實驗室來教授使用 Android Studio 的資料行動應用程式開發類別。  文中也將討論在 Azure 中的虛擬機器上使用 Android Studio 時，所做的調整。
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76849782"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>設定實驗室，使用 Android Studio 來教授資料行動應用程式開發

本文將說明如何設定簡介行動應用程式開發類別。  此課程著重於可發佈至 [Google Play 商店](https://play.google.com/store/apps)的 Android 行動應用程式。  學生會了解如何使用 [Android Studio](https://developer.android.com/studio) 來建置應用程式。  [Visual Studio 的 Android 模擬器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)可用來在本機測試應用程式。

## <a name="lab-configuration"></a>實驗室組態

若要設定此實驗室，您需要 Azure 訂用帳戶和實驗室帳戶，才能開始使用。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 取得 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶。 如需建立新實驗室帳戶的詳細資訊，請參閱[設定實驗室帳戶的教學](tutorial-setup-lab-account.md)課程。  您也可以使用現有的實驗室帳戶。

依照[設定教室實驗室教學課程](tutorial-setup-classroom-lab.md)來建立新的實驗室，然後套用下列設定：

| 虛擬機器大小 | Image |
| -------------------- | ----- |
| 中（嵌套虛擬化） | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>範本機器設定

當範本機器建立完成時，請[啟動電腦並](how-to-create-manage-template.md#update-a-template-vm)連線到它，以完成下列工作：

1. 新增 Hyper-v 角色
2. 下載並安裝 JAVA。  
3. 下載並安裝適用于 Android 的 Visual Studio 模擬器。
4. 下載並安裝 Android Studio。
5. 設定 Android Studio 的 Visual Studio 模擬器。

## <a name="add-hyper-v-role"></a>新增 Hyper-v 角色

必須啟用 hyper-v，才能成功安裝適用于 Android 的 Visual Studio 模擬器。  請依照[如何在範本虛擬機器中啟用嵌套虛擬化一](how-to-enable-nested-virtualization-template-vm.md)文中的指示進行。

## <a name="install-java"></a>安裝 Java

Android Studio 需要 JAVA。  請遵循下列步驟來下載最新版本的 JAVA。

1. 流覽至[JAVA 下載頁面](https://www.java.com/download/)。 按一下 [ **JAVA 下載**] 按鈕。
2. 在 [適用于 JAVA 的64位 Windows] 網頁上，按一下標示為 [**同意並開始免費下載**] 的按鈕。
3. 當 [ **JAVA 安裝**程式] 出現時，按一下 [**安裝**]。
4. 等到安裝程式標題變更為 [ **JAVA 設定–完成**]。  按一下 [**關閉**] 按鈕。

## <a name="install-visual-studio-emulator-for-android"></a>安裝適用于 Android 的 Visual Studio 模擬器

若要在本機測試 Android 應用程式，它必須使用 Android 裝置的虛擬化版本。  有幾個 Android 模擬器可供開發人員從其電腦測試其應用程式。  我們使用適用于 Android 的 Visual Studio 模擬器，因為它是支援「嵌套虛擬化」的模擬器。  因為實驗室服務 VM 已經是虛擬機器，所以我們需要支援「嵌套虛擬化」的模擬器。  Android Studio 的內建模擬器不支援「嵌套虛擬化」。  若要查看哪些模擬器支援嵌套虛擬化，請參閱[適用于模擬器效能的硬體加速（hyper-v & HAXM）](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration)。

請使用下列指示來下載並安裝適用于 Android 的 Visual Studio 模擬器。

1. 流覽至[Android 的 Visual Studio 模擬器](https://visualstudio.microsoft.com/vs/msft-android-emulator/)首頁。
2. 按一下 [**下載模擬器**] 按鈕。
3. 下載 vs_emulatorsetup .exe 時，請執行可執行檔。
4. 當 [Visual Studio 安裝] 對話方塊出現時，按一下 [**安裝**] 按鈕。
5. 等待安裝程式完成。  按一下 [**立即重新開機**] 按鈕以重新開機電腦並完成安裝。

先啟動模擬器，再使用 Android Studio 部署您的應用程式。  如需有關適用于 Android 的 Visual Studio 模擬器的詳細資訊，請參閱[android 的 Visual Studio 模擬器檔](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)。

## <a name="install-android-studio"></a>安裝 Android Studio

請依照下列指示下載並安裝[Android Studio](https://developer.android.com/studio)。

1. 流覽至[Android Studio 下載頁面](https://developer.android.com/studio#downloads)。  
    > [!NOTE]
    > 此網站不支援 Internet Explorer。
2. 按一下 Windows （64位）可執行檔 Android Studio 封裝。
3. 閱讀在快顯視窗中撰寫的法律條款。  準備好繼續進行時，請核取 **[我已閱讀並同意上述條款及條件**] 核取方塊，然後按一下 [**下載 Android Studio for Windows** ] 按鈕。
4. 一旦下載 Android Studio 安裝程式可執行檔，請執行可執行檔。
5. 在**Android Studio 安裝**程式安裝程式的 [**歡迎使用 Android Studio 設定**] 頁面上，按 **[下一步]**。
6. 在 [**設定**] 頁面上，按 **[下一步]**。
7. 在 [**選擇開始功能表資料夾**] 頁面上，按一下 [**安裝**]。
8. 等待安裝程式完成。
9. 在 [**安裝完成**] 頁面上，按 **[下一步]**。
10. 在 [**正在完成 Android Studio 設定**] 頁面上。  按一下 [完成]  。
11. 安裝程式完成後，Android Studio 會自動啟動。
12. 在 [**從匯入 Android 設定**...] 對話方塊中，選取 [**不要匯入設定**]。 按一下 [確定]  。
13. 在**Android Studio 安裝程式]** 的 [**歡迎使用**] 頁面上，按 **[下一步]**。
14. 在 [**安裝類型**] 頁面上，選擇 [**標準**]。 按 [下一步]  。
15. 在 [**選取 UI 主題**] 頁面上，選取 [所需的主題]。 按 [下一步]  。
16. 在 [**驗證設定**] 頁面上，按 **[下一步]**。
17. 在 [**下載元件**] 頁面上，等候所有元件都已下載。  按一下 [完成]  。

    > [!IMPORTANT]
    > HAXM 安裝預期會失敗。  HAXM 不支援「嵌套虛擬化」，這就是我們稍早在本文中為 Android 安裝 Visual Studio 模擬器的原因。

18. 當安裝程式完成時，就會出現 [**歡迎使用 Android Studio** ] 對話方塊。

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>設定適用于 Android 的 Android Studio 和 Visual Studio 模擬器

Android Studio 幾乎已準備好可供使用。  我們仍然需要告訴 Visual Studio 已安裝 Android SDK 的 Android 模擬器。  這會讓 Android Visual Studio 中執行的任何模擬器顯示為 Android Studio 進行偵錯工具的部署目標。

我們必須設定特定的登錄機碼，以告知 android Sdk 所在的 Visual Studio 模擬器。  若要設定所需的登錄機碼，請執行下列腳本。  下列 PowerShell 腳本會假設 Android Sdk 的預設安裝位置。  如果您已將 Android Sdk 安裝在另一個位置，請在`$androidSdkPath`執行腳本之前修改的值。

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> 重新開機 Visual Studio 適用于 Android 和 Android Studio 的模擬器，以使用新的設定。

在 Visual Studio 模擬器中啟動您需要的版本。  它會顯示為 android studio 中 Android 應用程式的部署目標。  Android Studio 專案的最低版本必須支援在適用于 Android 的 Visual Studio 模擬器中執行的版本。  現在您已準備好使用 Android Studio 和適用于 Android 的 Visual Studio 模擬器來建立和調試專案。  如果您有任何問題，請參閱 Android 模擬器疑難排解。

## <a name="cost"></a>成本

如果您想要估計此實驗室的成本，可以遵循下列範例。
對於25名學生的課程，其中有20小時的排程類別時間，以及10小時的家庭作業或指派配額，實驗室的價格將會是  

25名\*學生（20已排程 + 10 配額）時數 * 55 實驗室單位 * 每小時0.01 美元 = 412.5 美元

如需更多有關定價的詳細資訊，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="next-steps"></a>後續步驟

下一步是設定任何實驗室的常見步驟。

- [建立和管理範本](how-to-create-manage-template.md)
- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)
