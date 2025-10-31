# Kubernetes常见面试题
## 基础概念
### 1.什么是 Kubernetes？它的主要功能是什么？
- Kubernetes 是一个开源的容器编排平台，用于自动化部署、扩展和管理容器化应用程序。
- 主要功能包括：服务发现与负载均衡、存储编排、自动部署与回滚、自动扩缩容、自我修复、密钥与配置管理等。
### 2.Kubernetes 和 Docker 有什么区别？
- Docker 是一个容器化平台，用于创建和运行容器。
- Kubernetes 是一个容器编排系统，用于管理多个 Docker 容器组成的应用程序。
### 3.解释 Kubernetes 的架构和主要组件
- Master 节点组件：
- API Server：集群的前端接口
- Scheduler：负责将 Pod 调度到节点
- Controller Manager：运行控制器进程
- etcd：键值存储数据库
- Node 节点组件：
- Kubelet：与 Master 通信的代理
- Kube-proxy：网络代理
- 容器运行时（如 Docker）
## 核心概念
### 1.什么是 Pod？为什么需要 Pod 而不是直接部署容器？
- Pod 是 Kubernetes 中最小的部署单元，可以包含一个或多个容器。
- Pod 提供了共享的网络和存储空间，使得紧密耦合的容器可以方便地共享资源。
### 2.解释 Deployment、StatefulSet 和 DaemonSet 的区别
- Deployment：管理无状态应用，支持滚动更新和回滚
- StatefulSet：管理有状态应用，提供稳定的网络标识和持久存储
- DaemonSet：确保所有(或部分)节点运行一个 Pod 副本
### 3.Service 是什么？有哪些类型？
- Service 是定义一组 Pod 访问策略的抽象，提供稳定的 IP 和 DNS 名称。
- 类型：
- ClusterIP（默认）：集群内部访问
- NodePort：通过节点端口暴露服务
- LoadBalancer：使用云提供商的负载均衡器
- ExternalName：通过返回 CNAME 记录映射到外部服务
### 1.解释 ConfigMap 和 Secret 的作用及区别
- ConfigMap
  ：存储非敏感配置数据
- Secret
  ：存储敏感信息（如密码、令牌等）
- 区别：Secret 会进行 base64 编码，但不会加密，适合敏感度较低的数据。
### 1.如何排查 Pod 无法启动的问题？
- 检查 Pod 状态：
  kubectl describe pod <pod-name>
- 查看日志：
  kubectl logs <pod-name>
- 检查事件：
  kubectl get events
- 检查资源配额和限制
- 检查镜像拉取策略和权限
### 2.如何实现 Kubernetes 的滚动更新？
- 使用 Deployment 并配置
  strategy.rollingUpdate
- 命令：
  kubectl set image deployment/<deployment-name> <container-name>=<new-image>
- 可以通过
  maxUnavailable 和 maxSurge 参数控制更新过程
### 3.解释 Kubernetes 中的亲和性(Affinity)和反亲和性(Anti-affinity)
- 亲和性
  ：将 Pod 调度到满足特定条件的节点上
- 反亲和性
  ：避免将 Pod 调度到已运行特定 Pod 的节点上
- 用于优化资源利用、提高可用性或满足特定部署要求
## 安全与网络
### 1.Kubernetes 中有哪些安全机制？
- RBAC（基于角色的访问控制）
- Network Policies（网络策略）
- Pod Security Policies（Pod 安全策略）
- Secrets 管理
- 审计日志
### 2.什么是 NetworkPolicy？它如何工作？
- NetworkPolicy 定义了 Pod 组之间以及与其他网络端点之间的通信规则。
- 它通过标签选择器指定适用的 Pod，并定义入站(ingress)和出站(egress)规则。
### 3.如何限制 Pod 的资源使用？
- 通过
  resources 字段设置 requests 和 limits：
  resources:
  requests:
  memory: "64Mi"
  cpu: "250m"
  limits:
  memory: "128Mi"
  cpu: "500m"
## 实际场景
### 2.描述一个完整的 CI/CD 流水线如何与 Kubernetes 集成
- 代码提交触发 CI 流水线
- 构建 Docker 镜像并推送到镜像仓库
- 使用 Helm 或 kubectl 部署到 Kubernetes 测试环境
- 运行自动化测试
- 通过审批后部署到生产环境
- 可能包括金丝雀发布或蓝绿部署策略
### 3.如何监控 Kubernetes 集群？
- 使用 Prometheus 收集指标
- 使用 Grafana 进行可视化
- 使用 EFK 或 Loki 收集日志
- 使用 Kubernetes Dashboard 或 Kuboard 提供 Web 界面
- 使用 Alertmanager 设置告警
## 最新趋势
### 1.什么是 Operator 模式？
- Operator 是 Kubernetes 的扩展，使用自定义资源(CR)管理应用及其组件。
- 它将运维知识编码为软件，自动化复杂应用的管理。
### 2.Serverless 和 Kubernetes 有什么关系？
- Knative 和 OpenFaaS 等框架在 Kubernetes 上提供 Serverless 功能。
- 云厂商的 Serverless 产品（如 AWS Fargate、Azure Container Instances）基于 Kubernetes。
- Kubernetes 本身可以看作 Serverless 的基础设施层。
