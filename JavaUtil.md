---
title: JavaUtil
date: 2017-08-24 09:04:16
categories : 编程
tags : [Java util,]
---

# Directory

## instruction

程序设计中一项常见的任务就是在文件集上执行操作，这些文件要么在本地目录中，要么遍布于整个目录树中。如果有一种工具能产生整个文件集，将会非常节省时间。下面的实用工具类就可以通过使用 $local()$ 方法由本地目录的文件构成的 File 对象组，或者通过 $walk()$ 方法产生给定目录下的由整个目录树中所有文件构成的 List\<File> ( File 对象比文件更有用，因为 File 包含更多的信息)。这些文件是基于你提供的正则表达式而被选中的

## Code

```Java
package categoryUtil;

import java.io.File;
import java.io.FilenameFilter;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.regex.Pattern;

/**
 * Created by isaac on 2017/8/24.
 */
public class Directory {
    public static File[] local(File dir,final String regex){
        return dir.listFiles(new FilenameFilter() {
            private Pattern pattern = Pattern.compile(regex);
            @Override
            public boolean accept(File dir, String name) {
                return pattern.matcher(new File(name).getName()).matches();
            }
        });
    }

    public static File[] local(String path,final String regex){
        //Override
        return local(new File(path),regex);
    }

    //A two-tuple for returning a pair of objects:
    public static class TreeInfo implements Iterable<File>{
        public List<File> files = new ArrayList<File>();
        public List<File> dirs = new ArrayList<File>();

        //The default iterable elements is the file list:
        public Iterator<File> iterator(){
            return files.iterator();
        }

        protected void addAll(TreeInfo other){
            files.addAll(other.files);
            dirs.addAll(other.dirs);
        }

        public String toString(){
            return "dirs : " + PPrint.pformat(dirs) +
                    "\n\n files : " + PPrint.pformat(files);
        }
    }

    public static TreeInfo walk(String start,String regex){
        //Begin recursion
        return recurseDirs(new File(start),regex);
    }

    public static TreeInfo walk(File start,String regex){
        return recurseDirs(start,regex);
    }

    public static TreeInfo walk(File start){
        //Everything
        return recurseDirs(start,".*");
    }

    public static TreeInfo walk(String start){
        return recurseDirs(new File(start),".*");
    }

    static TreeInfo recurseDirs(File startDir,String regex){
        TreeInfo result = new TreeInfo();
        for(File item : startDir.listFiles()){
            if(item.isDirectory()){
                result.dirs.add(item);
                result.addAll(recurseDirs(item,regex));
            }
            else if(item.getName().matches(regex)){
                result.files.add(item);
            }
        }
        return result;
    }
}

```

## Testify

```java
public static void main(String[] args){
  if(args.length==0)
    System.out.println(walk("."));
  else
    for(String arg : agrs)
      System.out.println(walk(arg));
}
```

# Pretty Print

## Instrcution

容器默认的toString方法会在单个行中打印容器中的所有元素，对于大型集合来说，这会变得难以阅读，本工具可替换其格式。添加新行并缩排所有的元素的工具

## Code

```java
package categoryUtil;

import java.util.*;

/**
 * Created by isaac on 2017/8/24.
 */
public class PPrint {
    public static String pformat(Collection<?> c){
        if(c.size() == 0) return "[]";
        StringBuffer result = new StringBuffer(" [ ");
        for(Object elem : c){
            if(c.size() != 1)
                result.append("\n  ");
            result.append(elem);
        }

        if(c.size()==1)
            result.append("\n");
        result.append(" ] ");
        return result.toString();
    }

    public static void pprint(Collection<?> c){
        System.out.println(pformat(c));
    }

    public static void pprint(Object[] c){
        System.out.println(Arrays.asList(c));
    }
}

```

