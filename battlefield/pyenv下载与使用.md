## 一、下载pyenv

**1.** 下载

使用终端运行
~~~
pip install pyenv-win --target %USERPROFILE%\.pyenv
~~~
**2.** 配置环境变量

在path中添加两项
~~~
%USERPROFILE%\.pyenv\pyenv-win\bin
%USERPROFILE%\.pyenv\pyenv-win\shims
~~~

## 二、管理员权限的调整
**Windows PowerShell** 的脚本执行策略会阻止 **pyenv.ps1** 脚本运行。

默认情况下，Windows 会限制未签名脚本执行。

#### 方法 1：临时解决（当前会话生效，推荐测试用）

在 **PowerShell** 里输入：
~~~
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
~~~

这样只对 **当前 PowerShell 窗口**生效，关闭窗口后失效。

#### 方法 2：对当前用户永久生效
~~~
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
~~~

这会让当前用户可以运行本地脚本（比如 pyenv），而不会影响系统其他用户。

#### 方法 3：对整个系统生效（需要管理员权限，不推荐随便改）

以管理员身份打开 PowerShell，执行：
~~~
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope LocalMachine
~~~

### **常见策略说明**

**Restricted**（默认）：禁止一切脚本运行。

**AllSigned**：只允许已签名的脚本。

**RemoteSigned**：本地脚本无限制，下载的脚本必须签名。

**Unrestricted**：不限制（有安全风险，不推荐）。

通常设置为 RemoteSigned 就能正常使用 pyenv-win。

## 三、pyenv的指令使用

**1.** 查看可安装的 Python 版本
~~~
pyenv install --list
~~~

**2.** 安装指定版本 Python
~~~
pyenv install 3.11.6
~~~

**3.** 查看已安装的版本
~~~
pyenv versions
~~~

**4.** 设置全局（默认）Python 版本
~~~
pyenv global 3.11.6
~~~

**5.** 设置项目本地 Python 版本（在项目目录下执行）
~~~
pyenv local 3.10.12
~~~

**6.** 临时使用某个版本
~~~
pyenv shell 3.9.18
~~~

**7.** 卸载某个版本
~~~
pyenv uninstall 3.8.18
~~~

## 四、pip的更新
使用
~~~
pip install --upgrade pyenv-win --target %USERPROFILE%\.pyenv
~~~

或者在切换python虚拟环境后运行
~~~
python -m pip install --upgrade pip
~~~

## 五、将版本导入至vscode
在 VSCode 里：

* 按 `Ctrl + Shift + P`

* 输入 **Python: Select Interpreter**

* 选择你 pyenv 下的 3.9.13 解释器

`（路径应该是 C:\Users\(username)\.pyenv\pyenv-win\versions\(version)\python.exe）`
