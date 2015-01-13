title: "[译]使用Mockito简单mock入门"
tags:
  - java
  - 工作
  - 翻译
  - 设计
id: 3505
categories:
  - 我的翻译
date: 2014-03-01 10:57:33
---

我们在写单元测试的时候，面临的一个挑战就是要测试的内容总是依赖于其他组件，要是我们还得先配置好其他组件，未免有点不如意，那么我们可以使用Mocks来代替那些依赖的组件

本文问了展示这个过程，我会创建一个DAL，数据访问层，这是一个类，提供了一个通用的api来访问和修改数据仓库的数据，然后，我们要测试这个api，而不用配置连接某个本地的数据库，，或者一个远程的数据库，或者是一个文件系统，反正就是任何放数据的东西，DAL层的好处就是隔离开了数据访问和应用程序代码

首先使用maven来创建一个工程
<pre class="lang:default decode:true">mvn archetype:generate -DgroupId=info.sanaulla -DartifactId=MockitoDemo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false</pre>
执行之后，本地生成MockitoDemo 文件夹，然后整个工程的目录结构与生成好了。

然后，我们写这样一个model类，表示book类型
<pre class="lang:java decode:true">package info.sanaulla.models;

import java.util.List;

/**
* Model class for the book details.
*/
public class Book {

  private String isbn;
  private String title;
  private List&lt;String&gt; authors;
  private String publication;
  private Integer yearOfPublication;
  private Integer numberOfPages;
  private String image;

  public Book(String isbn,
              String title,
              List&lt;String&gt; authors,
              String publication,
              Integer yearOfPublication,
              Integer numberOfPages,
              String image){

    this.isbn = isbn;
    this.title = title;
    this.authors = authors;
    this.publication = publication;
    this.yearOfPublication = yearOfPublication;
    this.numberOfPages = numberOfPages;
    this.image = image;

  }

  public String getIsbn() {
    return isbn;
  }

  public String getTitle() {
    return title;
  }

  public List&lt;String&gt; getAuthors() {
    return authors;
  }

  public String getPublication() {
    return publication;
  }

  public Integer getYearOfPublication() {
    return yearOfPublication;
  }

  public Integer getNumberOfPages() {
    return numberOfPages;
  }

  public String getImage() {
    return image;
  }
}</pre>
然后，我们访问Book model的DAL类会如下
<pre class="lang:java decode:true">package info.sanaulla.dal;

import info.sanaulla.models.Book;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

/**
* API layer for persisting and retrieving the Book objects.
*/
public class BookDAL {

  private static BookDAL bookDAL = new BookDAL();

  public List&lt;Book&gt; getAllBooks(){
      return Collections.EMPTY_LIST;
  }

  public Book getBook(String isbn){
      return null;
  }

  public String addBook(Book book){
      return book.getIsbn();
  }

  public String updateBook(Book book){
      return book.getIsbn();
  }

  public static BookDAL getInstance(){
      return bookDAL;
  }
}</pre>
DAL层现在还没啥功能，我们要通过TDD来测试，实际中，DAL可能和ORM来交互，也可能和数据库API交互，但是我们设计DAL的时候，不用关心

准备测试

我倾向于用junit做单元测试，而Mockito做mock。首先更新一下pom文件。添加依赖
<pre class="lang:default decode:true">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd"&gt;
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
  &lt;groupId&gt;info.sanaulla&lt;/groupId&gt;
  &lt;artifactId&gt;MockitoDemo&lt;/artifactId&gt;
  &lt;packaging&gt;jar&lt;/packaging&gt;
  &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
  &lt;name&gt;MockitoDemo&lt;/name&gt;
  &lt;url&gt;http://maven.apache.org&lt;/url&gt;
  &lt;dependencies&gt;
    &lt;!-- Dependency for JUnit --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;junit&lt;/groupId&gt;
      &lt;artifactId&gt;junit&lt;/artifactId&gt;
      &lt;version&gt;4.10&lt;/version&gt;
      &lt;scope&gt;test&lt;/scope&gt;
    &lt;/dependency&gt;
    &lt;!-- Dependency for Mockito --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.mockito&lt;/groupId&gt;
      &lt;artifactId&gt;mockito-all&lt;/artifactId&gt;
      &lt;version&gt;1.9.5&lt;/version&gt;
      &lt;scope&gt;test&lt;/scope&gt;
    &lt;/dependency&gt;
  &lt;/dependencies&gt;
&lt;/project&gt;</pre>
下面，我们要测试啦，我们会在单元测试中把mock数据注入到BookDAL中，这样我们就不要依赖任何数据源就可以完成测试
<pre class="lang:default decode:true">public class BookDALTest {
  public void setUp() throws Exception {

  }

  public void testGetAllBooks() throws Exception {

  }

  public void testGetBook() throws Exception {

  }

  public void testAddBook() throws Exception {

  }

  public void testUpdateBook() throws Exception {

  }
}</pre>
我们当然对应测试DAL类的每个方法嘛。

