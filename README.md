# Go Zero Columbina UI 加密货币交易终端

基于 Vue 3 + TypeScript + Vite 构建的加密货币现货交易前端，OKX 深色风格。

![Go Zero Columbina UI 交易终端]

## 技术栈

- **框架**：Vue 3（Composition API + `<script setup>`）
- **构建**：Vite 8 + vue-tsc
- **样式**：Tailwind CSS v4 + shadcn-vue（reka-ui）
- **语言**：TypeScript 6

## 功能模块

| 模块 | 说明 |
|------|------|
| 登录 | 账号密码登录，JWT Token 自动附加到请求头 |
| K 线图 | 可拖动平移、滚轮缩放、开高低收悬浮展示 |
| 盘口 | 买卖盘深度展示，WebSocket 增量更新 |
| 下单 | 限价 / 市价 / 止盈止损，买入卖出 |
| 委托 | 当前委托 & 历史委托，支持撤单 |
| 资产 | 用户资产展示（可用 / 冻结） |
| 最新成交 | 右侧实时成交列表 |

## 实时数据（WebSocket）

通过 WebSocket 订阅实时行情，所有订阅共用同一连接，10s 心跳保活。

| 订阅 | Topic 格式 | 说明 |
|------|-----------|------|
| K 线 | `kline@{symbol}@{period}` | 实时更新最后一根 K 线 |
| Ticker | `ticker@{symbol}` | 24h 最新价、高低、量额、涨跌幅 |
| 盘口 | `depth@{symbol}` | 增量更新买卖盘档位 |

## 项目结构

```
src/
├── api/               # API 层
│   ├── auth.ts        # 登录、Token 管理
│   ├── token.ts       # localStorage Token 读写
│   ├── request.ts     # HTTP 请求封装（自动附加 Bearer Token）
│   ├── quotes.ts      # 行情接口（K线、盘口、Ticker、成交）
│   ├── order.ts       # 下单、撤单、订单列表
│   ├── account.ts     # 用户资产
│   ├── ws.ts          # WebSocket 客户端
│   ├── klineWs.ts     # K线 WS 消息解析与合并
│   ├── tickerWs.ts    # Ticker WS 消息解析
│   ├── depthWs.ts     # 盘口 WS 增量消息处理
│   └── index.ts       # 统一导出
├── composables/       # 组合式函数
│   ├── useKlineList.ts         # K线加载 + WS 订阅
│   ├── useTicker.ts            # Ticker 加载 + WS 订阅
│   ├── useOrderBookDepth.ts    # 盘口加载 + WS 订阅
│   ├── useTickList.ts          # 最新成交轮询
│   ├── useCreateOrder.ts       # 下单
│   ├── useCancelOrder.ts       # 撤单
│   ├── useOrderListPagination.ts # 委托列表分页
│   └── useUserAssets.ts        # 用户资产
├── components/
│   ├── TradingTerminal.vue     # 主交易页面
│   ├── KlineChart.vue          # K线图组件（拖动/缩放）
│   ├── LoginPage.vue           # 登录页
│   └── OrderAssetBar.vue       # 买卖区资产条
└── App.vue            # 根组件（登录态路由）
```

## 快速开始

先启动后端基础设施和业务服务：

```bash
cd ../../go/go-zero_columbina
make dep
make start
```

确认 Gateway 的 `http://localhost:8888` 可以访问后，再启动前端：

```bash
# 安装依赖
pnpm install

# 启动开发服务器
pnpm dev

# 构建生产版本
pnpm build
```

## 环境变量

| 变量 | 说明 | 默认值 |
|------|------|--------|
| `VITE_API_BASE_URL` | HTTP API 地址 | `/api`（开发环境代理） |
| `VITE_WS_URL` | WebSocket 地址 | 与当前页面同源的 `/ws` |

## 后端接口

| 路径前缀 | 说明 |
|---------|------|
| `/account/v1/` | 登录、登出、资产 |
| `/order/v1/` | 下单、撤单、订单列表 |
| `/quotes/v1/` | K线、盘口、Ticker、成交 |

## 默认交易对

固定为 **COLU/USDT**（API 格式 `COLU_USDT`），涨色 `#00d395`，跌色 `#f6465d`。
