title: Spring揭秘-第13章 统一的数据访问异常层次体系
tags:
  - java
  - spring
id: 3700
categories:
  - 学习笔记
date: 2014-07-13 11:25:13
---

Spring揭秘-第13章 统一的数据访问异常层次体系
<div></div>
<div>DAO可以分离数据哭的访问和存储，屏蔽各种数据访问方式的差异性，下面以访问顾客信息为例，使用DAO模式</div>
<div>首先定义一个数据访问对象接口，如下</div>
<div></div>
<div>
<div class="kwd">
<pre class="lang:default decode:true ">    public interrface ICustomerDao{
    Customer findCustomerByPK(String customerId);
    void updateCustomerStatus(Customer customer);</pre>
&nbsp;

</div>
<div class="kwd">之后，所有的数据访问都通过该接口进行，不论底层存储机制如何改变，DAO的实现因此会扩展，但客户端代码不需要调整</div>
</div>
<div></div>
<div>客户端要用的时候这样使用即可。</div>
<div>
<div>
<pre class="lang:default decode:true ">    publicclassCustomerService
    {
    privateICustomerDao customerDao;
    publicvoid disableCustomerCampain(String customerId)
    {
    Customer customer=getCustomerDao().findCustomerByPK(customerId);
    customer.setCampainStatus(CampainStatus.DISABLE);
    getCustomerDao().updateCustomerStatus(customer);
    }
    publicICustomerDao getCustomerDao()
    {
    return customerDao;
    }
    publicvoid setCustomerDao(ICustomerDao customerDao)
    {
    this.customerDao=customerDao;
    }</pre>
&nbsp;

</div>
</div>
<div>我们只要针对不同的数据存储方式实现不同的Dao类即可。</div>
<div></div>
<div>我们开始实现具体的访问数据了。</div>
<div>
<div>
<pre class="lang:default decode:true ">    publicCustomer findCustomerByPK(String customerId)
    {
    Connection con=null;
    try{
    con=getDataSource().getConnection();
    Customer cust=..;
    return cust;
    }
    catch(SQLException e){
    //这里咋办，直接抛出还是直接处理？
    }
    finally{
    releaseConnection(con);
    }
    }
    privatevoid releaseConnection(Connection con){
    }</pre>
&nbsp;

</div>
</div>
<div><span class="typ" style="font-family: Consolas, 'Liberation Mono', Menlo, Courier, monospace; line-height: 18px; white-space: pre; background-color: #f7f7f9;">SQLException</span><span class="pln" style="font-family: Consolas, 'Liberation Mono', Menlo, Courier, monospace; line-height: 18px; white-space: pre; background-color: #f7f7f9;"> 是checked exception，如果在DAO的实现类里直接处理掉，那么客户端怎么知道发生了问题，</span></div>
<div><span class="pln" style="font-family: Consolas, 'Liberation Mono', Menlo, Courier, monospace; line-height: 18px; white-space: pre; background-color: #f7f7f9;"> </span></div>
<div><span class="pln" style="font-family: Consolas, 'Liberation Mono', Menlo, Courier, monospace; line-height: 18px; white-space: pre; background-color: #f7f7f9;">于是，只能处理，那么客户端的签名就要改了</span></div>
<div>
<div>

1.  `<span class="typ">Customer</span><span class="pln"> findCustomerByPK</span><span class="pun">(</span><span class="typ">String</span><span class="pln"> customerId</span><span class="pun">)</span><span class="kwd">throws</span><span class="typ">SQLExcepiton</span><span class="pun">;</span>`
</div>
<div>ICustomerDao也要改了，可是这样的话</div>
<div></div>
<div>如果数据在其他方式存储，那么其他类型的异常捕获就要继续添加到方法签名。由于数据访问机制的不同，导致这个接口签名不断在变化。</div>
<div></div>
<div>那怎么办呢？DAO如此美好的远景。</div>
<div>直接抛出，的话，因此，可以根据发生的异常信息封装成不同的unchecked exception，然后抛出，这样方法签名不用改。因为unchecked不需要编译器检查。</div>
<div></div>
<div>要开始写了？Spring的数据访问异常层次体系已经给我们做完了</div>
<div></div>
<div>该体系下面所有的异常类型均以DataAccessException为统领，然后划分成不同的子类型</div>
<div></div>
<div>![](/images/3ab324530a8c3d0b0216d3f7549ebbfa6b7869f4.jpg)</div>
<div>这些类是啥搜索一下，或者根据名字猜一下，之后我们就可以</div>
</div>