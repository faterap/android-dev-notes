# MBox

### 1. 概念

#### WorkSpace

由 MBox 创建的工作单元，每个 `workspace` 即一个沙盒环境，具有一套隔离的代码、工具、插件等研发环境。

#### Feature

可在 `workspace` 创建的最小工作空间，每个 `feature` 同样也是一个沙盒环境，具有一套隔离的代码、工具、插件等研发环境，用户可在 `workspace` 内创建多个 `feature` 并自由切换。

#### Repo

即仓库，用于存放代码、构建工具，通常情况下为 Git 仓库。一个 Feature 可以拥有多个 `Repo`

#### Container

从 `Repo` 中抽象出的用于构建项目的集合，一个仓库可以存在多种类型的 `Container`，比如在 iOS 项目中可以同时具有 Bundler 和 Pod 两个类型的容器。

