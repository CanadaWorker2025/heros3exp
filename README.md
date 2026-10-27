# Heroes III Expansion 1.83

基于 VCMI 的英雄无敌 3 整合包，包含游戏程序、模组和自定义配置。本仓库附带可执行程序，正常游玩不需要自行编译引擎。

## 下载游戏

使用 Git 下载：

```powershell
git clone https://github.com/CanadaWorker2025/heros3exp.git
```

也可以在 GitHub 仓库页面选择 **Code -> Download ZIP**，下载后完整解压。

以下说明中的“仓库根目录”指包含本 README 和 `Hero3Expasion1.83` 文件夹的目录。例如：

```text
D:\coding\heros3exp\
```

## 补齐三个资源文件

以下文件由 `.gitignore` 排除，**Git clone 和 Download ZIP 都不会包含它们**。请从本项目 Releases 中另外提供的资源附件获取；若尚未发布附件，需要从原有整合包备份中复制。

[打开 Releases 页面](https://github.com/CanadaWorker2025/heros3exp/releases)

| 文件 | 用途 | 相对于仓库根目录的放置位置 |
| --- | --- | --- |
| `H3bitmap.lod` | 原版核心图片、文本等资源 | `Hero3Expasion1.83/Data/H3bitmap.lod` |
| `Heroes3.vid` | 原版视频资源包 | `Hero3Expasion1.83/Data/Heroes3.vid` |
| `preserveEnd.bik` | 禁林战役结局视频 | `Hero3Expasion1.83/Mods/new-menu/Content/video/preserveEnd.bik` |

补齐后的目录结构：

```text
heros3exp/
    README.md
    Hero3Expasion1.83/
        VCMI_launcher.exe
        VCMI_client.exe
        Data/
            H3bitmap.lod
            Heroes3.vid
        Mods/
            new-menu/
                Content/
                    video/
                        preserveEnd.bik
    My Games/
        vcmi/
            config/
            Saves/
```

如果资源压缩包内已经包含 `Hero3Expasion1.83` 目录，请解压到**仓库根目录**并合并文件夹，避免多套一层目录。

例如仓库位于 `D:\coding\heros3exp` 时，三个文件的完整路径应为：

```text
D:\coding\heros3exp\Hero3Expasion1.83\Data\H3bitmap.lod
D:\coding\heros3exp\Hero3Expasion1.83\Data\Heroes3.vid
D:\coding\heros3exp\Hero3Expasion1.83\Mods\new-menu\Content\video\preserveEnd.bik
```

## 首次启动和开始游戏

1. 补齐上述资源后，打开 `Hero3Expasion1.83` 文件夹。
2. 双击 `VCMI_launcher.exe`，首次启动后关闭启动器，以便生成当前 Windows 用户的配置目录。
3. 如需沿用本整合包的模组启用状态，将仓库内 `My Games/vcmi/config` 的内容复制到当前用户的 VCMI `config` 目录。已有配置时，先备份再合并；仓库中的配置副本未必包含原机器最新的个人设置。
4. 重新打开 `VCMI_launcher.exe`，检查模组页面，确认没有缺失依赖或报错。确认 `new-menu`、所需城镇模组和“特长修改”（`extra creatures specialty`）等已按预设启用。
5. 点击启动游戏，进入游戏后选择单人地图或战役。首次加载模组可能需要等待。

配置完成后，也可以直接双击 `VCMI_client.exe` 进入游戏。请使用这里的 VCMI 程序启动整合包；旧的 `Heroes3 HD.lnk` 快捷方式可能指向原机器路径或另一套引擎。

## 存档和用户设置

VCMI 在 Windows 下通常使用：

```text
C:\Users\你的用户名\Documents\My Games\vcmi\
```

如果“文档”目录被 OneDrive 或系统设置重定向，实际位置可能不同，以本机生成的目录为准。

- `config`：用户设置、模组启用状态等。
- `Saves`：存档；例如 `Quicksave.vsgm1`。

**仓库中的 `My Games/vcmi` 是一份备份副本，不会因为放在仓库里就自动成为游戏当前使用的用户目录。**

换电脑时，如需继续原来的进度和设置，请先关闭游戏，将原机器实际使用的 `Documents/My Games/vcmi` 目录复制到新机器对应位置；新机器已有数据时先备份。

## 更新和常见问题

- Git 用户可以用 `git pull` 获取已提交的更新。自行修改过配置时，先检查 `git status` 并备份自己的修改。
- 游戏、模组配置更新后，建议退出客户端和启动器再重新打开。
- 提示缺少资源或界面异常：先确认三个补充文件的路径正确，尤其是 `H3bitmap.lod`，并检查模组依赖。
- 结局视频无法播放：检查 `preserveEnd.bik` 是否放在 `Mods/new-menu/Content/video` 下。
- 找不到旧存档：检查是否复制到了当前用户实际使用的 `My Games/vcmi/Saves`，而非仅复制到仓库内的备份目录。
- 缺少 DLL：先确认仓库已完整下载并解压；如系统提示缺少 Visual C++ 运行库，请从微软官方安装对应运行库。

修改记录见 [LSFupdatelog.md](LSFupdatelog.md)。
