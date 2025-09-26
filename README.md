# 内部网络资产发现与管理工具
# 源码获取：https://mbd.pub/o/bread/YZWXm5pwZQ==
## 项目概述

这是一个基于Django框架开发的内部网络资产发现与管理工具，专门用于自动化发现、监控和管理企业内部网络中的各种资产设备。系统集成了多种网络扫描技术，提供实时资产状态监控、漏洞检测、告警管理等功能。

## 功能特性

### 核心功能
- **智能网络发现**：支持ARP扫描、Ping扫描、TCP SYN扫描、UDP扫描等多种扫描方式
- **资产状态监控**：实时监控资产在线状态，支持批量状态检测
- **端口与服务发现**：自动识别开放端口和运行的服务
- **漏洞检测**：集成漏洞扫描功能，识别常见安全风险
- **告警管理**：智能告警系统，支持标记已读和批量操作

### 技术特色
- **并发优化**：采用多线程、多进程并发扫描，大幅提升扫描效率
- **智能策略**：根据网络规模自动选择最优扫描策略
- **降级处理**：当高级扫描失败时自动降级使用基础方法
- **实时反馈**：提供扫描进度实时更新和用户交互

## 项目结构

```
lan/
├── assets/                          # 核心应用模块
│   ├── models.py                    # 数据模型定义
│   ├── views.py                     # 视图函数
│   ├── urls.py                      # URL路由配置
│   ├── network_scanner.py           # 网络扫描核心引擎
│   ├── vulnerability_scanner.py     # 漏洞扫描模块
│   ├── templates/assets/            # 前端模板
│   └── static/assets/              # 静态资源
├── lan_discovery/                   # Django项目配置
│   ├── settings.py                  # 项目设置
│   ├── urls.py                      # 主URL配置
│   └── wsgi.py                      # WSGI配置
├── manage.py                        # Django管理脚本
└── 各种测试脚本                      # 功能测试文件
```

## 核心模块说明

### 1. 网络扫描引擎 (network_scanner.py)

#### 主要功能
- **智能网络大小估算**：支持CIDR、IP范围、单个IP格式
- **并发扫描优化**：根据网络规模动态选择线程/进程池
- **多种扫描方式**：
  - ARP扫描：局域网设备发现
  - Ping扫描：基础连通性检测
  - TCP SYN扫描：端口状态检测
  - UDP扫描：UDP服务发现
  - 综合扫描：多方法组合扫描

#### 性能优化特性
```python
# 智能并发策略
if network_size <= 50:           # 小型网络：顺序扫描
    return sequential_scan()
elif network_size <= 256:         # 中型网络：线程池
    return thread_pool_scan()
else:                             # 大型网络：进程池
    return process_pool_scan()
```

### 2. 资产模型 (models.py)

#### 核心数据模型
- **Asset**：资产基本信息（名称、IP、状态等）
- **Port**：端口信息（端口号、协议、服务等）
- **Vulnerability**：漏洞信息（类型、风险等级、描述等）
- **Alert**：告警信息（类型、状态、时间等）

### 3. 视图控制器 (views.py)

#### 主要功能模块
- **资产管理**：CRUD操作、状态检测、扫描功能
- **网络扫描**：同步/异步扫描接口
- **告警管理**：标记已读、批量操作
- **漏洞扫描**：快速/深度扫描模式

#### AJAX支持示例
```python
def mark_alert_as_read(request, pk):
    """支持AJAX和传统请求的标记已读功能"""
    try:
        alert = get_object_or_404(Alert, pk=pk)
        alert.is_read = True
        alert.save()
        
        # AJAX请求返回JSON
        if request.headers.get('X-Requested-With') == 'XMLHttpRequest':
            return JsonResponse({'status': 'success', 'message': '操作成功'})
        
        # 传统请求重定向
        return redirect('assets:alert_list')
    except Exception as e:
        # 错误处理...
```

## 安装与部署

### 环境要求
- Python 3.8+
- Django 4.2+
- 系统权限（扫描功能需要管理员权限）

### 安装步骤
1. 克隆项目到本地
2. 安装依赖：`pip install -r requirements.txt`
3. 数据库迁移：`python manage.py migrate`
4. 启动服务：`python manage.py runserver 5000`
5. 访问应用：http://127.0.0.1:5000/

## 使用指南

### 快速开始
1. **网络扫描**：访问"网络扫描"页面，输入目标网络段
2. **资产管理**：在"资产列表"中查看和管理发现的设备
3. **状态监控**：使用"检测状态"功能实时监控资产
4. **告警处理**：在"告警中心"处理安全告警

### 扫描配置
- **快速扫描**：适用于日常监控，速度优先
- **增强扫描**：全面检测，包含端口和服务发现
- **自定义扫描**：支持特定端口范围和扫描参数

## 技术架构

### 前端技术
- Bootstrap 5：响应式UI框架
- JavaScript：动态交互功能
- AJAX：异步数据加载

### 后端技术
- Django：Web框架
- SQLite：数据库（生产环境可切换PostgreSQL）
- 多线程/多进程：并发处理

### 网络技术
- Scapy：网络包操作
- Socket：基础网络通信
- 多协议支持：TCP/UDP/ICMP等

## 性能指标

### 扫描效率
- **小型网络**(/24)：约25秒完成全面扫描
- **中型网络**(/16)：优化后可在几分钟内完成
- **并发处理**：支持同时扫描多个网络段

### 资源优化
- **内存管理**：分批处理大型网络扫描
- **CPU利用**：智能分配计算资源
- **网络带宽**：合理的扫描间隔和并发控制

