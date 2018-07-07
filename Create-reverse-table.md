---
title: Create reverse table
date: 2017-05-01 15:44:06
categories: 编程
tags: [建立倒排表,java,Lucene,信息检索]

---

> http://a280606790.iteye.com/blog/799347
>
> http://www.cnblogs.com/sephy/p/3303711.html

## Lucene包结构功能表

| 包名                            | 功能                         |
| ----------------------------- | -------------------------- |
| org.apache.lucene.analysis    | 语言分析器，主要用于的切词，支持中文主要是扩展此类  |
| org.apache.lucene.document    | 索引存储时的文档结构管理，类似于关系型数据库的表结构 |
| org.apache.lucene.index       | 索引管理，建立、删除等                |
| org.apache.lucene.queryParser | 查询分析器，查询关键词间的运算，与、或、非等     |
| org.apache.lucene.search      | 检索管理，根据查询条件，检索得到结果         |
| org.apache.lucene.util        | 公用类                        |



* Analyzer是分析器，将字符串划分成一个个词语，去除其中的无效词语。英文的of,the，中文'的'，'地'等。

* document用户提供的源，可以是文本文件、字符串或者数据库表的一条记录。一条记录经过索引后，就是以一个Document形式存储在索引文件中。用户进行搜索以Document列表形式返回

* Field: 一个document可以包含多个信息域，标题、正文、最后修改时间，这些信息域通过Field在Document中存储。Field有两个属性可选：存储和索引。通过存储属性可以控制是否对该Field进行存储。

* Term:搜索的最小单位，表示文档的一个词语。term由词语和这个词语出现的Field组成。

* Tocken: term中的一次出现，包含term文本和相应的起止偏移，以及一个类型字符串。一句话可以出现多次相同的词语，它们用同一个term表示，但是用不同的tocken，每个tocken标记该词语出现的地方。

* Segment: 添加索引时并不是每个document都马上添加到同一个索引文件，首先被写入不同的小文件，然后再合并成一个大索引文件，每一个小文件都是一个segment。

  ​

## Lucene查询逻辑

### 影响Lucene对文档评分权重的方式

如果希望某些文档和某些域比其他的域更重要，如果此文档和此域包含所要查询的词则应该得分较高，则可以在索引阶段设定文档的boost和域的boost值。默认值1.0，增加权重需要比1大才可以

Lucene文档的打分公式：
$$
score(q,d)=coord(q,d) \cdot queryNorm(q)\cdot \sum (tf(t\in d))\cdot idf(t)^3 \cdot t.getBoost()\cdot norm(t,d))
$$
通过修改对应域的boost的值来修改对应域的权重。

lucene的搜索相当强大，提供了很多辅助查询类，每个类都继承自Query类，各自完成特定的一种查询，可以组合使用完成特定的复杂操作；Lucene还提供Sort类对结果进行排序，还有Filter类对查询条件进行限制。各种查询方式可以在官网API中查看。

对于分词器，按照句子语义切分成一个个词语。对英文进行切词已经有完善的分词器StandardAnalyzer，很多情况下都可以使用。但是对中文分词并不友好，它会将中文词语单切，对每一个字符都分词。例如搜索"如果"，也会将"牛奶不如果汁好喝"搜索出来，并且因为对每一个字都分词，会造成词项很多，文件很大。所以选择使用Paoding分词，按词典词义进行分词。

执行查询的逻辑为：在已创建的索引目录中，创建一个搜索器。使用分词器对查询语句分词，设置搜索的域，利用生成的query去搜素，以Hits的形式返回。

查询例示：

