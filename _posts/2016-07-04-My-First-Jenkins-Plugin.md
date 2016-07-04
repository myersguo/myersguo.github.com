---
layout: wp
title: My First Jenkins Plugin
---  

上次谈到了jenkins的插件的编写相关的知识，在这两天的编写过程中，真实“磕磕绊绊“，由于对jenkin插件的写法和JAVA相关技巧、语法的不熟悉，走了很多的弯路，最后能把插件拼凑着完成，实属幸运。  

1. jenkins写文件的方法：  
我的需求中，要把从接口读到的文件写入当前工作目录中的某个文件下。因此我需要知道jenkins如何写文件。如果我直接写的话：  


```
    PrintWriter writer = new PrintWriter("d:\\test.json", "UTF-8");
    writer.println(fileContent);
    writer.close();

````

但我需要部署到当前目录下面：  
Building in workspace  
即
./config/xxx.php   
因此写成：  

```
private void writeScmFile(AbstractBuild<?, ?> build,PrintStream logger, String filepath,
                              String filename, String content) throws IOException,InterruptedException{
        String relativePath = '.' + filepath + "/" + filename;
        FilePath f = build.getWorkspace().child(relativePath);
        f.write(content, "UTF-8");
    }
```  

2. jenkins的全局配置和局部配置  

插件的描述只需要实现DescriptorImpl,比如我的：  

```
public class MyJenkinsPlugin extends Builder {

    public int projectId;
    public String site;
    ....
    @Override
    public boolean perform(AbstractBuild<?,?> build, Launcher launcher, BuildListener listener)
            throws InterruptedException,IOException {
        final PrintStream logger = listener.getLogger();
        String token = getDescriptor().getToken();
        ...
    }
    ....

 @Extension
    public static final class DescriptorImpl extends BuildStepDescriptor<Builder> {
        private String baseUrl;
        private String token;

        public DescriptorImpl(){
            load();
        }
        @Override
        public boolean isApplicable(Class<? extends AbstractProject> aClass){
            return true;
        }
        @Override
        public String getDisplayName(){
            return "My Jenkins Plugin";
        }

        //检查projectId
        public FormValidation doCheckProjectId(@QueryParameter String value)
                throws IOException, ServletException {
            if(value.length() == 0 )
            return FormValidation.ok();
        }

        public FormValidation doCheckSite(@QueryParameter String value)
                throws IOException, ServletException {
            if(value.length() == 0 )
            return FormValidation.ok();
        }
       
        @Override
        public boolean configure(StaplerRequest req, net.sf.json.JSONObject formData) throws FormException {
            token = formData.getString("token");
            baseUrl = formData.getString("baseUrl");
            save();
            return super.configure(req, formData);
        } 

```

全局配置用global.jelly来描述，比如我的：  

```
<?jelly escape-by-default='true'?>
<j:jelly xmlns:j="jelly:core" xmlns:st="jelly:stapler" xmlns:d="jelly:define"
    xmlns:l="/lib/layout" xmlns:t="/lib/hudson" xmlns:f="/lib/form">
  <f:section title="...">

    <f:entry title="token" field="token"
      description="oken setting">
       <f:textbox />
    </f:entry>

    <f:entry title="Domain" field="baseUrl">
       <f:textbox />
    </f:entry>

  </f:section>
</j:jelly>

```

插件的build配置在config.jelly中。每个字段对应的帮助文件在 help-字段名.html中，当然可以指定帮助文件。  

3. mvn clean install ,一切就绪，在jenkins高级中上传插件，然后enjoy plugins吧。   

### 总结   
第一次尝试写一个jenkins插件，总结两点：  
1） 从jenkins官方教程读起，这里是源头。  
2） 找一个相似的jenkins插件进行改造，所谓 “照猫画虎“，先把东西搞出来，run first，当插件真正跑起来的时候，你会发现，你已经趟过不少的坑了，就会体会到jenkin插件开发的魅力所在。  



