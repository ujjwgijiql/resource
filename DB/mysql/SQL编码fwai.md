# 1、建表规范

   1-1、表名，字段名以下划线分割，名称尽量不要超过3个单词。

   1-2、字段不能为null，如果一定要null，需要说明null与空，null与0等的区别。

   1-3、数据库用utf8mb4编码，字符类型在定义时要指定utf8mb4编码。

       ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci COMMENT='XXX';

       VARCHAR(??) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT 'XXX',

   1-4、建表DDL字段，表名均要有注释。

   1-5、业务表尽量根据业务查询需要，建立相关索引。

   1-6、字符字段长度根据业务需要实际定义，表关联字段定义要一至。

   1-7、日期字段用bigint(20)，时间精确到毫秒。

   1-8、主键不要用自增字段。如为自增主键则不可有意义（系统生成统一唯一主键上线后）

   1-9、注意基本表和扩展表的设计。

   1-10、每个表添加一个扩展字段，用于冗余数据的存储（json格式，key用下划线分割）

   1-11、基本必备字段：

        `create_time` bigint NOT NULL DEFAULT 0 COMMENT '创建时间',

        `modify_time` bigint NOT NULL DEFAULT 0 COMMENT '修改时间',

        `valid` tinyint(1) NOT NULL DEFAULT 1 COMMENT '是否逻辑删除(0:删除)',

        `version`  bigint NOT NULL DEFAULT 1 COMMENT '版本(乐观锁)',

        `create_user_id` bigint NOT NULL DEFAULT 0 COMMENT '创建者id',

        `modify_user_id` bigint NOT NULL DEFAULT 0 COMMENT '修改者id',

        `extend_fields` VARCHAR(1000) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL DEFAULT '' COMMENT '扩展字段',

   1-12、尽量不要用浮点小数字段类型，能转化成整形存储的就用整形，例如：金额用分，显示元时在代码里转化显示。

   1-13、不要使用外键。

   

# 2、查询规范

   2-1、禁止使用NOT IN，不等于(<>)，between。

   2-2、禁止使用OR，用IN替代。（一定要用需要说明）

   2-3、mybatis变量用#{}，不能使用${}

   2-4、业务表的查询需要有索引

   2-5、数据更新用主键做条件，非主键的更新条件要评估。

   2-6、尽量不要用子查询，左、右关联。

   2-7、尽量不要在查询语句中用函数，在业务代码里转化。

   2-8、禁止使用DISTINCT，用GROUP BY去重。

   2-9、禁止存储过程、触发器。

   2-10、禁止select* 查询需写明查询字段，避免全模糊查询，例如 like'%XXX%'，尽量不用模糊查询，最多只允许后模糊查询 like'XXX%'。





# 3、缓存使用规范

   3-1、所有缓存均需要设置过期时间。

   3-2、缓存key长度在50以内。

   3-3、缓存命中率达到80%以上。

   3-4、缓存内容不要太大，20K以内。太大的内容要压缩后现存。

   3-5、不要整个页面一块缓存。

   

# 4、其它

   4-1、业务一览页面，必须有查询条件，数据量大的页面，限制查询时间范围不能超过一个月。

要跳到指定页数的查询考虑使用搜索引擎。数据量少的只显示上一页，下一页。不要显示总页数和直接跳到指定页，用时间比较所在页，尽量不要用limit跳转。

    4-2、所有DDL、DML在上线前，均需要发邮件给DBA做评审。