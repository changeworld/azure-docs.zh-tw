---
title: 教學課程：使用 Python 啟動沈浸式閱讀程式
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將建立會啟動沈浸式閱讀程式的 Python 應用程式。
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.openlocfilehash: a252afae0a007ee0b791b56d19ffb0685848d30a
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76844355"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>教學課程：使用 Python 範例專案啟動沈浸式閱讀程式

在[概觀](./overview.md)中，您會了解什麼是沈浸式閱讀程式，及其如何實作經實證的技術，針對語言學習者、沈浸式讀者及存在學習差異的學生改善其閱讀理解程度。 本教學課程說明如何建立會啟動沈浸式閱讀程式的 Python Web 應用程式。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 使用範例專案建立具有 Pip、Flask、Jinja 和 virtualenv 的 Python Web 應用程式
> * 取得存取權杖
> * 使用範例內容啟動沈浸式閱讀程式

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

* 為 Azure Active Directory 驗證所設定的沈浸式閱讀程式資源。 遵循[這些指引](./how-to-create-immersive-reader.md)來設定。 設定環境屬性時，您需要這裡建立的一些值。 將工作階段的輸出儲存到文字檔中，以供日後參考。
* [Git](https://git-scm.com/)
* [沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) 和 [pip](https://docs.python.org/3/installing/index.html)。 從 Python 3.4 開始，Python 二進位安裝程式依預設會包含 pip。
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) 和 [virtualenvwrapper-win for Windows](https://pypi.org/project/virtualenvwrapper-win/) 或 [virtualenvwrapper for OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [要求模組](https://pypi.org/project/requests/2.7.0/)
* 整合式開發環境，例如 [Visual Studio Code](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>設定驗證認證

建立名為 .env  的新檔案，並將下列程式碼貼入其中，然後使用您建立沈浸式閱讀程式資源時提供的值。

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

請勿將此檔案認可到原始檔控制，因為它包含不應公開的機密資料。

**getimmersivereadertoken** API 端點在某種形式的驗證 (例如 [OAuth](https://oauth.net/2/)) 後面應受到保護，以防止未經授權的使用者取得權杖，用於沈浸式閱讀程式服務和計費；該工作已超出本教學課程的範圍。

## <a name="create-a-python-web-app-on-windows"></a>在 Windows 上建立 Python Web 應用程式

在 Windows 上使用 `flask` 建立 Python Web 應用程式。

安裝 [Git](https://git-scm.com/)。

在安裝 Git 之後，開啟命令提示字元，並將沈浸式閱讀程式 SDK Git 存放庫「複製」到您電腦上的資料夾

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

安裝 [Python](https://www.python.org/downloads/)。

勾選 [將 Python 新增至 PATH] 方塊。

![Python Windows 安裝對話方塊步驟 1](./media/pythoninstallone.jpg)

勾選方塊以新增選用功能，然後按 [下一步] 按鈕。

![Python Windows 安裝對話方塊步驟 2](./media/pythoninstalltwo.jpg)

選擇 [自訂安裝]，並將安裝路徑設定為根資料夾 (例如 `C:\Python37-32\`)，然後按一下 [安裝] 按鈕。

![Python Windows 安裝對話方塊步驟 3](./media/pythoninstallthree.jpg)

在 Python 安裝完成後，開啟命令提示字元，並透過 `cd` 切換至 [Python 指令碼] 資料夾。

```cmd
cd C:\Python37-32\Scripts
```

安裝 Flask。

```cmd
pip install flask
```

安裝 Jinja2。 適用於 Python 的全功能範本引擎。

```cmd
pip install jinja2
```

安裝 virtualenv。 一項用來建立獨立 Python 環境的工具。

```cmd
pip install virtualenv
```

安裝 virtualenvwrapper-win。 Virtualenvwrapper 背後的構想是要讓 virtualenv 更容易使用。

```cmd
pip install virtualenvwrapper-win
```

安裝要求模組。 「要求」是 Apache2 授權的 HTTP 程式庫，以 Python 撰寫。

```cmd
pip install requests
```

安裝 python-dotenv 模組。 此模組會從 .env 檔案讀取機碼值組，並將其新增至環境變數。

```cmd
pip install python-dotenv
```

建立虛擬環境

```cmd
mkvirtualenv advanced-python
```

透過 `cd` 切換至範例專案根資料夾。

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

連接範例專案與環境。 這會將新建立的虛擬環境對應至範例專案根資料夾。

```cmd
setprojectdir .
```

啟用虛擬環境。

```cmd
activate
```

專案現在應會處於作用中狀態，且您會在命令提示字元中看到類似於 `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` 的內容。

停用環境。

```cmd
deactivate
```

`(advanced-python)` 前置詞現在應已消失，因為環境已停用。

若要重新啟用環境，請從範例專案根資料夾執行 `workon advanced-python`。

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>使用範例內容啟動沈浸式閱讀程式

當環境處於作用中狀態時，從範例專案根資料夾輸入 `flask run` 以執行範例專案。

```cmd
flask run
```

開啟瀏覽器並巡覽至 _http://localhost:5000_ 。

## <a name="create-a-python-web-app-on-osx"></a>在 OSX 上建立 Python Web 應用程式

在 OSX 上使用 `flask` 建立 Python Web 應用程式。

安裝 [Git](https://git-scm.com/)。

在安裝 Git 之後，開啟終端機，並將沈浸式閱讀程式 SDK Git 存放庫「複製」到您電腦上的資料夾

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

安裝 [Python](https://www.python.org/downloads/)。

Python 根資料夾 (例如 `Python37-32`) 現在應位於 [應用程式] 資料夾中。

在 Python 安裝完成後，開啟終端機，並透過 `cd` 切換至 [Python 指令碼] 資料夾。

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

安裝 pip。

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

然後執行下列程式碼，為目前登入的使用者安裝 pip，以避免發生權限問題。

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- 在系統提示時輸入您的密碼。
- 將 pip 的安裝路徑新增至 PATH 變數。
- 移至檔案底部，然後在清單的最後輸入要新增的路徑，例如 `PATH=$PATH:/usr/local/bin`。
- 點擊 control-x 結束作業。
- 輸入 `Y` 以儲存修改過的緩衝區。
- 就這麼簡單！ 若要加以測試，請在新的終端機視窗中輸入︰`echo $PATH`。

安裝 Flask。

```bash
pip install flask --user
```

安裝 Jinja2。 適用於 Python 的全功能範本引擎。

```bash
pip install Jinja2 --user
```

安裝 virtualenv。 一項用來建立獨立 Python 環境的工具。

```bash
pip install virtualenv --user
```

安裝 virtualenvwrapper。 Virtualenvwrapper 背後的構想是要讓 virtualenv 更容易使用。

```bash
pip install virtualenvwrapper --user
```

安裝要求模組。 「要求」是 Apache2 授權的 HTTP 程式庫，以 Python 撰寫。

```bash
pip install requests --user
```

安裝 python-dotenv 模組。 此模組會從 .env 檔案讀取機碼值組，並將其新增至環境變數。

```bash
pip install python-dotenv --user
```

選擇要用來保存虛擬環境的資料夾，並執行下列命令

```bash
mkdir ~/.virtualenvs
```

透過 `cd` 切換至沈浸式閱讀程式 SDK Python 範例應用程式資料夾。

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

建立虛擬環境

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

連接範例專案與環境。 這會將新建立的虛擬環境對應至範例專案根資料夾。

```bash
setprojectdir .
```

啟用虛擬環境。

```bash
activate
```

專案現在應會處於作用中狀態，且您會在命令提示字元中看到類似於 `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` 的內容。

停用環境。

```bash
deactivate
```

`(advanced-python)` 前置詞現在應已消失，因為環境已停用。

若要重新啟用環境，請從範例專案根資料夾執行 `workon advanced-python`。

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>使用範例內容啟動沈浸式閱讀程式

當環境處於作用中狀態時，從範例專案根資料夾輸入 `flask run` 以執行範例專案。

```bash
flask run
```

開啟瀏覽器並巡覽至 _http://localhost:5000_ 。

## <a name="next-steps"></a>後續步驟

* 探索[沈浸式閱讀程式 SDK](https://github.com/microsoft/immersive-reader-sdk) 和[沈浸式閱讀程式 SDK 參考](./reference.md)
* 檢視 [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) 上的程式碼範例
