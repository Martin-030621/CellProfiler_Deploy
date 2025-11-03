# CellProfiler 离线部署与运行完整手册

## 一、在无网络服务器部署

### 1、解压镜像包

```bash
gunzip cellprofiler_4.2.8.tar.gz
```

### 2、导入镜像

```bash
docker load -i cellprofiler_4.2.8.tar
```

验证：

```bash
docker images
```

输出示例：

```
REPOSITORY                  TAG       IMAGE ID       SIZE
cellprofiler/cellprofiler   4.2.8     0e54aebabc99   x.xGB
```

---

## 二、建立工作目录

```bash
mkdir -p /cellprofiler-docker/input
mkdir -p /cellprofiler-docker/output
mkdir -p /cellprofiler-docker/pipelines
```

放置文件结构：

```
/cellprofiler-docker/
├── input/
│   ├── image1.tif
│   ├── image2.tif
├── output/
├── pipelines/
│   └── pipeline.cppipe
```

---

## 三、运行 CellProfiler

```bash
docker run --rm   -v /cellprofiler-docker/input:/input   -v /cellprofiler-docker/output:/output   -v /cellprofiler-docker/pipelines/pipeline.cppipe:/pipeline.cppipe   cellprofiler/cellprofiler:4.2.8   -c -r -p /pipeline.cppipe -i /input -o /output
```

### 参数说明

| 参数            | 含义                       |
| --------------- | -------------------------- |
| `--rm`        | 容器运行结束后自动删除     |
| `-v`          | 挂载宿主机文件夹到容器     |
| `-c`          | 以命令行模式运行（无界面） |
| `-r`          | 执行 pipeline 文件         |
| `-p`          | 指定 pipeline 文件路径     |
| `-i` / `-o` | 输入与输出文件夹           |

---

## 四、验证运行

```bash
docker run --rm cellprofiler/cellprofiler:4.2.8 cellprofiler --version
```

输出：

```
CellProfiler, version 4.2.8
```

查看日志：

```bash
docker logs -f <container_id>
```

结果查看：

```bash
ls /cellprofiler-docker/output/
```

---

## 五、容器内部文件结构

```
/
├── app/
│   ├── cellprofiler/
│   ├── cellprofiler_core/
│   └── centrosome/
├── usr/local/lib/python3.8/
├── input/
├── output/
├── pipeline.cppipe
└── /usr/bin/cellprofiler
```

---

## 六、交互模式（调试）

```bash
docker run -it --rm   -v /cellprofiler-docker:/workspace   cellprofiler/cellprofiler:4.2.8 /bin/bash
```

容器内运行：

```bash
cd /workspace
cellprofiler -c -r -p pipelines/pipeline.cppipe -i input -o output
```

---

## 七、后台运行

```bash
docker run -d --name cp_job   -v /cellprofiler-docker/input:/input   -v /cellprofiler-docker/output:/output   -v /cellprofiler-docker/pipelines/pipeline.cppipe:/pipeline.cppipe   cellprofiler/cellprofiler:4.2.8   -c -r -p /pipeline.cppipe -i /input -o /output
```

查看任务：

```bash
docker ps
docker logs -f cp_job
```

停止任务：

```bash
docker stop cp_job
```

---

## 八、清理旧数据

```bash
docker system prune -a --volumes -f
```
