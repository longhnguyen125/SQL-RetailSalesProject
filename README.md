# SQL-RetailSalesProject
The Retail Sales Database tracks customers, orders, products, and shipments for a retail business. It stores customer details, shipping addresses, purchase orders, products, invoices, and shipping methods. With nine linked tables, it ensures smooth order tracking from purchase to delivery while supporting efficient data management and analysis.

USE MASTER
GO

CREATE DATABASE [RETAILS SALES]
GO

USE [RETAIL SALES]
GO

CREATE TABLE CUSTOMER(
	accountNum CHAR(10) NOT NULL,
	fName VARCHAR(50) NOT NULL,
	lName VARCHAR(50) NOT NULL,
	mInitial CHAR(1),
	streetNum VARCHAR(10) NOT NULL,
	[apt/unitNum] VARCHAR(10),
	streetLine1 VARCHAR(100) NOT NULL,
	streetLine2 VARCHAR(100),
	city VARCHAR(50) NOT NULL,
	stateCode CHAR(2) NOT NULL,
	zipCode CHAR(5) NOT NULL,
	telNum CHAR(10) NOT NULL,
	email VARCHAR(100) NOT NULL,
	CONSTRAINT PK_CUSTOMER
	PRIMARY KEY (accountNum)
);



CREATE TABLE SHIPTO(
	accountNum CHAR(10) NOT NULL,
	shipAddNum CHAR(4) NOT NULL,
	fName VARCHAR(50) NOT NULL,
	lName VARCHAR(50) NOT NULL, 
	mInitial CHAR(1),
	streetNum VARCHAR(10) NOT NULL,
	[apt/unitNum] VARCHAR(10),
	streetLine1 VARCHAR(100) NOT NULL,
	streetLine2 VARCHAR(100),
	city VARCHAR(50) NOT NULL,
	stateCode CHAR(2) NOT NULL,
	zipCode CHAR(5) NOT NULL,
	telNum CHAR(10) NOT NULL,
	email VARCHAR(100),
	CONSTRAINT PK_SHIPTO
	PRIMARY KEY (accountNum, shipAddNum),

	CONSTRAINT FK_SHIPTO
	FOREIGN KEY (accountNum) REFERENCES CUSTOMER (accountNum)
);


CREATE TABLE [SHIP METHODS](
	shipCode CHAR(3) NOT NULL,
	Courier VARCHAR(20) NOT NULL,
	shipMethod VARCHAR(20) NOT NULL,
	typicalNumDaysDel TINYINT NOT NULL,
	CONSTRAINT PK_SHIP_METHODS
	PRIMARY KEY(shipCode)
);


CREATE TABLE [PURCHASE ORDER](
	PONum CHAR(4) NOT NULL,
	accountNum CHAR(10) NOT NULL,
	datePlaced DATE NOT NULL,
	expectedShip	DATE NOT NULL,
	expectedDel DATE NOT NULL,	
	shippingCost SMALLMONEY NOT NULL,
	taxAmount SMALLMONEY NOT NULL,
	otherAdjustments smallmoney NULL,
	shipAddNum CHAR(4) NOT NULL,
	shipCode CHAR(3) NOT NULL,
	CONSTRAINT PK_PONum 
	PRIMARY KEY (PONum, accountNum),

	CONSTRAINT FK_PONum1
	FOREIGN KEY (accountNum) REFERENCES CUSTOMER (accountNum),
	CONSTRAINT FK_PONum2
	FOREIGN KEY (accountNum, shipAddNum) REFERENCES SHIPTO(accountNum, shipAddNum),
	CONSTRAINT FK_PONum3
	FOREIGN KEY (shipCode) REFERENCES [SHIP METHODS](shipCode)
);



CREATE TABLE PRODUCT(
	productNo CHAR(10) NOT NULL,
	pName VARCHAR(100) NOT NULL,
	description VARCHAR(100) NOT NULL,
	detailedDescription VARCHAR(100),
	listPrice smallmoney NOT NULL,

	CONSTRAINT PK_PRODUCT
	PRIMARY KEY(productNo)
);

CREATE TABLE [ORDERED PRODUCTS](
	lineNum CHAR(10) NOT NULL,
	productNo CHAR(10) NOT NULL,
	qtyOrdered tinyint NOT NULL,
	units VARCHAR(10) NOT NULL,
	unitPrice smallmoney NOT NULL,
	discount smallmoney,
	PONum CHAR(4) NOT NULL,
	accountNum CHAR(10) NOT NULL,
	CONSTRAINT PK_ORD_PROD
	PRIMARY KEY (lineNum),

	CONSTRAINT FK_ORD_PROD_PURCHASE
	FOREIGN KEY (PONum, accountNum) 
	REFERENCES [PURCHASE ORDER](PONum, accountNum),

	CONSTRAINT FK_ORD_PROD_PRODUCT
	FOREIGN KEY (productNo)
	REFERENCES PRODUCT(productNo)
);




CREATE TABLE INVOICE(
	invoiceNum CHAR(10) NOT NULL,
	PONum CHAR(4) NOT NULL,
	accountNum CHAR(10) NOT NULL,
	dateInvoiced DATE NOT NULL,
	actualShip DATE NOT NULL,
	actualDel DATE NULL,
	taxCharged SMALLMONEY NOT NULL,
	shippingCostCharged SMALLMONEY NOT NULL,
	additionalDiscount SMALLMONEY NULL,
	actualShipCode CHAR(3) NOT NULL,
	
	CONSTRAINT PK_INVOICE
	PRIMARY KEY (invoiceNum),
	
	CONSTRAINT FK_INVOICE1
	FOREIGN KEY (actualShipCode)
	REFERENCES [SHIP METHODS](shipCode),
	
	CONSTRAINT FK_INVOICE2
	FOREIGN KEY (PONum, accountNum)
	REFERENCES [PURCHASE ORDER](PONum, accountNum)
);

CREATE TABLE [ITEMS SHIPPED](
	invoiceNum CHAR(10) NOT NULL,
	lineNum CHAR(10) NOT NULL,
	qtyShipped tinyint NOT NULL,

	CONSTRAINT PK_ITEMS_SHIPPED
	PRIMARY KEY (invoiceNum, lineNum),

	CONSTRAINT FK_ITEMS_SHIP1
	FOREIGN KEY (lineNum) 
	REFERENCES [ORDERED PRODUCTS](lineNum),

	CONSTRAINT FK_ITEMS_SHIP2
	FOREIGN KEY (invoiceNum)
	REFERENCES INVOICE (invoiceNum)
);


CREATE TABLE SHIPMENT(
	trackingCode CHAR(18) NOT NULL,
	invoiceNum CHAR(10) NOT NULL,
	datetimePickedup smalldatetime,
	datetimeDelivered smalldatetime,
	weightLBS decimal(5,2) NOT NULL,

	CONSTRAINT PK_SHIPMENT
	PRIMARY KEY(trackingCode, invoiceNum),

	CONSTRAINT FK_SHIPMENT
	FOREIGN KEY (invoiceNum) 
	REFERENCES INVOICE (invoiceNum)
);
