## 实验三 创建分区表
*2016级软工3班*  
 *陈荣杰*  
 *201610414302* 

#### 使用SQL-DEVELOPER分配权限  
  在编辑用户的限额中，把USER02和USER03后的无限制勾选。  

#### 创建orders表
  <pre>
  [oracle@deep02 ~]$ sqlplus new_user_crj/123@pdborcl

SQL*Plus: Release 12.1.0.2.0 Production on 星期三 10月 31 08:49:31 2018

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

上次成功登录时间: 星期三 10月 24 2018 09:34:00 +08:00

连接到:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options
CREATE TABLE orders
 (
      order_id NUMBER(10, 0) NOT NULL
 STORAGE (   BUFFER_POOL DEFAULT )
 NOCOMPRESS NOPARALLEL
 PARTITION BY RANGE (order_date)
 (
  PARTITION PARTITION_BEFORE_2016 VALUES LESS THAN (
  TO_DATE(' 2016-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS',
  'NLS_CALENDAR=GREGORIAN'))
  , customer_name VARCHAR2(40 BYTE) NOT NULL
  , customer_tel VARCHAR2(40 BYTE) NOT NULL
      , order_date DATE NOT NULL
  , employee_id NUMBER(6, 0) NOT NULL
  , discount NUMBER(8, 2) DEFAULT 0
      , trade_receivable NUMBER(8, 2) DEFAULT 0
 TABLESPACE USERS02
  PCTFREE 10
  INITRANS 1
 )
  STORAGE
 (
  INITIAL 8388608
 TABLESPACE USERS
  NEXT 1048576
  MINEXTENTS 1
  MAXEXTENTS UNLIMITED
  BUFFER_POOL DEFAULT
 PCTFREE 10 INITRANS 1
 )
 NOCOMPRESS NO INMEMORY
 STORAGE (   BUFFER_POOL DEFAULT )
 , PARTITION PARTITION_BEFORE_2018 VALUES LESS THAN (
 TO_DATE(' 2018-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS',
 'NLS_CALENDAR=GREGORIAN'))
 NOLOGGING
 NOCOMPRESS NOPARALLEL
 PARTITION BY RANGE (order_date)
 (
  PARTITION PARTITION_BEFORE_2016 VALUES LESS THAN (
  TO_DATE(' 2016-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS',
  'NLS_CALENDAR=GREGORIAN'))
  NOLOGGING
  TABLESPACE USERS
  PCTFREE 10
  INITRANS 1
  STORAGE
 (
  INITIAL 8388608
  NEXT 1048576
  MINEXTENTS 1
  MAXEXTENTS UNLIMITED
  BUFFER_POOL DEFAULT
 )
 NOCOMPRESS NO INMEMORY
 , PARTITION PARTITION_BEFORE_2017 VALUES LESS THAN (
 TO_DATE(' 2017-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS',
 'NLS_CALENDAR=GREGORIAN'))
 NOLOGGING
 TABLESPACE USERS02
  PCTFREE 10
  INITRANS 1
  STORAGE
 (
  INITIAL 8388608
  NEXT 1048576
  MINEXTENTS 1
  MAXEXTENTS UNLIMITED
  BUFFER_POOL DEFAULT
 )
 NOCOMPRESS NO INMEMORY
 , PARTITION PARTITION_BEFORE_2018 VALUES LESS THAN (
 TO_DATE(' 2018-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS',
 'NLS_CALENDAR=GREGORIAN'))
 NOLOGGING
 TABLESPACE USERS03
 );


表已创建。
  </pre> 
  
  
  <pre>
  ALTER TABLE ORDERS
  ADD CONSTRAINT ORDERS_PK PRIMARY KEY
 (
  ORDER_ID
   )
  ENABLE;

 表已更改。
  </pre>

#### 创建orders_details表
<pre>
 CREATE TABLE order_details
   (
id NUMBER(10, 0) NOT NULL
   , order_id NUMBER(10, 0) NOT NULL
   , product_id VARCHAR2(40 BYTE) NOT NULL
   , product_num NUMBER(8, 2) NOT NULL
   , product_price NUMBER(8, 2) NOT NULL
   , CONSTRAINT order_details_fk1 FOREIGN KEY  (order_id)
REFERENCES orders  (  order_id   )
ENABLE
)
TABLESPACE USERS
PCTFREE 10 INITRANS 1
STORAGE (   BUFFER_POOL DEFAULT )
NOCOMPRESS NOPARALLEL
PARTITION BY REFERENCE (order_details_fk1)
  (
PARTITION PARTITION_BEFORE_2016
NOLOGGING
  TABLESPACE USERS
  PCTFREE 10 INITRANS 1
STORAGE
(
INITIAL 8388608
NEXT 1048576
   MINEXTENTS 1
(
INITIAL 8388608
MAXEXTENTS UNLIMITED
BUFFER_POOL DEFAULT
)
NOCOMPRESS NO INMEMORY,
PARTITION PARTITION_BEFORE_2017
MAXEXTENTS UNLIMITED
NOLOGGING
TABLESPACE USERS02
PCTFREE 10 INITRANS 1
STORAGE
(
INITIAL 8388608
NEXT 1048576
MINEXTENTS 1
MAXEXTENTS UNLIMITED
BUFFER_POOL DEFAULT
)
TABLESPACE USERS03
NOCOMPRESS NO INMEMORY,
PARTITION PARTITION_BEFORE_2018
NOLOGGING
TABLESPACE USERS03
  );
  
表已创建
</pre>  
#### 插入数据  

<pre>
  CREATE SEQUENCE SEQ_TEST
　　　INCREMENT BY 1
　　　START WITH 1
 ;

序列已创建。
  
  
Begin
     for i in 1..2000
     loop
            insert into ORDERS(ORDER_ID,CUSTOMER_NAME,CUSTOMER_TEL,ORDER_DATE,EMPLOYEE_ID,DISCOUNT) 
            VALUES(SEQ_TEST.nextval,'张三',12345,to_date('2016-01-01','yyyy-mm-dd'),SEQ_TEST.nextval,
            SEQ_TEST.nextval);
     end loop;
      commit;
   end;
  /
  PL/SQL 过程已成功完成。  
  
  Begin
     for i in 1..2000
     loop
            insert into ORDERS(ORDER_ID,CUSTOMER_NAME,CUSTOMER_TEL,ORDER_DATE,EMPLOYEE_ID,DISCOUNT) 
            VALUES(SEQ_TEST.nextval,'张三',12345,to_date('2016-01-01','yyyy-mm-dd'),SEQ_TEST.nextval,
            SEQ_TEST.nextval);
     end loop;
      commit;
   end;
  /
  PL/SQL 过程已成功完成。
  
</pre>
