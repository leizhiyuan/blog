title: CSV导入导出工具
tags:
  - C＃
  - 大学
  - 数据库
  - 编程
  - 翻译
  - 软件
id: 1790
categories:
  - 我的翻译
date: 2012-09-30 22:33:19
---

[![]({{BASE_PATH}}/images/650890bebf13c4d041d3113af4b40fb93a2cc0c5.png "form1")](http://leaverimage.b0.upaiyun.com/27639_o.png)
**介绍**

本文介绍并且实现了在平面文件CSV和SQL server之间的导入导出功能。使用VS2005写的。使用了.net 2.0

本文基于前一篇文章：[从CSV导入数据并存储到数据库](http://www.codeproject.com/KB/database/FinalCSVReader.aspx)，本文包含了新功能，比如，导出功能，在数据库创建表，批量拷贝。接下来的例子中有很多注释。

**导入**

通过ODBC驱动连接到一个CSV文件，然后把文件读到一张表了（基于前面提到的那篇文章）
使用不同的编码和不同的行分隔符（基于前文）
加载CSV文件到DataSet（基于前文）
如何显示对CSV文件的预览（基于前文）
通过SqlBulkCopy的对象向SQL server转移数据，原始数据是DataSet实例

使用结构表，基于CSV文件，创建一个新表
使用事件来处理批量拷贝的进程
**导出**

浏览SQL 数据库中的用户表
使用不同的编码和分隔符
使用SqlDataReader读取数据，使用StreamWriter转移数据到平面文件

[![]({{BASE_PATH}}/images/b131fd0af27287ab9270df33d1a313ab957dfb12.png "form2")](http://leaverimage.b0.upaiyun.com/27640_o.png)
使用
1.	下载工程
2.	新建一个数据库，或者使用一个存在的数据库
3.	修改软件中的数据库连接字符串，在prop.cs文件中

<pre class="lang:c# decode:true " >public static string sqlConnString = "server=(local);
	database=Test_CSV_impex;Trusted_Connection=True";</pre> 

4.	运行工程

**一些代码段**
加载CSV到DataSet中
<pre class="lang:c# decode:true " >
/*
 *加载CSV到DataSet.
 * 
 * 如果numberOfRows parameter 是 -1, 就加载所有行, 否则就加载指定数目的行
 */

public DataSet LoadCSV(int numberOfRows)
{
	DataSet ds = new DataSet();
	try
	{
		// 创建并打开ODBC连接
		string strConnString = "Driver={Microsoft Text Driver (*.txt; *.csv)};
			Dbq=" + this.dirCSV.Trim() + ";
			Extensions=asc,csv,tab,txt;Persist Security Info=False";
		string sql_select;
		OdbcConnection conn;
		conn = new OdbcConnection(strConnString.Trim());
		conn.Open();

		//创建SQL语句
		if (numberOfRows == -1)
		{
			sql_select = "select * from [" + 
					this.FileNevCSV.Trim() + "]";
		}
		else
		{
			sql_select = "select top " + numberOfRows + 
				" * from [" + this.FileNevCSV.Trim() + "]";
		}

		//创建数据适配器 
		OdbcDataAdapter obj_oledb_da = new OdbcDataAdapter(sql_select, conn);

		//用CSV的数据填充DataSet
		obj_oledb_da.Fill(ds, "csv");

		//关闭连接
		conn.Close();
	}
	catch (Exception e) //异常处理
	{
		MessageBox.Show(e.Message, "Error - LoadCSV",
				MessageBoxButtons.OK,MessageBoxIcon.Error);
	}
	return ds;
}</pre>

通过SqlBulkCopy从ODBC连接中转移数据到数据库
<pre class="lang:c# decode:true " >/*
 *通过SqlBulkCopy从ODBC连接中转移数据到数据库
 * 该方法不使用临时的dataset，而是直接从ODBC连接中加载数据
 */

private void SaveToDatabaseDirectly()
{
	try
	{
		if (fileCheck())
		{
			// 选择 格式，编码 ，并且写结构表			
Format();
			Encoding();
			writeSchema();

			// 创建并打开ODBC连接
			string strConnString = "Driver={Microsoft Text Driver 
				(*.txt; *.csv)};Dbq=" + this.dirCSV.Trim() + ";
				Extensions=asc,csv,tab,txt;
				Persist Security Info=False";
			string sql_select;
			OdbcConnection conn;
			conn = new OdbcConnection(strConnString.Trim());
			conn.Open();

			//通过SQL语句统计CVS的行数
			OdbcCommand commandRowCount = new OdbcCommand
				("SELECT COUNT(*) FROM [" + 
				this.FileNevCSV.Trim() + "]", conn);
			this.rowCount = System.Convert.ToInt32
					(commandRowCount.ExecuteScalar());

			// 创建ODBC命令
			sql_select = "select * from [" + 
					this.FileNevCSV.Trim() + "]";
			OdbcCommand commandSourceData = 
				new OdbcCommand(sql_select, conn);

			// 创建OdbcDataReader读取CSV数据
			OdbcDataReader dataReader = 
				commandSourceData.ExecuteReader();

			// 创建结构表. 
                           // 提供列名来创建表
			DataTable dt;
			dt = dataReader.GetSchemaTable();

			// 可以预览表:
			//this.dataGridView_preView.DataSource = dt;

			// 创建一个新的空表
			CreateTableInDatabase(dt, this.txtOwner.Text, 
				this.txtTableName.Text, prop.sqlConnString);

			//从原数据拷贝数据到数据库
			using (SqlBulkCopy bc = new SqlBulkCopy
				("server=(local);database=Test_CSV_impex;
				Trusted_Connection=True"))
			{
				// 本例不检查表所有者和表名
				bc.DestinationTableName = "[" + 
					this.txtOwner.Text + "].[" + 
					this.txtTableName.Text + "]";

				// 通过SqlRowsCopied事件来通知
				bc.NotifyAfter = 100;
				bc.SqlRowsCopied += 
				   new SqlRowsCopiedEventHandler(OnSqlRowsCopied);

				// 开始大量拷贝.
				bc.WriteToServer(dataReader);

				// 关闭SqlBulkCopy实例
				bc.Close();
			}

			// 向窗体显示导入的数目
			this.lblProgress.Text = "Imported: " + 
				this.rowCount.ToString() + "/" + 
				this.rowCount.ToString() + " row(s)";
			this.lblProgress.Refresh();

			// 通知用户
			MessageBox.Show("ready");
		}
	}
	catch (Exception e)
	{
		MessageBox.Show(e.Message, "Error - SaveToDatabaseDirectly", 
				MessageBoxButtons.OK, MessageBoxIcon.Error);
	}
}</pre>

基于结构表创建表

<pre class="lang:c# decode:true " >/*
 * 使用结构表生成创建表的命令, 并且在数据库执行
 */

private bool CreateTableInDatabase(DataTable dtSchemaTable, string tableOwner, 
		string tableName, string connectionString)
{
	try
	{
		// 生成创建表的命令
		// 结构表的第一行包含列名.
		// 所有列的数据类型是 nvarcher(4000) 

		string ctStr = "CREATE TABLE [" + tableOwner + "].[" + 
						tableName + "](\r\n";
		for (int i = 0; i < dtSchemaTable.Rows.Count; i++)
		{
			ctStr += "  [" + dtSchemaTable.Rows[i][0].ToString() + "]
					[nvarchar](4000) NULL";
			if (i < dtSchemaTable.Rows.Count)
			{
				ctStr += ",";
			}
			ctStr += "\r\n";
		}
		ctStr += ")";

		// 你可以检查Sql语句
		//MessageBox.Show(ctStr);

		// 执行SQL语句生成表.		
		SqlConnection conn = new SqlConnection(connectionString);
		SqlCommand command = conn.CreateCommand();
		command.CommandText = ctStr;
		conn.Open();
		command.ExecuteNonQuery();
		conn.Close();

		return true;
	}
	catch (Exception e)
	{
		MessageBox.Show(e.Message, "CreateTableInDatabase", 
			MessageBoxButtons.OK, MessageBoxIcon.Error);
		return false;
	}
}</pre>
获取数据库中用户表名
<pre class="lang:c# decode:true " >/*
 * 加载数据库里的用户表，并且用表名填充listbox控件
 */

private void loadTables()
{
         // 连接到数据库，选择表名.
         SqlConnection cn = new SqlConnection(prop.sqlConnString);
	SqlDataAdapter da = new SqlDataAdapter
			("select name from dbo.sysobjects where xtype = 'U' 
		and name <> 'dtproperties' order by name", cn);
	DataTable dt = new DataTable();

         // 填充列表到 DataTable.
	da.Fill(dt);

         // 清空ListBox
	this.lbxTables.Items.Clear();

         // 填充表名到 ListBox.
         //如果数据库没有用户名则通知用户
	if (dt.Rows.Count == 0)
	{
	MessageBox.Show("There is no user table in the specified database. 
		Import a CSV file first.", "Warning", 
		MessageBoxButtons.OK, MessageBoxIcon.Warning);
	this.lbxTables.Items.Add("< no user table in database >");
	this.btnExportToCSV.Enabled = false;
	}
	else
	{
	this.btnExportToCSV.Enabled = true;

	for (int i = 0; i < dt.Rows.Count; i++)
	{
	    this.lbxTables.Items.Add(dt.Rows[i][0].ToString());
	}
	this.lbxTables.SelectedIndex = 0;
	}
}</pre>

通过StreamWriter写数据
<pre class="lang:c# decode:true " >/*
 * 导出数据到CSV文件
 */

private void exportToCSVfile(string fileOut)
{
    // 连接到数据库，生成选择命令.
    SqlConnection conn = new SqlConnection(prop.sqlConnString);
    string sqlQuery = "select * from " + this.lbxTables.SelectedItem.ToString();
    SqlCommand command = new SqlCommand(sqlQuery, conn);
    conn.Open();

    // 创建SqlDataReader实例从表中读取数据
    SqlDataReader dr = command.ExecuteReader();

    // 检索表的结构
    DataTable dtSchema = dr.GetSchemaTable();

// 用流创建CSV文件，使用指定的编码
  StreamWriter sw = new StreamWriter(fileOut, false, this.encodingCSV);

    string strRow; // 表示一行

    // 如果用户要求，写入表头.
    if (this.chkFirstRowColumnNames.Checked)
    {
	sw.WriteLine(columnNames(dtSchema, this.separator));
    }

    // 从SqlDataReader 一行行读
    // 通过给定的分隔符转换成字符串然后写到文件里
    while (dr.Read())
    {
	strRow = "";
	for (int i = 0; i < dr.FieldCount; i++)
	{
	    strRow += dr.GetString(i);
	    if (i < dr.FieldCount - 1)
	    {
		strRow += this.separator;
	    }
	}
	sw.WriteLine(strRow);
    }

    // 关闭文本流和数据库连接
    sw.Close();
    conn.Close();

    // 通知用户
    MessageBox.Show("ready");
}</pre>

Demo下载
[CSV_import_export](http://pan.baidu.com/share/link?shareid=68408&uk=1493685990)
许可
本文包括源代码和文件在CPOL下授权

原文地址：[C-CSV-Import-Export](http://www.codeproject.com/Articles/30705/C-CSV-Import-Export)