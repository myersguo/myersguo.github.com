---
layout: wp
title: jenkins plugin 开发之JAVA THINKING  
--- 

JENKINS的插件开发是使用JAVA写的，在写JAVA代码的发现诸多不顺，比如：  

1. 我要发起HTTP请求。  
导个包，用apache http client .可以需要使用HTTPS请求，又导入个包自定义协议。  
2. 比如我要获取JSON格式的数据。  
导个包，读数据，到JSONArray里，然后遍历Array中每个item。  
3. 我要对读取的字符做base64 decode。  
导个包，to byte, to String.  

还没有完全搞定 JENKINS PLUGIN，已经导入的包有：  

```

import hudson.Extension;
import hudson.Launcher;
import hudson.model.*;
import hudson.tasks.BuildStepDescriptor;
import hudson.tasks.Builder;
import hudson.util.FormValidation;
import org.apache.http.conn.scheme.Scheme;
import org.apache.http.conn.scheme.SchemeRegistry;
import org.apache.http.conn.ssl.SSLSocketFactory;
import org.apache.http.conn.ssl.TrustStrategy;
import org.kohsuke.stapler.DataBoundConstructor;
import org.kohsuke.stapler.DataBoundSetter;
import org.kohsuke.stapler.QueryParameter;
import org.kohsuke.stapler.StaplerRequest;

import javax.servlet.ServletException;
import java.io.IOException;
import java.io.PrintStream;
import java.io.PrintWriter;
import java.security.cert.CertificateException;
import java.security.cert.X509Certificate;
import java.util.Objects;
import java.util.logging.Logger;

import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.util.EntityUtils;
import org.apache.http.HttpEntity;
import org.json.simple.JSONArray;
import org.json.simple.JSONObject;
import org.json.simple.parser.JSONParser;
import org.apache.commons.httpclient.protocol.SSLProtocolSocketFactory;
import org.apache.commons.httpclient.protocol.Protocol;  

````  


