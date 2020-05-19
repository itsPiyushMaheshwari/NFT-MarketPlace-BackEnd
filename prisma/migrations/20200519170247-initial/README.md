# Migration `20200519170247-initial`

This migration has been generated by Manthankumar Satani at 5/19/2020, 5:02:47 PM.
You can check out the [state of the schema](./schema.prisma) after the migration.

## Database Steps

```sql
CREATE TABLE "public"."bids" (
"active" boolean   DEFAULT true,"created" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"id" SERIAL,"orders" integer  NOT NULL ,"price" Decimal(65,30)  NOT NULL ,"status" integer   DEFAULT 0,"updated" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"users" integer  NOT NULL ,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."categories" (
"active" boolean   DEFAULT true,"address" text  NOT NULL ,"created" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"description" text   ,"id" SERIAL,"img_url" text   ,"name" text   ,"updated" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"url" text   ,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."categoryaddresses" (
"active" boolean   DEFAULT true,"address" text  NOT NULL ,"category" integer  NOT NULL ,"chain_id" text  NOT NULL ,"created" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"id" SERIAL,"updated" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."erc20tokenaddresses" (
"active" boolean   DEFAULT true,"address" text  NOT NULL ,"chain_id" text  NOT NULL ,"created" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"id" SERIAL,"token" integer  NOT NULL ,"updated" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."erc20tokens" (
"active" boolean   DEFAULT true,"address" text  NOT NULL ,"created" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"decimal" integer  NOT NULL ,"icon" text   ,"id" SERIAL,"name" text   ,"symbol" text   ,"updated" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."favorites" (
"created" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"id" SERIAL,"token" integer  NOT NULL ,"updated" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"users" integer  NOT NULL ,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."orders" (
"active" boolean   DEFAULT true,"created" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"expiry_date" timestamp(3)   ,"id" SERIAL,"maker_address" integer  NOT NULL ,"maker_amount" Decimal(65,30)  NOT NULL ,"maker_token" integer  NOT NULL ,"min_price" Decimal(65,30)  NOT NULL ,"signature" text   ,"status" integer   DEFAULT 0,"taker_address" integer   ,"taker_amount" Decimal(65,30)   ,"taker_token" integer  NOT NULL ,"txhash" text   ,"type" text  NOT NULL ,"updated" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"views" integer   DEFAULT 0,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."tokens" (
"active" boolean   DEFAULT true,"address" text  NOT NULL ,"category" integer  NOT NULL ,"created" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"description" text   ,"id" SERIAL,"img_url" text   ,"metadata" jsonb   ,"name" text   ,"owner" integer  NOT NULL ,"updated" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY ("id"))

CREATE TABLE "public"."users" (
"active" boolean   DEFAULT true,"address" text  NOT NULL ,"created" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,"id" SERIAL,"updated" timestamp(3)  NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY ("id"))

CREATE UNIQUE INDEX "categories.address" ON "public"."categories"("address")

CREATE UNIQUE INDEX "users.address" ON "public"."users"("address")

ALTER TABLE "public"."bids" ADD FOREIGN KEY ("orders")REFERENCES "public"."orders"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."bids" ADD FOREIGN KEY ("users")REFERENCES "public"."users"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."categoryaddresses" ADD FOREIGN KEY ("category")REFERENCES "public"."categories"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."erc20tokenaddresses" ADD FOREIGN KEY ("token")REFERENCES "public"."erc20tokens"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."favorites" ADD FOREIGN KEY ("token")REFERENCES "public"."tokens"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."favorites" ADD FOREIGN KEY ("users")REFERENCES "public"."users"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."orders" ADD FOREIGN KEY ("maker_address")REFERENCES "public"."users"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."orders" ADD FOREIGN KEY ("maker_token")REFERENCES "public"."tokens"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."orders" ADD FOREIGN KEY ("taker_address")REFERENCES "public"."users"("id") ON DELETE SET NULL  ON UPDATE CASCADE

ALTER TABLE "public"."orders" ADD FOREIGN KEY ("taker_token")REFERENCES "public"."erc20tokens"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."tokens" ADD FOREIGN KEY ("category")REFERENCES "public"."categories"("id") ON DELETE CASCADE  ON UPDATE CASCADE

ALTER TABLE "public"."tokens" ADD FOREIGN KEY ("owner")REFERENCES "public"."users"("id") ON DELETE CASCADE  ON UPDATE CASCADE
```

## Changes