然后我们会在setup（这个方法是在测试类执行测试前执行的，用于准备数据啦）
<pre class="lang:default decode:true">public class BookDALTest {

  private static BookDAL mockedBookDAL;
  private static Book book1;
  private static Book book2;

  @BeforeClass
  public static void setUp(){
    //创建BookDAL类的一个mock对象
    mockedBookDAL = mock(BookDAL.class);

    //创建Book类.
    book1 = new Book("8131721019","Compilers Principles",
            Arrays.asList("D. Jeffrey Ulman","Ravi Sethi", "Alfred V. Aho", "Monica S. Lam"),
            "Pearson Education Singapore Pte Ltd", 2008,1009,"BOOK_IMAGE");

    book2 = new Book("9788183331630","Let Us C 13th Edition",
            Arrays.asList("Yashavant Kanetkar"),"BPB PUBLICATIONS", 2012,675,"BOOK_IMAGE");

    //用mock数据来stub对应的方法
    when(mockedBookDAL.getAllBooks()).thenReturn(Arrays.asList(book1, book2));
    when(mockedBookDAL.getBook("8131721019")).thenReturn(book1);
    when(mockedBookDAL.addBook(book1)).thenReturn(book1.getIsbn());
    when(mockedBookDAL.updateBook(book1)).thenReturn(book1.getIsbn());
  }

  public void testGetAllBooks() throws Exception {}

  public void testGetBook() throws Exception {}

  public void testAddBook() throws Exception {}

  public void testUpdateBook() throws Exception {}
}</pre>
在setup()方法中

1.首先我创建BookDAL的对象
<pre class="lang:default decode:true">BookDAL mockedBookDAL = mock(BookDAL.class);</pre>
2.然后stub,存根。。用mock的数据。这样当对应的方法被调用的时候就返回我mock的数据
<pre class="lang:default decode:true">//当被getAllBooks调用的时候就返回我book1，和book2
when(mockedBookDAL.getAllBooks()).thenReturn(Arrays.asList(book1, book2)); 
when(mockedBookDAL.getBook("8131721019")).thenReturn(book1); 
when(mockedBookDAL.addBook(book1)).thenReturn(book1.getIsbn()); 
when(mockedBookDAL.updateBook(book1)).thenReturn(book1.getIsbn());</pre>
然后，我们执行一下

mvn test就可以看到结果了
<pre class="lang:default decode:true ">-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running info.sanaulla.AppTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.029 sec
Running info.sanaulla.dal.BookDALTest
Tests run: 4, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.209 sec

Results :

Tests run: 5, Failures: 0, Errors: 0, Skipped: 0</pre>
&nbsp;

&nbsp;

&nbsp;
<pre class="lang:default decode:true">package info.sanaulla.dal;

import info.sanaulla.models.Book;
import org.junit.BeforeClass;
import org.junit.Test;

import static org.junit.Assert.*;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.when;

import java.util.Arrays;
import java.util.List;

public class BookDALTest {

  private static BookDAL mockedBookDAL;
  private static Book book1;
  private static Book book2;

  @BeforeClass
  public static void setUp(){
    mockedBookDAL = mock(BookDAL.class);
    book1 = new Book("8131721019","Compilers Principles",
            Arrays.asList("D. Jeffrey Ulman","Ravi Sethi", "Alfred V. Aho", "Monica S. Lam"),
            "Pearson Education Singapore Pte Ltd", 2008,1009,"BOOK_IMAGE");

    book2 = new Book("9788183331630","Let Us C 13th Edition",
            Arrays.asList("Yashavant Kanetkar"),"BPB PUBLICATIONS", 2012,675,"BOOK_IMAGE");

    when(mockedBookDAL.getAllBooks()).thenReturn(Arrays.asList(book1, book2));
    when(mockedBookDAL.getBook("8131721019")).thenReturn(book1);
    when(mockedBookDAL.addBook(book1)).thenReturn(book1.getIsbn());

    when(mockedBookDAL.updateBook(book1)).thenReturn(book1.getIsbn());

  }

  @Test
  public void testGetAllBooks() throws Exception {

    List&lt;Book&gt; allBooks = mockedBookDAL.getAllBooks();
    assertEquals(2, allBooks.size());
    Book myBook = allBooks.get(0);
    assertEquals("8131721019", myBook.getIsbn());
    assertEquals("Compilers Principles", myBook.getTitle());
    assertEquals(4, myBook.getAuthors().size());
    assertEquals((Integer)2008, myBook.getYearOfPublication());
    assertEquals((Integer) 1009, myBook.getNumberOfPages());
    assertEquals("Pearson Education Singapore Pte Ltd", myBook.getPublication());
    assertEquals("BOOK_IMAGE", myBook.getImage());
  }

  @Test
  public void testGetBook(){

    String isbn = "8131721019";

    Book myBook = mockedBookDAL.getBook(isbn);

    assertNotNull(myBook);
    assertEquals(isbn, myBook.getIsbn());
    assertEquals("Compilers Principles", myBook.getTitle());
    assertEquals(4, myBook.getAuthors().size());
    assertEquals("Pearson Education Singapore Pte Ltd", myBook.getPublication());
    assertEquals((Integer)2008, myBook.getYearOfPublication());
    assertEquals((Integer)1009, myBook.getNumberOfPages());

  }

  @Test
  public void testAddBook(){
    String isbn = mockedBookDAL.addBook(book1);
    assertNotNull(isbn);
    assertEquals(book1.getIsbn(), isbn);
  }

  @Test
  public void testUpdateBook(){

    String isbn = mockedBookDAL.updateBook(book1);
    assertNotNull(isbn);
    assertEquals(book1.getIsbn(), isbn);
  }
}</pre>
很简单。。。没有配置任何实际的数据源。