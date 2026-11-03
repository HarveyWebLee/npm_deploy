# npm 私有源部署服务

## 部署

```
docker-compose up -d
```

## 访问

```
http://localhost:4873
```

## 企业级增强

```
uplinks:
  npmjs:
    url: https://registry.npmmirror.com/
```

## 权限控制

```YAML
packages:
  '@company/*':
    access: $authenticated
    publish: $authenticated
```

### 私有 scope：

```Bash
npm publish --access restricted
```

## 重新部署

    不丢数据 + 不影响使用 + 可回滚

### 核心目录：

```
./storage   # 包数据（最重要）
./conf      # 配置文件
```

### 最安全的重新部署流程

#### 标准流程（不会丢包）

```
# 1️⃣ 停止服务
docker-compose down

# 2️⃣ 备份数据（非常关键）
cp -r storage storage.bak
cp -r conf conf.bak

# 3️⃣ 拉取新版本镜像（可选）
docker pull verdaccio/verdaccio:5

# 4️⃣ 重新启动
docker-compose up -d
```

### 如果你改了配置（比如权限/源）

##### 热更新方式

```
# 重启容器即可
docker-compose restart
# Verdaccio 不会自动热加载 config.yaml 必须重启容器
```

### 彻底重建（服务器迁移 / 崩了）

#### 迁移步骤

```
# 旧服务器
scp -r storage conf user@new-server:/data/verdaccio

# 新服务器
cd /data/verdaccio
docker-compose up -d
```
