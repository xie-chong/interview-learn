# critical_data_hash表重建primary_key, 主要分为2阶段操作, 系统重启前的备份和系统重启后(表结构重建后)的数据迁移

# 若原始表中已经存在JPA注解 @ID 修饰的物理主键, 且表中有数据, @ID修改为其他字段后, 系统重启后不再重新生成新的primary_key（故选择原始数据备份后，drop操作，让jpa重新创建表）

# 系统重启前操作
# 1. 先创建表 acc_loan.critical_data_hash_temp

```
create table acc_loan.critical_data_hash_temp
(
    id                 bigint(50) auto_increment
        primary key,
    due_bill_no        varchar(50)  null comment '借据号',
    apply_no           varchar(32)  null comment '进件申请单号',
    project_no         varchar(20)  null comment '项目编号',
    user_id            varchar(100) null comment '用户编号',
    user_name          varchar(100) null comment '用户姓名',
    user_name_hash     varchar(100) null comment '用户姓名哈希值',
    id_no              varchar(50)  null comment '证件号码',
    id_no_hash         varchar(100) null comment '证件号码哈希值',
    phone              varchar(50)  null comment '手机号',
    phone_hash         varchar(100) null comment '手机号哈希值',
    bank_card_no       varchar(50)  null comment '银行卡原值',
    bank_card_no_hash  varchar(100) null comment '银行卡哈希值',
    car_no             varchar(50)  null comment '车牌号',
    car_no_hash        varchar(100) null comment '车牌号哈希值',
    address            varchar(255) null comment '住址',
    address_hash       varchar(100) null comment '住址哈希值',
    created_date       datetime(6)  null,
    last_modified_date datetime(6)  null
)
    comment '敏感数据哈希表';
```

# 2. 数据备份

```
insert into acc_loan.critical_data_hash_temp (
                                              due_bill_no,
                                              project_no,
                                              user_id,
                                              user_name,
                                              user_name_hash,
                                              id_no,
                                              id_no_hash,
                                              phone,
                                              phone_hash,
                                              bank_card_no,
                                              bank_card_no_hash,
                                              car_no,
                                              car_no_hash,
                                              address,
                                              address_hash,
                                              created_date,
                                              last_modified_date)
SELECT
       due_bill_no,
       project_no,
       user_id,
       user_name,
       user_name_hash,
       id_no,
       id_no_hash,
       phone,
       phone_hash,
       bank_card_no,
       bank_card_no_hash,
       car_no,
       car_no_hash,
       address,
       address_hash,
       created_date,
       last_modified_date
FROM acc_loan.critical_data_hash;
```

# 3. 删除旧表( 待系统重启后自动重构)
```
drop table acc_loan.critical_data_hash;
```

# 系统重启后操作
# 4. 临时表数据迁移回重构后的旧表( 需系统重启后操作)
# 该语法依赖表结构的字段排序

```
#insert into acc_loan.critical_data_hash  (SELECT * FROM acc_loan.critical_data_hash_temp);

insert into acc_loan.critical_data_hash (id,
                                         due_bill_no,
                                         apply_no,
                                         project_no,
                                         user_id,
                                         user_name,
                                         user_name_hash,
                                         id_no,
                                         id_no_hash,
                                         phone,
                                         phone_hash,
                                         bank_card_no,
                                         bank_card_no_hash,
                                         car_no,
                                         car_no_hash,
                                         address,
                                         address_hash,
                                         created_date,
                                         last_modified_date)
SELECT id,
       due_bill_no,
       apply_no,
       project_no,
       user_id,
       user_name,
       user_name_hash,
       id_no,
       id_no_hash,
       phone,
       phone_hash,
       bank_card_no,
       bank_card_no_hash,
       car_no,
       car_no_hash,
       address,
       address_hash,
       created_date,
       last_modified_date
FROM acc_loan.critical_data_hash_temp;
```


# 5. 删除临时表( 需确认数据已经迁移到重构后的数据表critical_data_hash)

```
SELECT count(*) temp_count FROM acc_loan.critical_data_hash_temp;
SELECT count(*) old_count FROM acc_loan.critical_data_hash;

drop table acc_loan.critical_data_hash_temp;
```
