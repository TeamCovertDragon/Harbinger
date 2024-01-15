# 新版构建项目方法

## Eclipse
我们接下来介绍构建用于Eclipse项目的一种办法。
  1. 打开你解压压缩包的文件夹(也就是包含build.gradle的目录)。
  2. shift+右键空白处 `打开命令窗口`（CMD黑窗口） 如果你是win10 你可能看见的是 `在此处打开PowerShell窗口` 一个道理。
  3. 如果你上一步打开的是PowerShell窗口 你需要先输入`cmd`并回车打开命令行窗口
  4. 输入gradlew genEclipseRuns 回车 提示`BUILD SUCCESSFUL`绿色字符（红色字符请检查你的网络或求助他人）。
  5. 再输入gradlew eclipse 回车提示`BUILD SUCCESSFUL`绿色字符。
  6. 打开Eclipse 左上角`File` -> `Import` -> `Existing Projects into Workspace` 。
  7. 选择你刚刚解压压缩包的文件夹，如果一切正常，`Finish`按钮可以被按下 你只需要直接按下`Finish`。

## IDEA
我们接下来介绍构建用于IDEA项目的一种办法。
  1. 打开IDEA 如果你是第一次启动 你应该选择`Open` 并选择`build.gradle`文件。
  2. 如果你不是第一次启动 你应该点击左上角`File` -> `Open` 并选择刚刚的`build.gradle`文件。
  3. 如果上一步弹窗 询问导入方式 你应该选择`Open As Project`。
  4. 此时IDEA会自动部署环境 右下角会有进度条 等待部署完成
  5. 等待部署完成后 同Eclipse一样的方法打开命令行窗口
  6. 输入 `gradlew genIntellijRuns` 回车
  7. 显示`BUILD SUCCESSFUL`绿色字符后 重启你的IDEA