---
date: 2016-06-29 22:16
status: public
tags: mysql,insert
title: Mysql中利用mybatis批量插入和更新
---

从db2_name中的field1， field2字段插入表db1_name的filed1，filed2字段。
```sql
insert into db1_name (filed1, filed2) select field1, field2 from db2_name
```
从多表中查询数据插入到一张表中
```sql
INSERT INTO flora_inventory_detail (creator, gmt_create, modifier, gmt_modified, is_deleted, goods_id, bill_goods_num,stock_goods_num, inventory_id)
        SELECT #{param.creator}, now(), #{param.creator}, now(), 'N', fg.id ,SUM(fwbd.actual_goods_num), fs.goods_num,
        #{param.inventoryId}
        FROM
        flora_wh_bill fwb,
        flora_wh_bill_detail fwbd,
        flora_stock fs,
        flora_warehouse fw,
        flora_goods fg
        WHERE
        fwb.id = fwbd.bill_id AND fwb.wh_id = fs.wh_id
        AND fwb.wh_id = fw.id
        AND fwbd.goods_id = fg.id
        AND fwbd.goods_id = fs.goods_id
        AND fwb.bill_status IN ('YSH' , 'YCX', 'YTH', 'DSH')
        AND fwb.is_deleted = 'N'
        AND fwbd.is_deleted = 'N'
        AND fs.is_deleted = 'N'
        AND fw.is_deleted = 'N'
        AND fg.is_deleted = 'N'
        AND fw.wh_region_id = #{param.regionId}
        AND fs.goods_num != 0
        AND fs.store_id!=1000
        GROUP BY fwb.wh_id , fwbd.goods_id
```

foreach中内容有mybatis生成，拼接SQL语句。
```xml
    <update id="batchUpdateInventoryDetail" parameterType="com...">
        UPDATE flora_inventory_detail a,(
        SELECT 0 AS goods_id, 0 AS actual_goods_num, 0 AS remark, 0 AS inventory_id
        <trim prefix="union">
            <foreach collection="InventoryDetailLi" index="index" item="tag"  separator="union">
                SELECT #{tag.goodsId}, #{tag.actualGoodsNum}, #{tag.remark}, #{tag.inventoryId}
            </foreach>
        </trim>
        )b SET a.actual_goods_num=b.actual_goods_num, a.remark=b.remark
         WHERE  a.goods_id=b.goods_id AND a.inventory_id=b.inventory_id
        AND a.is_deleted='N'
    </update>
```

最后拼接完的语句为:
```sql
 UPDATE flora_inventory_detail a,
 ( 
    SELECT 0 AS goods_id, 0 AS actual_goods_num, 0 AS remark, 0 AS inventory_id 
    union 
    SELECT ?, ?, ?, ? 
    union 


    union 
    SELECT ?, ?, ?, ? 
    union 
    SELECT ?, ?, ?, ? 
    union 
    SELECT ?, ?, ?, ? 
    union 
    SELECT ?, ?, ?, ? 
    union 
    SELECT ?, ?, ?, ? 
  )b 
  SET a.actual_goods_num=b.actual_goods_num, a.remark=b.remark 
  WHERE a.goods_id=b.goods_id 
  AND a.inventory_id=b.inventory_id 
  AND a.is_deleted='N'
``