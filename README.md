# 記帳網站前端（Vue 3）

這個專案是依照你提供的兩張畫面做的 **深色系記帳 UI 前端雛形**，目前先聚焦在：

- 月份總覽（Expense / Income / Balance 圓環）
- 記帳輸入面板（類別格、備註、金額鍵盤）

## 快速啟動

```bash
npm install
npm run dev
```

預設會開在 `http://localhost:5173`。

---

## 後端 API（Python）建議架構

建議使用 **FastAPI + SQLAlchemy + PostgreSQL**。

### 1) 目錄建議

```txt
backend/
  app/
    main.py
    core/
      config.py
      security.py
    db/
      session.py
      models.py
      migrations/
    schemas/
      auth.py
      transaction.py
      category.py
      report.py
    routers/
      auth.py
      categories.py
      transactions.py
      reports.py
    services/
      transaction_service.py
      report_service.py
```

### 2) 核心資料表

- `users`
  - `id`, `email`, `password_hash`, `created_at`
- `accounts`（可選，多錢包）
  - `id`, `user_id`, `name`, `currency`
- `categories`
  - `id`, `user_id`(nullable，系統預設可為 null), `type`(`expense|income`), `name`, `icon`, `color`
- `transactions`
  - `id`, `user_id`, `account_id`, `category_id`, `type`, `amount`, `currency`, `note`, `occurred_at`, `created_at`
- `tags`、`transaction_tags`（可選）

### 3) REST API 設計

#### Auth
- `POST /api/v1/auth/register`
- `POST /api/v1/auth/login`
- `POST /api/v1/auth/refresh`

#### Categories
- `GET /api/v1/categories?type=expense`
- `POST /api/v1/categories`
- `PATCH /api/v1/categories/{id}`
- `DELETE /api/v1/categories/{id}`

#### Transactions
- `GET /api/v1/transactions?start=2026-03-01&end=2026-03-31&type=expense`
- `POST /api/v1/transactions`
- `PATCH /api/v1/transactions/{id}`
- `DELETE /api/v1/transactions/{id}`

#### Reports / Dashboard
- `GET /api/v1/reports/monthly-summary?month=2026-03`
  - 回傳：`expense_total`, `income_total`, `balance`, `category_breakdown`
- `GET /api/v1/reports/daily-groups?month=2026-03`
  - 回傳：依日期分組的交易清單（對應你第二張圖的列表）

### 4) 與 Vue 前端的資料契約（最小可用）

#### 新增交易 `POST /transactions`

```json
{
  "type": "expense",
  "category_id": 3,
  "amount": 126,
  "currency": "TWD",
  "note": "Lunch",
  "occurred_at": "2026-03-09T12:30:00+08:00"
}
```

#### 月報表 `GET /reports/monthly-summary`

```json
{
  "month": "2026-03",
  "expense_total": 4491,
  "income_total": 0,
  "balance": -4491,
  "category_breakdown": [
    { "category": "Lunch", "amount": 716, "percent": 0.16 },
    { "category": "Traffic", "amount": 160, "percent": 0.04 }
  ]
}
```

### 5) FastAPI 範例骨架

```python
# app/main.py
from fastapi import FastAPI
from app.routers import transactions, categories, reports

app = FastAPI(title="Bookkeeping API")

app.include_router(transactions.router, prefix="/api/v1/transactions", tags=["transactions"])
app.include_router(categories.router, prefix="/api/v1/categories", tags=["categories"])
app.include_router(reports.router, prefix="/api/v1/reports", tags=["reports"])
```

### 6) 開發順序建議

1. 先完成 `categories` + `transactions` CRUD
2. 補 `monthly-summary` / `daily-groups` 報表
3. 最後做 auth + refresh token + 權限隔離
4. 加上資料庫 migration（Alembic）與測試（pytest）

