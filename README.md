```mermaid
flowchart TD

    %% =========================================================
    %% ERPNext — COMPLETE BUSINESS PROCESS
    %% =========================================================

    START(["🚀 BUSINESS START"])

    %% =========================================================
    %% MASTER DATA
    %% =========================================================

    subgraph MASTER["🧩 MASTER DATA & CONFIGURATION"]

        COMPANY["🏢 Company
        Defines legal entity, fiscal year,
        currency, default accounts and
        accounting configuration"]

        PARTY["👤 Customer / Supplier
        Defines business partners,
        contacts, addresses, credit limits
        and accounting relationships"]

        ITEM["📦 Item Master
        Defines products/services,
        UOM, stock behavior, taxes,
        valuation and inventory settings"]

        WAREHOUSE["🏭 Warehouse
        Defines physical/logical inventory
        locations and stock ownership"]

        PRICE["💰 Item Price / Price List
        Defines buying/selling prices,
        pricing rules and conditions"]

        COMPANY --> PARTY
        PARTY --> ITEM
        ITEM --> WAREHOUSE
        WAREHOUSE --> PRICE
    end

    START --> COMPANY
    PRICE --> REQUEST


    %% =========================================================
    %% BUSINESS PROCESS SELECTION
    %% =========================================================

    REQUEST{"🔀 BUSINESS REQUIREMENT"}

    REQUEST -->|Sell| SALES_START
    REQUEST -->|Buy| PURCHASE_START
    REQUEST -->|Inventory| STOCK_START
    REQUEST -->|Accounting| ACCOUNT_START
    REQUEST -->|Manufacturing| MANUFACTURING_START
    REQUEST -->|HR / Payroll| HR_START


    %% =========================================================
    %% SALES — ORDER TO CASH
    %% =========================================================

    subgraph SALES["🟢 SALES — ORDER TO CASH"]

        SALES_START["🛒 Sales Requirement
        Customer wants goods/services"]

        SALES_START --> QUOTATION

        QUOTATION["📄 Quotation
        Price proposal sent to customer"]

        QUOTATION --> QUOTE_DECISION{"Customer accepts?"}

        QUOTE_DECISION -->|No| QUOTE_CLOSE["❌ Closed / Expired
        No sales transaction created"]

        QUOTE_DECISION -->|Yes| SALES_ORDER

        SALES_ORDER["📋 Sales Order
        Official customer order
        and sales commitment"]

        SALES_ORDER --> STOCK_CHECK{"📦 Stock available?"}

        STOCK_CHECK -->|Yes| DELIVERY

        STOCK_CHECK -->|No| REPLENISH["🔄 Replenish Stock
        Purchase or Manufacture"]

        REPLENISH --> DELIVERY

        DELIVERY["🚚 Delivery Note
        Records physical delivery
        and reduces warehouse stock"]

        DELIVERY --> SALE_STOCK["📊 Stock Ledger
        Inventory quantity and
        valuation updated"]

        SALE_STOCK --> SALES_INVOICE

        SALES_INVOICE["🧾 Sales Invoice
        Records revenue, receivable
        and applicable taxes"]

        SALES_INVOICE --> SALE_GL["💳 GL Entry
        Debit Customer Receivable
        Credit Sales / Tax accounts"]

        SALES_INVOICE --> PAYMENT_CHECK{"💵 Customer paid?"}

        PAYMENT_CHECK -->|Yes| SALE_PAYMENT

        PAYMENT_CHECK -->|No| AR["📌 Accounts Receivable
        Customer outstanding remains"]

        SALE_PAYMENT["💰 Payment Entry
        Records customer payment
        against receivable"]

        SALE_PAYMENT --> CUSTOMER_BALANCE
        AR --> CUSTOMER_BALANCE

        CUSTOMER_BALANCE["👤 Customer Balance Updated
        Outstanding amount recalculated"]

    end


    %% =========================================================
    %% PURCHASE — PROCURE TO PAY
    %% =========================================================

    subgraph PURCHASE["🔵 PURCHASE — PROCURE TO PAY"]

        PURCHASE_START["🛍️ Purchase Requirement
        Company requires goods/services"]

        PURCHASE_START --> MATERIAL_REQUEST

        MATERIAL_REQUEST["📋 Material Request
        Internal purchase/material requirement"]

        MATERIAL_REQUEST --> RFQ

        RFQ["📨 Request for Quotation
        Request prices from suppliers"]

        RFQ --> SUPPLIER_QUOTATION

        SUPPLIER_QUOTATION["💬 Supplier Quotation
        Supplier price and terms"]

        SUPPLIER_QUOTATION --> PURCHASE_ORDER

        PURCHASE_ORDER["📑 Purchase Order
        Official commitment to supplier"]

        PURCHASE_ORDER --> PURCHASE_RECEIPT

        PURCHASE_RECEIPT["📦 Purchase Receipt
        Records physical receipt
        of goods into warehouse"]

        PURCHASE_RECEIPT --> PURCHASE_STOCK

        PURCHASE_STOCK["📊 Stock Ledger
        Inventory quantity and
        valuation updated"]

        PURCHASE_STOCK --> PURCHASE_INVOICE

        PURCHASE_INVOICE["🧾 Purchase Invoice
        Records supplier payable,
        expense/asset and taxes"]

        PURCHASE_INVOICE --> PURCHASE_GL

        PURCHASE_GL["💳 GL Entry
        Debit Expense/Asset/Tax
        Credit Supplier Payable"]

        PURCHASE_INVOICE --> SUPPLIER_PAYMENT_CHECK{"💵 Supplier paid?"}

        SUPPLIER_PAYMENT_CHECK -->|Yes| SUPPLIER_PAYMENT

        SUPPLIER_PAYMENT_CHECK -->|No| AP["📌 Accounts Payable
        Supplier outstanding remains"]

        SUPPLIER_PAYMENT["💰 Payment Entry
        Records payment made
        to supplier"]

        SUPPLIER_PAYMENT --> SUPPLIER_BALANCE
        AP --> SUPPLIER_BALANCE

        SUPPLIER_BALANCE["👤 Supplier Balance Updated
        Outstanding amount recalculated"]

    end


    %% =========================================================
    %% STOCK / INVENTORY
    %% =========================================================

    subgraph STOCK["🟠 STOCK & INVENTORY MANAGEMENT"]

        STOCK_START["📦 Stock Operation"]

        STOCK_START --> STOCK_TYPE{"🔀 Operation Type?"}

        STOCK_TYPE -->|Receive| RECEIPT

        RECEIPT["📥 Material Receipt
        Adds inventory into warehouse"]

        STOCK_TYPE -->|Issue| ISSUE

        ISSUE["📤 Material Issue
        Removes inventory for consumption
        or other business purposes"]

        STOCK_TYPE -->|Transfer| TRANSFER

        TRANSFER["🔁 Material Transfer
        Moves inventory between warehouses"]

        STOCK_TYPE -->|Manufacture| STOCK_MANUFACTURE

        STOCK_MANUFACTURE["🏭 Manufacture Stock Entry
        Consumes raw materials and
        creates finished goods"]

        RECEIPT --> STOCK_LEDGER
        ISSUE --> STOCK_LEDGER
        TRANSFER --> STOCK_LEDGER
        STOCK_MANUFACTURE --> STOCK_LEDGER

        STOCK_LEDGER["📊 Stock Ledger Entry
        Permanent inventory movement record"]

        STOCK_LEDGER --> STOCK_QTY

        STOCK_QTY["📦 Warehouse Quantity
        Available stock recalculated"]

        STOCK_LEDGER --> STOCK_VALUE

        STOCK_VALUE["💰 Stock Valuation
        Inventory value recalculated
        using configured valuation method"]

    end


    %% =========================================================
    %% ACCOUNTING
    %% =========================================================

    subgraph ACCOUNTING["🟣 ACCOUNTING & FINANCE"]

        ACCOUNT_START["💼 Accounting Requirement"]

        ACCOUNT_START --> ACCOUNT_TYPE{"🔀 Transaction Type?"}

        ACCOUNT_TYPE -->|Adjustment| JOURNAL

        JOURNAL["📔 Journal Entry
        Manual accounting adjustment,
        accrual or account transfer"]

        ACCOUNT_TYPE -->|Payment| PAYMENT

        PAYMENT["💰 Payment Entry
        Money received or paid"]

        ACCOUNT_TYPE -->|Sales| ACCOUNT_SALES

        ACCOUNT_SALES["🧾 Sales Invoice
        Revenue + Receivable + Tax"]

        ACCOUNT_TYPE -->|Purchase| ACCOUNT_PURCHASE

        ACCOUNT_PURCHASE["🧾 Purchase Invoice
        Expense/Asset + Payable + Tax"]

        JOURNAL --> GL
        PAYMENT --> GL
        ACCOUNT_SALES --> GL
        ACCOUNT_PURCHASE --> GL

        GL["📚 General Ledger
        Central debit/credit accounting record"]

        GL --> RECEIVABLE_PAYABLE

        RECEIVABLE_PAYABLE["👥 Receivable / Payable
        Customer and supplier balances"]

        GL --> TAX_ACCOUNTS

        TAX_ACCOUNTS["🧮 Tax Accounts
        Applicable tax balances"]

        GL --> CASH_BANK

        CASH_BANK["🏦 Cash / Bank
        Cash and bank balances"]

        GL --> INCOME_EXPENSE

        INCOME_EXPENSE["📈 Income / Expense
        Financial performance"]

    end


    %% =========================================================
    %% MANUFACTURING
    %% =========================================================

    subgraph MANUFACTURING["🔴 MANUFACTURING — PLAN TO PRODUCE"]

        MANUFACTURING_START["🏭 Manufacturing Requirement"]

        MANUFACTURING_START --> BOM

        BOM["📋 Bill of Materials
        Defines components, quantities,
        operations and production structure"]

        BOM --> WORK_ORDER

        WORK_ORDER["📝 Work Order
        Creates production requirement"]

        WORK_ORDER --> RAW_CHECK{"Raw materials available?"}

        RAW_CHECK -->|No| RAW_PURCHASE

        RAW_PURCHASE["🛒 Purchase / Replenishment
        Procure missing components"]

        RAW_PURCHASE --> CONSUMPTION

        RAW_CHECK -->|Yes| CONSUMPTION

        CONSUMPTION["📤 Material Consumption
        Raw materials consumed"]

        CONSUMPTION --> PRODUCTION

        PRODUCTION["⚙️ Production
        Manufacturing operation"]

        PRODUCTION --> FINISHED

        FINISHED["📦 Finished Goods
        Completed products received
        into warehouse"]

        FINISHED --> MANUFACTURING_STOCK

        MANUFACTURING_STOCK["📊 Stock + Valuation
        Raw material consumption and
        finished goods valuation updated"]

    end


    %% =========================================================
    %% HR / PAYROLL
    %% =========================================================

    subgraph HR["🟡 HR & PAYROLL"]

        HR_START["👥 Employee Management"]

        HR_START --> EMPLOYEE

        EMPLOYEE["👤 Employee
        Employee master and employment data"]

        EMPLOYEE --> ATTENDANCE

        ATTENDANCE["🕘 Attendance
        Employee presence/absence records"]

        EMPLOYEE --> LEAVE

        LEAVE["🏖️ Leave Application
        Leave request and approval"]

        ATTENDANCE --> PAYROLL
        LEAVE --> PAYROLL

        PAYROLL["💵 Payroll Entry
        Calculates salary, deductions,
        taxes and payable amount"]

        PAYROLL --> SALARY_SLIP

        SALARY_SLIP["📄 Salary Slip
        Employee salary calculation"]

        SALARY_SLIP --> PAYROLL_GL

        PAYROLL_GL["📚 Payroll GL
        Salary expense and payable
        accounting entries"]

    end


    %% =========================================================
    %% VALIDATION & APPROVAL
    %% =========================================================

    subgraph CONTROL["🛡️ VALIDATION, PERMISSION & APPROVAL"]

        DRAFT["📝 Document Created
        Initial Draft"]

        DRAFT --> VALIDATE

        VALIDATE{"✅ Validation passed?"}

        VALIDATE -->|No| ERROR

        ERROR["⚠️ Validation Error
        Mandatory fields, permissions,
        business rules or custom validation"]

        ERROR --> CORRECT

        CORRECT["✏️ Correct Document"]

        CORRECT --> VALIDATE

        VALIDATE -->|Yes| APPROVAL_CHECK

        APPROVAL_CHECK{"🔐 Approval required?"}

        APPROVAL_CHECK -->|No| SUBMIT

        APPROVAL_CHECK -->|Yes| APPROVER

        APPROVER["👨‍💼 Approver Review
        Authorized user reviews
        business transaction"]

        APPROVER --> APPROVAL_DECISION{"Approved?"}

        APPROVAL_DECISION -->|No| REJECT

        REJECT["❌ Rejected
        Transaction returned or stopped"]

        APPROVAL_DECISION -->|Yes| SUBMIT

        SUBMIT["✅ Submit
        Document becomes an official
        ERPNext transaction"]

    end


    %% =========================================================
    %% REPORTING
    %% =========================================================

    subgraph REPORTING["📊 REPORTING & MANAGEMENT"]

        REPORT_START["📑 Submitted Transactions"]

        REPORT_START --> SALES_REPORT
        REPORT_START --> PURCHASE_REPORT
        REPORT_START --> STOCK_REPORT
        REPORT_START --> ACCOUNT_REPORT

        SALES_REPORT["📈 Sales Reports
        Sales orders, invoices,
        revenue and customer performance"]

        PURCHASE_REPORT["📉 Purchase Reports
        Supplier purchases,
        spending and supplier performance"]

        STOCK_REPORT["📦 Stock Reports
        Stock balance, movement,
        valuation and warehouse activity"]

        ACCOUNT_REPORT["💼 Accounting Reports
        GL, receivables, payables,
        financial statements"]

        ACCOUNT_REPORT --> PL

        PL["📊 Profit & Loss
        Revenue − Expenses = Profit/Loss"]

        ACCOUNT_REPORT --> BALANCE

        BALANCE["⚖️ Balance Sheet
        Assets, Liabilities & Equity"]

        ACCOUNT_REPORT --> CASHFLOW

        CASHFLOW["💵 Cash Flow
        Cash inflows and outflows"]

        STOCK_REPORT --> STOCK_BALANCE

        STOCK_BALANCE["📦 Stock Balance
        Item quantity by warehouse"]

        STOCK_REPORT --> STOCK_LEDGER_REPORT

        STOCK_LEDGER_REPORT["📚 Stock Ledger Report
        Detailed inventory movement history"]

    end


    %% =========================================================
    %% SYSTEM FOUNDATION
    %% =========================================================

    subgraph SYSTEM["⚙️ ERPNext SYSTEM LAYER"]

        DOCTYPES["🗂️ DocTypes
        Structured business documents
        and child tables"]

        PERMISSIONS["🔐 Roles & Permissions
        Controls create/read/write/
        submit/cancel access"]

        WORKFLOW["🔄 Workflow
        Controls approval states
        and allowed transitions"]

        NOTIFICATIONS["🔔 Notifications
        Email/system alerts triggered
        by configured events"]

    end


    %% =========================================================
    %% RELATIONSHIPS
    %% =========================================================

    SALES --> REPORTING
    PURCHASE --> REPORTING
    STOCK --> REPORTING
    ACCOUNTING --> REPORTING
    MANUFACTURING --> REPORTING
    HR --> REPORTING

    SUBMIT --> REPORT_START

    DOCTYPES -.-> SALES
    DOCTYPES -.-> PURCHASE
    DOCTYPES -.-> STOCK
    DOCTYPES -.-> ACCOUNTING
    DOCTYPES -.-> MANUFACTURING
    DOCTYPES -.-> HR

    PERMISSIONS -.-> VALIDATE
    WORKFLOW -.-> APPROVAL_CHECK
    NOTIFICATIONS -.-> APPROVER


    %% =========================================================
    %% FINAL STATE
    %% =========================================================

    REPORTING --> END(["🎯 BUSINESS PROCESS COMPLETED"])


    %% =========================================================
    %% COLOR DEFINITIONS
    %% =========================================================

    classDef start fill:#111827,stroke:#111827,color:#ffffff,stroke-width:3px;

    classDef master fill:#E0F2FE,stroke:#0284C7,color:#0C4A6E,stroke-width:2px;

    classDef sales fill:#DCFCE7,stroke:#16A34A,color:#14532D,stroke-width:2px;

    classDef purchase fill:#DBEAFE,stroke:#2563EB,color:#1E3A8A,stroke-width:2px;

    classDef stock fill:#FFEDD5,stroke:#EA580C,color:#7C2D12,stroke-width:2px;

    classDef accounting fill:#F3E8FF,stroke:#9333EA,color:#581C87,stroke-width:2px;

    classDef manufacturing fill:#FEE2E2,stroke:#DC2626,color:#7F1D1D,stroke-width:2px;

    classDef hr fill:#FEF3C7,stroke:#D97706,color:#78350F,stroke-width:2px;

    classDef control fill:#F1F5F9,stroke:#475569,color:#0F172A,stroke-width:2px;

    classDef decision fill:#FEF9C3,stroke:#CA8A04,color:#713F12,stroke-width:3px;

    classDef report fill:#EDE9FE,stroke:#7C3AED,color:#4C1D95,stroke-width:2px;

    classDef system fill:#E2E8F0,stroke:#475569,color:#1E293B,stroke-width:2px;

    classDef error fill:#FEE2E2,stroke:#DC2626,color:#7F1D1D,stroke-width:2px;

    classDef endNode fill:#111827,stroke:#111827,color:#ffffff,stroke-width:3px;


    %% =========================================================
    %% APPLY COLORS
    %% =========================================================

    class START start;
    class COMPANY,PARTY,ITEM,WAREHOUSE,PRICE master;

    class SALES_START,QUOTATION,QUOTE_CLOSE,SALES_ORDER,DELIVERY,SALE_STOCK,SALES_INVOICE,SALE_GL,SALE_PAYMENT,AR,CUSTOMER_BALANCE sales;

    class PURCHASE_START,MATERIAL_REQUEST,RFQ,SUPPLIER_QUOTATION,PURCHASE_ORDER,PURCHASE_RECEIPT,PURCHASE_STOCK,PURCHASE_INVOICE,PURCHASE_GL,SUPPLIER_PAYMENT,AP,SUPPLIER_BALANCE purchase;

    class STOCK_START,RECEIPT,ISSUE,TRANSFER,STOCK_MANUFACTURE,STOCK_LEDGER,STOCK_QTY,STOCK_VALUE stock;

    class ACCOUNT_START,JOURNAL,PAYMENT,ACCOUNT_SALES,ACCOUNT_PURCHASE,GL,RECEIVABLE_PAYABLE,TAX_ACCOUNTS,CASH_BANK,INCOME_EXPENSE accounting;

    class MANUFACTURING_START,BOM,WORK_ORDER,RAW_PURCHASE,CONSUMPTION,PRODUCTION,FINISHED,MANUFACTURING_STOCK manufacturing;

    class HR_START,EMPLOYEE,ATTENDANCE,LEAVE,PAYROLL,SALARY_SLIP,PAYROLL_GL hr;

    class DRAFT,VALIDATE,APPROVAL_CHECK,APPROVER,APPROVAL_DECISION,SUBMIT,CORRECT control;

    class ERROR,REJECT error;

    class SALES_REPORT,PURCHASE_REPORT,STOCK_REPORT,ACCOUNT_REPORT,PL,BALANCE,CASHFLOW,STOCK_BALANCE,STOCK_LEDGER_REPORT report;

    class DOCTYPES,PERMISSIONS,WORKFLOW,NOTIFICATIONS system;

    class REQUEST,QUOTE_DECISION,STOCK_CHECK,PAYMENT_CHECK,SUPPLIER_PAYMENT_CHECK,STOCK_TYPE,ACCOUNT_TYPE,RAW_CHECK,VALIDATE,APPROVAL_CHECK,APPROVAL_DECISION decision;

    class END endNode;
```