```java
   public static void searchIndex(String text){
        TokenStream stream = analyzer.tokenStream("content",new StringReader(text));
        Date date1=new Date();
        try {
            if(showParticiple)
                printTokens(" ",stream);
            System.out.println("搜索 '"+text+"' 结果如下");
            //1.打开存储位置
                //庖丁分词
            analyzer = new PaodingAnalyzer();
            IndexReader ireader = IndexReader.open(INDEX_DIR);
            //2.创建搜索器
            IndexSearcher isearcher = new IndexSearcher(ireader);
            //3.进行关键词查询
            QueryParser parser = new QueryParser("content",analyzer);
          	//进行多域查询，支持查询内容，时间，日期
            String fields[] = {"content","filename","editor","date"};
            String texts[] ={text,text,text,text};
            Query query = MultiFieldQueryParser.parse(texts,fields,analyzer);
            ScoreDoc[] hits=isearcher.search(query,null,300).scoreDocs;
            if(hits.length==0)
                System.out.println("没有相似文件");
            else {
                System.out.println("找到"+hits.length+"个文件");
            }
            for(int i=0;i<hits.length;i++){
                Document hitDoc = isearcher.doc(hits[i].doc);
                System.out.println("_______________________");
                System.out.println(hitDoc.get("filename"));
                System.out.println("时间: "+hitDoc.get("date"));
                System.out.println("URL: "+hitDoc.get("url"));
                System.out.println("Content:"+hitDoc.get("content"));
                System.out.println("编辑: "+hitDoc.get("editor"));
                System.out.println("Path:"+hitDoc.get("path"));
                System.out.println("_______________________");
            }
            //4.关闭查询器等
            ireader.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
        Date date2 = new Date();
        System.out.println("查看索引-----耗时："+(date2.getTime()-date1.getTime()+"ms\n"));
    }
```





## Lucene入库逻辑

1. 入库这定义到库中文档的结构，如需要把网站内容加载到全文检索库，让用户通过“站内检索“搜索到相关的网页的内容。入库文档结构和关系型数据库中的表结构类似，每个入库的文档由多个字段构成（文章标题、作者、发布时间、原文链接、正文内容）。

2. 包含N个字段的文档（Dcument）在入库前需要经过切词索引，切词规则由语言分析器（analyzer）完成。

3. 切分后的”单词“被注册到索引树上，供查询使用。另需要将其他不需要索引的内容入库，所有这些文件操作由storage完成。

4. 代码示例

   ```Java
   public static boolean createIndex(String path)
       {
           boolean first=true;
           //记录开始创建索引的时间
           Date date1=new Date();
           //获取路径下的所有文件集合
           List<File> fileList=getFileList(path);
           try {
               for(File f:fileList){
                   //从"."开始取子字符串 取文件后缀
                   String type =f.getName().substring(f.getName().lastIndexOf(".")+1);
                   //1.读取txt文件的内容
                   if("txt".equalsIgnoreCase(type)) {
                       //取得文件所有内容
                       con = txt2String(f);
                   }
        
                   //2.向本地文件存储索引
                   File indexFile = new File(INDEX_DIR);
                 	//如果索引不存在则创建
                   if(!indexFile.exists()){
                       indexFile.mkdirs();
                   }
                   indexWriter = new IndexWriter(INDEX_DIR,analyzer,first);
                   first=false;
           
                   //3.提取内容，进行索引的存储
                   Document document=new Document();
                   Field filename = new Field("filename",con.get(0), Field.Store.YES,Field.Index.TOKENIZED);
                   filename.setBoost(100);
                   Field editor = new Field("editor",con.get(2), Field.Store.YES,Field.Index.TOKENIZED);
                 	//设置内容权重
                   editor.setBoost(80);
                   document.add(filename);
                   Field content = new Field("content",con.get(con.size()-1), Field.Store.YES,Field.Index.TOKENIZED);
                   content.setBoost(50);
                   document.add(new Field("date",con.get(3), Field.Store.YES,Field.Index.UN_TOKENIZED));
                   document.add(editor);
                   document.add(new Field("url",con.get(1),Field.Store.YES,Field.Index.NO));
                   document.add(content);
                   document.add(new Field("path",f.getPath(), Field.Store.YES,Field.Index.NO));
                   //4.索引对象加入索引创建中
                   indexWriter.addDocument(document);
                   //提交索引并关闭
                   closeWriter();
               }
           } catch (Exception e) {
               e.printStackTrace();
           }
           Date date2=new Date();
           System.out.println("创建索引成功\t耗时： "+(date2.getTime()-date1.getTime())+"ms\n");
           return true;
       }
   ```

   ​
