# SQLserver 如何给表新建触发器

## 一. 需要数据库hzppb（汉字拼音表）和Xsxxb（学生信息表）



## 二. 对Xsxxb进行更新时的触发器

```SQL
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TRIGGER update_xmsx_after_update_xsxxb_xm 
   ON  dbo.Xsxxb 
   AFTER UPDATE --在更新数据时对表中数据进行更新
AS 
BEGIN
	IF UPDATE(xm) --如果表中的xm字段发生了更新，才执行对表中xmsx字段的更新
	BEGIN
		-- SET NOCOUNT ON added to prevent extra result sets from
		-- interfering with SELECT statements.
		SET NOCOUNT ON;
		DECLARE @xsid INT,@xm VARCHAR(50); --定义变量 ，@xm 姓名 可变字符串长度为五十 @xsid 学生ID 整型变量
		SELECT @xsid=xsid,@xm=xm FROM Inserted;--在执行插入的操作后，在Inserted的临时表中获取xsid和xm，并将其赋给@xsid和@xm
		UPDATE Xsxxb SET xmsx=dbo.PysxCx(@xm) WHERE xsid=@xsid;-- 更新Xsxxb中的数据，使用函数PysxCx在表hzpyb中查询@xm对应的xmsx(姓名缩写)，
		-- 并当@xsid 和Xsxxb表中的xsid相同时，更新对应的xmsx
		-- Insert statements for trigger here
	END
	else
		print '没有更新姓名，不对表的xmsx进行更新'
END
GO
```

## 三. 对表Xsxxb进行更新时的触发器

```sql
USE SSMSTest --使用的数据库名称
go
set ANSI_NULLS ON
GO
Set QUOTED_IDENTIFIER ON
GO
CREATE TRIGGER Update_xmsx_after_insert_xsxxb
	ON dbo.Xsxxb
	AFTER INSERT --在插入数据后对Xsxxb进行更新
AS
BEGIN
	SET NOCOUNT ON;
	DECLARE @xm VARCHAR(50),@xsid INT; --定义变量，@xm 姓名 可变字符串长度为五十 @xsid 学生ID 整型变量
	SELECT @xsid=xsid,@xm=xm FROM Inserted; --在执行插入的操作后，在Inserted的临时表中获取xsid和xm，并将其赋给@xsid和@xm
	UPDATE Xsxxb SET xmsx=dbo.PysxCx(@xm) where xsid=@xsid; -- 更新Xsxxb中的数据，使用函数PysxCx在表hzpyb中查询@xm对应的xmsx(姓名缩写)，
	-- 并当@xsid 和Xsxxb表中的xsid相同时，更新对应的xmsx
END

```