```diff
diff --git schema.prisma schema.prisma
migration ..20200519170247-initial
--- datamodel.dml
+++ datamodel.dml
@@ -1,0 +1,137 @@
+generator client {
+  provider = "prisma-client-js"
+}
+
+datasource db {
+  provider = "postgresql"
+  url      = env("DATABASE_URL")
+}
+
+model bids {
+  active      Boolean? @default(true)
+  created     DateTime @default(now())
+  id          Int      @default(autoincrement()) @id
+  orders      Int
+  price       Float
+  status      Int?     @default(0)
+  updated     DateTime @default(now())
+  users       Int
+  orders_bids orders   @relation(fields: [orders], references: [id])
+  users_bids  users    @relation(fields: [users], references: [id])
+}
+
+model categories {
+  active            Boolean?            @default(true)
+  address           String              @unique
+  created           DateTime            @default(now())
+  description       String?
+  id                Int                 @default(autoincrement()) @id
+  img_url           String?
+  name              String?
+  updated           DateTime            @default(now())
+  url               String?
+  categoryaddresses categoryaddresses[]
+  tokens            tokens[]
+}
+
+model categoryaddresses {
+  active     Boolean?   @default(true)
+  address    String
+  category   Int
+  chain_id   String
+  created    DateTime   @default(now())
+  id         Int        @default(autoincrement()) @id
+  updated    DateTime   @default(now())
+  categories categories @relation(fields: [category], references: [id])
+}
+
+model erc20tokenaddresses {
+  active      Boolean?    @default(true)
+  address     String
+  chain_id    String
+  created     DateTime    @default(now())
+  id          Int         @default(autoincrement()) @id
+  token       Int
+  updated     DateTime    @default(now())
+  erc20tokens erc20tokens @relation(fields: [token], references: [id])
+}
+
+model erc20tokens {
+  active              Boolean?              @default(true)
+  address             String
+  created             DateTime              @default(now())
+  decimal             Int
+  icon                String?
+  id                  Int                   @default(autoincrement()) @id
+  name                String?
+  symbol              String?
+  updated             DateTime              @default(now())
+  erc20tokenaddresses erc20tokenaddresses[]
+  orders              orders[]
+}
+
+model favorites {
+  created         DateTime @default(now())
+  id              Int      @default(autoincrement()) @id
+  token           Int
+  updated         DateTime @default(now())
+  users           Int
+  tokens          tokens   @relation(fields: [token], references: [id])
+  users_favorites users    @relation(fields: [users], references: [id])
+}
+
+model orders {
+  active               Boolean?    @default(true)
+  created              DateTime    @default(now())
+  expiry_date          DateTime?
+  id                   Int         @default(autoincrement()) @id
+  maker_address        Int
+  maker_amount         Float
+  maker_token          Int
+  min_price            Float
+  signature            String?
+  status               Int?        @default(0)
+  taker_address        Int?
+  taker_amount         Float?
+  taker_token          Int
+  txhash               String?
+  type                 String
+  updated              DateTime    @default(now())
+  views                Int?        @default(0)
+  maker_addressTousers users       @relation("orders_maker_addressTousers", fields: [maker_address], references: [id])
+  tokens               tokens      @relation(fields: [maker_token], references: [id])
+  taker_addressTousers users?      @relation("orders_taker_addressTousers", fields: [taker_address], references: [id])
+  erc20tokens          erc20tokens @relation(fields: [taker_token], references: [id])
+  bids                 bids[]
+}
+
+model tokens {
+  active      Boolean?    @default(true)
+  address     String
+  category    Int
+  created     DateTime    @default(now())
+  description String?
+  id          Int         @default(autoincrement()) @id
+  img_url     String?
+  metadata    Json?
+  name        String?
+  owner       Int
+  updated     DateTime    @default(now())
+  categories  categories  @relation(fields: [category], references: [id])
+  users       users       @relation(fields: [owner], references: [id])
+  favorites   favorites[]
+  orders      orders[]
+}
+
+model users {
+  active                             Boolean?    @default(true)
+  address                            String      @unique
+  created                            DateTime    @default(now())
+  id                                 Int         @default(autoincrement()) @id
+  updated                            DateTime    @default(now())
+  bids                               bids[]
+  favorites                          favorites[]
+  orders_orders_maker_addressTousers orders[]    @relation("orders_maker_addressTousers")
+  orders_orders_taker_addressTousers orders[]    @relation("orders_taker_addressTousers")
+  tokens                             tokens[]
+}
```

