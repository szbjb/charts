# KasmWeb Helm Chart

## 配置文件说明

该 Helm chart 用于部署 `KasmWeb`，通过修改 `values.yaml` 文件中的配置项，你可以定制不同的部署方案。以下是配置文件的详细说明。

### 快速启动

如果你希望快速启动一个默认配置的 KasmWeb 实例（不启用持久化存储），可以直接使用以下命令：

```bash
helm repo add szbjb-charts https://szbjb.github.io/charts
helm repo update
helm install my-kassmweb szbjb-charts/kasmweb
```

该命令会使用默认的配置，即不启用持久化存储 (PVC)，并使用默认的服务和端口设置。
1. 默认使用 hostPath 持久化存储（storage.usePersistentStorage=true）
```bash
helm repo add szbjb-charts https://szbjb.github.io/charts
helm repo update    
helm install my-kassmweb szbjb-charts/kasmweb  --set storage.usePersistentStorage=true
```
2. 使用 StorageClass 和 PVC 持久化存储（storage.usePersistentStorage=true 和 storage.type=StorageClass）
```bash 
helm repo add szbjb-charts https://szbjb.github.io/charts
helm repo update
helm install my-kassmweb szbjb-charts/kasmweb  --set storage.usePersistentStorage=true --set storage.type=StorageClass --set pvc.usePersistentVolumeClaim=true --set pvc.storageClassName=""

```


### 配置项表格

| 配置项                      | 描述                                      | 默认值                                                    |
|---------------------------|-----------------------------------------|---------------------------------------------------------|
| `replicaCount`            | 应用副本数                                 | `1`                                                     |
| `image.repository`        | Docker 镜像仓库地址                           | `swr.cn-southwest-2.myhuaweicloud.com/kubeode/kasmweb/ubuntu-jammy-desktop` |
| `image.tag`               | Docker 镜像标签                            | `1.15.0`                                                |
| `image.pullPolicy`        | 镜像拉取策略                                | `IfNotPresent`                                          |
| `service.type`            | 服务类型，支持 ClusterIP, NodePort, LoadBalancer 等 | `NodePort`                                              |
| `service.port`            | 服务端口                                   | `6901`                                                  |
| `service.nodePort`        | NodePort 设置                              | ```（自动分配）                                          |
| `service.targetPort`      | 目标端口                                   | `6901`                                                  |
| `pvc.usePersistentVolumeClaim` | 是否启用持久化存储卷 PVC                       | `false`                                                 |
| `pvc.size`                | 持久化存储卷 PVC 的大小                     | `5Gi`                                                   |
| `pvc.storageClassName`    | PVC 存储类名称                              | ```                                                    |
| `pvc.accessMode`          | PVC 访问模式                                | `ReadWriteOnce`                                         |
| `storage.type`            | 存储类型，选择 hostpath 或 StorageClass      | `hostpath`                                              |
| `storage.path`            | `hostpath` 存储路径                         | `/mnt/disks/data/pvc-data`                              |
| `env.VNC_PW`              | VNC 密码                                  | `Password`                                              |
| `env.LANG`                | 系统语言设置                                | `zh_CN.UTF-8`                                           |
| `ingress.enabled`         | 是否启用 Ingress                           | `false`                                                 |
| `ingress.host`            | 域名或主机名                                | `example.com`                                           |
| `ingress.tls.secretName`  | TLS 证书的 Secret 名称                        | `example-com-tls`                                       |

### 选择部署方案

根据不同的需求，你可以通过修改 `values.yaml` 或在 `helm install` 时通过 `--set` 参数来调整部署配置。

#### 1. 启用持久化存储 (PVC)

```yaml
pvc:
  usePersistentVolumeClaim: true
  size: 10Gi
  storageClassName: "standard"
```

##### 安装命令：
```bash
helm install my-KasmWeb ./ --set pvc.usePersistentVolumeClaim=true --set pvc.size=10Gi --set pvc.storageClassName="standard"
```

#### 2. 不使用持久化存储 (PVC)

```yaml
pvc:
  usePersistentVolumeClaim: false
```

##### 安装命令：
```bash
helm install my-KasmWeb ./ --set pvc.usePersistentVolumeClaim=false
```

#### 3. 启用 Ingress，并配置 TLS 证书

```yaml
ingress:
  enabled: true
  host: "kasm.example.com"
  tls:
    - hosts:
        - "kasm.example.com"
      secretName: "kasm-tls"
```

##### 安装命令：
```bash
helm install my-KasmWeb ./ --set ingress.enabled=true --set ingress.host="kasm.example.com" --set ingress.tls[0].secretName="kasm-tls"
```

#### 4. 启用 Ingress，但使用默认的全局证书

```yaml
ingress:
  enabled: true
  host: "kasm.example.com"
  tls: []  # 不指定 secretName，Ingress Controller 使用默认证书
```

##### 安装命令：
```bash
helm install my-KasmWeb ./ --set ingress.enabled=true --set ingress.host="kasm.example.com"
```

#### 5. 使用 NodePort 作为服务类型

```yaml
service:
  type: NodePort
  port: 6901
  nodePort: 30001
```

##### 安装命令：
```bash
helm install my-KasmWeb ./ --set service.type=NodePort --set service.nodePort=30001
```

#### 6. 使用 LoadBalancer 作为服务类型

```yaml
service:
  type: LoadBalancer
  port: 6901
```

##### 安装命令：
```bash
helm install my-KasmWeb ./ --set service.type=LoadBalancer
```

### 启动和管理

1. **安装 Chart**
   ```bash
   helm install my-KasmWeb ./ --values values.yaml
   ```

2. **升级 Chart**
   ```bash
   helm upgrade my-KasmWeb ./ --values values.yaml
   ```

3. **卸载 Chart**
   ```bash
   helm uninstall my-KasmWeb
   ```

