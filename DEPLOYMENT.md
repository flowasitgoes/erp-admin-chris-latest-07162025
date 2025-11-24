# ERP 管理系統部署指南

## 系統概述

本 ERP 管理系統基於 Next.js 15 開發，支援現代化的部署方式。本文檔提供在 Vercel 和 Cloudflare Pages 平台上的完整部署步驟。

## 技術需求

### 系統要求
- Node.js 18.0 或更高版本
- pnpm 8.0 或更高版本
- Git 版本控制

### 專案依賴
- Next.js 15.2.4
- React 19
- TypeScript 5
- Tailwind CSS 3.4

## 部署選項

### 選項 1: Vercel 部署 (推薦)

Vercel 是專為 Next.js 優化的平台，提供最佳的效能和開發體驗。

#### 準備工作

1. **註冊 Vercel 帳號**
   - 前往 [vercel.com](https://vercel.com) 註冊帳號
   - 建議使用 GitHub 帳號登入以便整合

2. **安裝 Vercel CLI** (可選，但推薦)
   ```bash
   pnpm install -g vercel
   ```

#### 部署步驟

##### 方法一：使用 Vercel 網站介面

1. **連接 GitHub 倉庫**
   - 登入 Vercel 後，點擊 "New Project"
   - 選擇 "Import Git Repository"
   - 連接您的 GitHub 帳號並選擇專案倉庫

2. **配置專案設定**
   - **Framework Preset**: Next.js
   - **Root Directory**: `./` (專案根目錄)
   - **Build Command**: `pnpm build`
   - **Install Command**: `pnpm install`
   - **Output Directory**: `.next` (自動設定)

3. **環境變數設定** (如果需要)
   - 在專案設定中添加必要的環境變數
   - 本專案目前無需額外環境變數

4. **部署專案**
   - 點擊 "Deploy" 開始部署
   - Vercel 會自動偵測並應用 `vercel.json` 配置
   - 部署完成後會獲得預覽 URL 和生產環境 URL

##### 方法二：使用 Vercel CLI

```bash
# 登入 Vercel
vercel login

# 初始化專案 (在專案根目錄執行)
vercel

# 按照提示進行配置：
# - 設定專案名稱
# - 選擇範圍 (個人或團隊)
# - 確認設定

# 首次部署
vercel --prod

# 後續更新部署
vercel --prod
```

#### Vercel 專用配置說明

專案中的 `vercel.json` 配置：

```json
{
  "buildCommand": "pnpm build",
  "installCommand": "pnpm install",
  "framework": "nextjs"
}
```

此配置確保 Vercel 使用正確的建置命令和套件管理器。

### 選項 2: Cloudflare Pages 部署

Cloudflare Pages 提供全球 CDN 支援和優異的效能。

#### 準備工作

1. **註冊 Cloudflare 帳號**
   - 前往 [cloudflare.com](https://cloudflare.com) 註冊帳號

2. **安裝 Wrangler CLI**
   ```bash
   pnpm install -g wrangler
   ```

#### 部署步驟

##### 方法一：使用 Cloudflare 儀表板

1. **連接 GitHub 倉庫**
   - 登入 Cloudflare 儀表板
   - 前往 "Pages" 區段
   - 點擊 "Create a project"
   - 選擇 "Connect to Git"

2. **配置建置設定**
   - **Repository**: 選擇您的專案倉庫
   - **Branch**: 選擇要部署的分支 (通常是 main)
   - **Build settings**:
     - **Build command**: `pnpm build`
     - **Build output directory**: `.next`
     - **Root directory**: `./` (留空或設定為 `/`)

3. **環境變數設定**
   - 在 "Environment variables" 區段添加變數
   - **NODE_VERSION**: `18` (建議設定)

4. **部署專案**
   - 點擊 "Save and Deploy"
   - Cloudflare 會自動建置並部署專案

##### 方法二：使用 Wrangler CLI

1. **登入 Cloudflare**
   ```bash
   wrangler auth login
   ```

2. **初始化 Pages 專案**
   ```bash
   # 在專案根目錄建立 wrangler.toml
   wrangler pages deployment tail

   # 或手動建立 wrangler.toml 檔案
   ```

3. **建立 wrangler.toml 配置**
   ```toml
   name = "erp-admin"
   compatibility_date = "2024-01-01"

   [env.production]
   # 生產環境配置

   [[pages_build_config]]
   # 建置配置
   build_command = "pnpm build"
   destination_dir = ".next"
   root_dir = "."
   ```

4. **部署專案**
   ```bash
   # 首次部署
   wrangler pages deploy .next

   # 或使用 git 整合
   wrangler pages deployment create
   ```

## 建置優化

### Next.js 優化配置

專案已配置以下優化設定：

1. **靜態資源優化**
   - Image 優化
   - CSS 優化
   - JavaScript 打包優化

2. **效能優化**
   - App Router 支援
   - Server Components
   - Streaming SSR

### 套件管理

專案使用 pnpm 作為套件管理器，確保：

- 更快的安裝速度
- 更好的磁碟使用率
- 更嚴格的依賴解析

## 環境變數

### 開發環境
建立 `.env.local` 檔案：
```bash
# 開發環境變數 (如果需要)
NODE_ENV=development
```

### 生產環境
在部署平台設定以下環境變數：
```bash
NODE_ENV=production
```

## 自訂域名

### Vercel 自訂域名
1. 在 Vercel 專案設定中前往 "Domains"
2. 添加您的自訂域名
3. 按照指示配置 DNS 記錄

### Cloudflare 自訂域名
1. 在 Cloudflare Pages 專案設定中前往 "Custom domains"
2. 添加域名
3. Cloudflare 會自動處理 SSL 證書

## 監控與維護

### 部署狀態監控
- **Vercel**: 提供即時建置日誌和效能指標
- **Cloudflare**: 提供建置狀態和訪問統計

### 自動部署
- 推送到主分支時自動觸發重新部署
- 支援預覽部署 (Pull Request)

### 效能監控
- Vercel Analytics (免費)
- Cloudflare Web Analytics
- 自訂效能監控方案

## 故障排除

### 常見問題

1. **建置失敗**
   - 檢查 Node.js 版本 (需要 18+)
   - 確認 pnpm 正確安裝
   - 查看建置日誌中的具體錯誤

2. **環境變數問題**
   - 確保在正確的環境中設定變數
   - 檢查變數名稱拼寫

3. **靜態資源載入問題**
   - 檢查 `public` 目錄中的檔案路徑
   - 確認 Next.js 配置正確

### 支援資源
- [Next.js 部署文檔](https://nextjs.org/docs/deployment)
- [Vercel 文檔](https://vercel.com/docs)
- [Cloudflare Pages 文檔](https://developers.cloudflare.com/pages)

## 部署檢查清單

- [ ] 程式碼推送到 Git 倉庫
- [ ] 環境變數正確設定
- [ ] 建置命令測試通過
- [ ] 靜態資源正確放置
- [ ] 自訂域名配置完成
- [ ] SSL 證書自動生成
- [ ] 效能優化設定完成

---

*本文檔適用於 ERP 管理系統的部署，建議在部署前仔細閱讀相關平台的官方文檔。*
