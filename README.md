
USE [ShoesDB];
GO

-- =============================================
-- 1. Таблица Category (Категории товаров)
-- =============================================
CREATE TABLE dbo.Category
(
    Id   INT IDENTITY(1,1) NOT NULL,
    Name NVARCHAR(25) NOT NULL,

    CONSTRAINT PK_Category PRIMARY KEY (Id)
);
GO

-- =============================================
-- 2. Таблица Producer (Производители)
-- =============================================
CREATE TABLE dbo.Producer
(
    Id   INT IDENTITY(1,1) NOT NULL,
    Name NVARCHAR(25) NOT NULL,

    CONSTRAINT PK_Producer PRIMARY KEY (Id)
);
GO

-- =============================================
-- 3. Таблица Provider (Поставщики)
-- =============================================
CREATE TABLE dbo.Provider
(
    Id   INT IDENTITY(1,1) NOT NULL,
    Name NVARCHAR(25) NOT NULL,

    CONSTRAINT PK_Provider PRIMARY KEY (Id)
);
GO

-- =============================================
-- 4. Таблица Unit (Единицы измерения)
-- =============================================
CREATE TABLE dbo.Unit
(
    Id   INT IDENTITY(1,1) NOT NULL,
    Name NVARCHAR(5) NOT NULL,

    CONSTRAINT PK_Unit PRIMARY KEY (Id)
);
GO

-- =============================================
-- 5. Таблица Role (Роли пользователей)
-- =============================================
CREATE TABLE dbo.Role
(
    Id   INT IDENTITY(1,1) NOT NULL,
    Name NVARCHAR(25) NOT NULL,

    CONSTRAINT PK_Role PRIMARY KEY (Id)
);
GO

-- =============================================
-- 6. Таблица OrderStatus (Статусы заказов)
-- =============================================
CREATE TABLE dbo.OrderStatus
(
    Id   INT IDENTITY(1,1) NOT NULL,
    Name NVARCHAR(20) NOT NULL,

    CONSTRAINT PK_OrderStatus PRIMARY KEY (Id)
);
GO

-- =============================================
-- 7. Таблица PickUpPoint (Пункты выдачи)
-- =============================================
CREATE TABLE dbo.PickUpPoint
(
    Id       INT IDENTITY(1,1) NOT NULL,
    PostCode NVARCHAR(6) NOT NULL,
    City     NVARCHAR(30) NOT NULL,
    Street   NVARCHAR(30) NOT NULL,
    Building NVARCHAR(6) NULL,

    CONSTRAINT PK_PickUpPoint PRIMARY KEY (Id)
);
GO

-- =============================================
-- 8. Таблица User (Пользователи)
-- =============================================
CREATE TABLE dbo.[User]
(
    Id         INT IDENTITY(1,1) NOT NULL,
    Surname    NVARCHAR(30) NOT NULL,
    Name       NVARCHAR(30) NOT NULL,
    Patronymic NVARCHAR(30) NULL,
    Login      NVARCHAR(30) NOT NULL,
    Password   NVARCHAR(30) NOT NULL,
    RoleId     INT NOT NULL,

    CONSTRAINT PK_User PRIMARY KEY (Id),

    CONSTRAINT FK_User_Role
        FOREIGN KEY (RoleId)
        REFERENCES dbo.Role(Id)
);
GO

-- =============================================
-- 9. Таблица Product (Товары)
-- =============================================
CREATE TABLE dbo.Product
(
    Id            INT IDENTITY(1,1) NOT NULL,
    Article       NVARCHAR(10) NOT NULL,
    Name          NVARCHAR(30) NOT NULL,
    UnitId        INT NOT NULL,
    Price         MONEY NOT NULL,
    ProviderId    INT NOT NULL,
    ProductId     INT NOT NULL,
    CategoryId    INT NOT NULL,
    Discount      DECIMAL(4,2) NOT NULL,
    AmountInStock DECIMAL(6,2) NOT NULL,
    Description   NVARCHAR(MAX) NOT NULL,
    Photo         NVARCHAR(MAX) NULL,

    CONSTRAINT PK_Product PRIMARY KEY (Id),

    CONSTRAINT FK_Product_Unit
        FOREIGN KEY (UnitId)
        REFERENCES dbo.Unit(Id),

    CONSTRAINT FK_Product_Provider
        FOREIGN KEY (ProviderId)
        REFERENCES dbo.Provider(Id),

    -- На скриншоте столбец называется ProductId,
    -- а связь ведёт к таблице Producer.
    CONSTRAINT FK_Product_Producer
        FOREIGN KEY (ProductId)
        REFERENCES dbo.Producer(Id),

    CONSTRAINT FK_Product_Category
        FOREIGN KEY (CategoryId)
        REFERENCES dbo.Category(Id)
);
GO

-- =============================================
-- 10. Таблица [Order] (Заказы)
-- =============================================
CREATE TABLE dbo.[Order]
(
    Id           INT IDENTITY(1,1) NOT NULL,
    CreationDate DATETIME NOT NULL,
    DeliveryDate DATE NOT NULL,
    PickUpPointId INT NOT NULL,
    UserId       INT NOT NULL,
    ReceiptCode  NVARCHAR(4) NOT NULL,
    StatusId     INT NOT NULL,

    CONSTRAINT PK_Order PRIMARY KEY (Id),

    CONSTRAINT FK_Order_PickUpPoint
        FOREIGN KEY (PickUpPointId)
        REFERENCES dbo.PickUpPoint(Id),

    CONSTRAINT FK_Order_User
        FOREIGN KEY (UserId)
        REFERENCES dbo.[User](Id),

    CONSTRAINT FK_Order_OrderStatus
        FOREIGN KEY (StatusId)
        REFERENCES dbo.OrderStatus(Id)
);
GO

-- =============================================
-- 11. Таблица ProductInOrder (Товары в заказе)
-- =============================================
CREATE TABLE dbo.ProductInOrder
(
    Id        INT IDENTITY(1,1) NOT NULL,
    OrderId   INT NOT NULL,
    ProductId INT NOT NULL,
    Amount    INT NOT NULL,

    CONSTRAINT PK_ProductInOrder PRIMARY KEY (Id),

    CONSTRAINT FK_ProductInOrder_Order
        FOREIGN KEY (OrderId)
        REFERENCES dbo.[Order](Id),

    CONSTRAINT FK_ProductInOrder_Product
        FOREIGN KEY (ProductId)
        REFERENCES dbo.Product(Id)
);
GO

-- =============================================
-- Создание завершено
-- =============================================
PRINT N'Все 11 таблиц успешно созданы.';
GO
