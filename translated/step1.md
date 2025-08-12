# 将应用程序容器化

在本指南的剩余部分中，您将使用一个基于 Node.js 的简单待办事项列表管理器。如果您对 Node.js 不熟悉，请无需担心。本指南无需任何 JavaScript 相关经验。

## 先决条件

* 您已安装了 [Docker 桌面](/get-started/get-docker.md) 的最新版本。
* 您已安装了 [Git 客户端](https://git-scm.com/downloads)。
* 您拥有一个 IDE 或文本编辑器来编辑文件。Docker 推荐使用 [Visual Studio Code](https://code.visualstudio.com/)。

## 获取应用程序

在运行应用程序之前，您需要将应用程序源代码下载到本地计算机。

1. 使用以下命令克隆 [getting-started-app 仓库](https://github.com/docker/getting-started-app/tree/main)：

   ```console
   $ git clone https://github.com/docker/getting-started-app.git
   ```

2. 查看克隆的仓库内容。您应看到以下文件和子目录：

   ```text
   ├── getting-started-app/
   │ ├── .dockerignore
   │ ├── package.json
   │ ├── README.md
   │ ├── spec/
   │ ├── src/
   │ └── yarn.lock
   ```

# 构建应用程序的镜像

要构建镜像，您需要使用 Dockerfile。Dockerfile 是一个没有文件扩展名的文本文件，其中包含一组指令脚本。Docker 通过该脚本构建容器镜像。

1. 在 `getting-started-app` 目录中（与 `package.json` 文件同级），创建一个名为 `Dockerfile` 的文件，内容如下：

   ```dockerfile
   # syntax=docker/dockerfile:1
   FROM node:lts-alpine
   WORKDIR /app
   COPY . .
   RUN yarn install --production
   CMD ["node", "src/index.js"]
   EXPOSE 3000
   ```

   此 Dockerfile 以 `node:lts-alpine` 基础镜像为起点，这是一个轻量级 Linux 镜像，预装了 Node.js 和 Yarn 包管理器。它将所有源代码复制到镜像中，安装必要的依赖项，并启动应用程序。

2. 使用以下命令构建镜像：

   在终端中，确保您位于 `getting-started-app` 目录下。将 `/path/to/getting-started-app` 替换为您 `getting-started-app` 目录的路径。

   ```console
   $ cd /path/to/getting-started-app
   ```

   然后，构建镜像：

   ```console
   $ docker build -t getting-started .
   ```

`docker build` 命令使用 Dockerfile 构建新的镜像。您可能注意到 Docker 下载了大量“层”。这是因为您指示构建器从 `node:lts-alpine` 镜像开始构建，但由于您的计算机上没有该镜像，Docker 需要下载它。

Docker 下载镜像后，Dockerfile 中的指令会将您的应用程序复制到镜像中，并使用 `yarn` 安装应用程序的依赖项。`CMD` 指令指定从该镜像启动容器时默认执行的命令。

最后，`-t` 标志为镜像打上标签。可以将此视为最终镜像的人类可读名称。由于您将镜像命名为 `getting-started`，因此在运行容器时可以引用该镜像。

`docker build` 命令末尾的 `.` 告诉 Docker 在当前目录中查找 `Dockerfile`。

## 启动应用程序容器

现在您已经有了镜像，可以使用 `docker run` 命令在容器中运行应用程序。

1. 使用 `docker run` 命令运行容器，并指定您刚刚创建的镜像名称：

   ```console
   $ docker run -d -p 127.0.0.1:3000:3000 getting-started
   ```

   `-d` 标志（即 `--detach`）用于在后台运行容器。这意味着 Docker 会启动容器并返回终端提示符，同时终端不会显示日志。

   `-p` 标志（即 `--publish`）用于在主机和容器之间创建端口映射。`-p` 标志接受格式为 `HOST:CONTAINER` 的字符串值，其中 `HOST` 是主机上的地址，`CONTAINER` 是容器上的端口。该命令将容器的端口 3000 映射到主机上的 `127.0.0.1:3000`（即 `localhost:3000`）。若未进行端口映射，则无法从主机访问该应用程序。

2. 几秒钟后，打开网页浏览器访问 [http://localhost:3000](http://localhost:3000)。您应能看到应用程序。

   ![空待办事项列表](images/todo-list-empty.webp)

3. 添加一两个项目，确保其按预期工作。您可以标记项目为已完成并删除它们。您的前端已成功将项目存储到后端。

此时，您已拥有一个可运行的待办事项列表管理器，其中包含几个项目。

如果您快速查看容器，应至少看到一个使用 `getting-started` 镜像并在端口 `3000` 上运行的容器。要查看容器，您可以使用 Docker 命令行或 Docker 桌面的图形界面。

{{< tabs >}}

{{< tab name="CLI" >}}

在终端中运行 `docker ps` 命令以列出容器。

```console
$ docker ps
```

输出应类似于以下内容：

```console
CONTAINER ID        IMAGE               COMMAND                 CREATED             STATUS              PORTS                   NAMES
df784548666d        getting-started     "docker-entrypoint.s…"   2 minutes ago       Up 2 minutes        127.0.0.1:3000->3000/tcp   priceless_mcclintock
```

{{< /tab >}}

{{< tab name="Docker Desktop" >}}

在 Docker 桌面中，选择 **容器** 选项卡以查看您的容器列表。

![Docker Desktop 运行 get-started 容器的界面](images/dashboard-two-containers.webp)

{{< /tab >}}

{{< /tabs >}}

## 摘要

在本节中，您学习了创建 Dockerfile 以构建镜像的基础知识。在构建镜像后，您启动了容器并查看了正在运行的应用程序。

相关信息：

* [Dockerfile 参考](/reference/dockerfile/)
* [Docker CLI 参考](/reference/cli/docker/)

## 后续步骤

接下来，您将对应用程序进行修改，并学习如何使用新镜像更新正在运行的应用程序。在此过程中，您还将学习一些其他有用的命令。

{{< button text="更新应用程序" url="03_updating_app.md" >}}


