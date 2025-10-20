# 实战场景题 - 灰度发布

## 线上集群灰度发布时的流量调度与容灾
### 题目描述
微服务架构系统，服务A通过Kubernetes部署，现在需灰度发布新版本（v2）验证。要求：支持按自定义比例（如5%）导入线上流量至v2版本；若v2异常（如CPU激增/错误率上升）则自动切回v1。设计实现方案（含流量调度、监控、熔断策略）。
### 方案设计
#### 1. 流量调度
- 使用Kubernetes的Deployment和Service资源，创建两个版本的Pod（v1和v2）。
- 利用Service的标签选择器（Label Selector）和权重（Weight）功能，实现按比例分配流量。例如，使用Nginx Ingress方案或者使用Istio或Linkerd等服务网格（Service Mesh）工具，配置流量分配规则，将5%的流量导向v2，95%导向v1。
#### 2. 监控与告警
- 部署Prometheus监控系统，采集CPU使用率、内存使用率、请求错误率等指标。
- 配置Grafana仪表盘，实时展示v1和v2的关键指标。
- 设置Prometheus告警规则，当v2的CPU使用率超过设定阈值（如80%）或错误率超过设定阈值（如1%）时，触发告警。
#### 3. 自动回滚
- 使用Kubernetes的Horizontal Pod Autoscaler（HPA）监控Pod的资源使用情况，结合Prometheus告警，实现自动回滚机制。
- 编写Kubernetes Operator或使用Argo Rollouts等工具，监听Prometheus的告警事件，当检测到v2异常时，自动将流量切回v1，并删除或缩减v2的Pod数量。
### 具体说一下Nginx Ingress的流量调度
- 配置两个Service，分别指向v1和v2的Deployment。
- 使用Nginx Ingress的`canary`注解，设置流量权重。例如：
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: service-ingress
  annotations:
    nginx.ingress.kubernetes.io/canary: "true"
    nginx.ingress.kubernetes.io/canary-weight: "5" # 5%
```
- 这样配置后，Nginx Ingress会将5%的流量导向v2，剩余95%导向v1。

## 如何做流量染色？
- 1.客户端配置Cookie： 在用户首次访问时（或满足条件时），由入口服务（如登录页、网关）在响应中设置一个特定的Cookie：traffic_tag=gray。后续请求浏览器会自动带上该Cookie
- 2.网关层添加标识头： 在入口点（如API网关、负载均衡器、前端Web服务器、客户端SDK）配置规则，检查请求的Cookie，如果匹配特殊流量，向请求的HTTP Header中添加一个自定义的标识头 X-Traffic-Tag: gray
- 3.标记的传递与处理：
  - 微服务层处理：每个微服务都有一个 Filter/Interceptor，接收请求时检查 Header 中是否包含 X-Traffic-Tag 标记。如果存在，则将该标记传递给下游服务的请求 Header 中，确保标记在整个调用链中保持一致。
  - 数据源路由：在数据访问层，根据 X-Traffic-Tag 标记决定使用哪个数据库实例（如主库或灰度库）。
  - 日志记录与监控：在日志系统中记录 X-Traffic-Tag 标记，便于后续分析和监控。