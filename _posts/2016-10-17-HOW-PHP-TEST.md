---
title: PHP自动化测试框架
layout: wp
---

### PHP源码是怎么做测试的？ ###   

先举个例子，看PHP是怎么做测试的：  

设置环境变量：  

```php
export TEST_PHP_EXECUTABLE=/home/work/app/php/bin/php 
```

执行测试：  

```php
/home/work/app/php/bin/php  run-tests.php tests/basic/001.phpt 
```

结果：   

```php
=====================================================================
PHP         : /home/work/app/php/bin/php 
PHP_SAPI    : cli
PHP_VERSION : 5.3.10
ZEND_VERSION: 2.3.0
PHP_OS      : Linux - Linux vagrant.localdomain 2.6.32-358.el6.x86_64 #1 SMP Fri Feb 22 00:31:26 UTC 2013 x86_64
INI actual  : /home/work/app/php/etc/php.ini
More .INIs  :  
CWD         : /home/work/data/www/php-src
Extra dirs  : 
VALGRIND    : Not used
=====================================================================
Running selected tests.
PASS Trivial "Hello World" test [tests/basic/001.phpt] 
=====================================================================
Number of tests :    1                 1
Tests skipped   :    0 (  0.0%) --------
Tests warned    :    0 (  0.0%) (  0.0%)
Tests failed    :    0 (  0.0%) (  0.0%)
Expected fail   :    0 (  0.0%) (  0.0%)
Tests passed    :    1 (100.0%) (100.0%)
---------------------------------------------------------------------
Time taken      :   11 seconds
=====================================================================
```

设置JUNIT输出：  

```php
export TEST_PHP_JUNIT="/tmp/php.result"   
```

再次执行上述测试，查看php执行结果：  

```shell
cat /tmp/php.result
```

结果：  


```php
<?xml version="1.0" encoding="UTF-8"?>
<testsuites>
<testsuite name="php-src" tests="1" failures="0" errors="0" skip="0" time="10.3658">
<testsuite name="php-src.tests.basic" tests="1" failures="0" errors="0" skip="0" time="10.3658">
<testcase classname='php-src.tests.basic' name='001.phpt - Trivial &quot;Hello World&quot; test' time='10.3658'>
</testcase>
</testsuite>
</testsuite>
</testsuites>
```

下面，我讲逐步讲解run-test.php源码(php源码版本5.3):     

检查：  

```php
pcre扩展，
PHP_VERSION_ID全局变量(php 5.2.8才有);  
定义__DIR__(php5.3.0后才有); 
设置： timezone   
保存当前目录：CUR_DIR   
```

设置security related env:  


```php
putenv('SSH_CLIENT=deleted');
```

设置：  

```php
ini_set('pcre.backtrack_limit', PHP_INT_MAX);

```

清空缓存：  

```php
while(@ob_end_clean());
```

检查环境变量 TEST_PHP_EXECUTABLE,TEST_PHP_CGI_EXECUTABLE：  

```php
if (getenv('TEST_PHP_EXECUTABLE')) {
    $php = getenv('TEST_PHP_EXECUTABLE');
    if ($php=='auto') {
        $php = $cwd . '/sapi/cli/php';
        putenv("TEST_PHP_EXECUTABLE=$php");
        if (!getenv('TEST_PHP_CGI_EXECUTABLE')) {
            $php_cgi = $cwd . '/sapi/cgi/php-cgi';
            if (file_exists($php_cgi)) {
                putenv("TEST_PHP_CGI_EXECUTABLE=$php_cgi");
            } else {
                $php_cgi = null;
            }
        }
    }
    $environment['TEST_PHP_EXECUTABLE'] = $php;
}

#...测试是否可执行  

if (function_exists('is_executable') && !is_executable($php)) {
        error("invalid PHP executable specified by TEST_PHP_EXECUTABLE  = $php");
}
```

检查LOG的输出详情：  

```php
if (getenv('TEST_PHP_LOG_FORMAT')) {
    $log_format = strtoupper(getenv('TEST_PHP_LOG_FORMAT'));
} else {
    $log_format = 'LEODS';
}
```

junit初始化：
```
junit_init();
```

write_information: 输出PHP当前信息,eg:  


```php
=====================================================================
PHP         : /home/work/app/php/bin/php 
PHP_SAPI    : cli
PHP_VERSION : 5.3.10
ZEND_VERSION: 2.3.0
PHP_OS      : Linux - Linux vagrant.localdomain 2.6.32-358.el6.x86_64 #1 SMP Fri Feb 22 00:31:26 UTC 2013 x86_64
INI actual  : /home/work/app/php/etc/php.ini
More .INIs  :  
CWD         : /home/work/data/www/php-src
Extra dirs  : 
VALGRIND    : Not used
=====================================================================
```

save_or_mail_results: 如果有错误收集错误信息，并发送给qa邮件。前提是，指定-s参数，表示存储结果的文件。它的结果大致如：  

```
=====================================================================
FAILED TEST SUMMARY
---------------------------------------------------------------------
Trivial "Hello World" test [tests/basic/001.phpt]
=====================================================================


=====================================================================
TEST RESULT SUMMARY
---------------------------------------------------------------------
Exts skipped    :    0    
Exts tested     :    0    
---------------------------------------------------------------------

Number of tests :    1                 1    
Tests skipped   :    0 (  0.0%) --------
Tests warned    :    0 (  0.0%) (  0.0%)
Tests failed    :    1 (100.0%) (100.0%)
Expected fail   :    0 (  0.0%) (  0.0%)
Tests passed    :    0 (  0.0%) (  0.0%)
---------------------------------------------------------------------
Time taken      :   11 seconds
=====================================================================

=====================================================================
FAILED TEST SUMMARY
---------------------------------------------------------------------
Trivial "Hello World" test [tests/basic/001.phpt]
=====================================================================


================================================================================
/home/work/data/www/php-src/tests/basic/001.phpt
================================================================================
Hello World
================================================================================
001+ Hello World^M
001- hHello World
================================================================================
================================================================================
BUILD ENVIRONMENT
================================================================================
OS:
Linux - Linux vagrant.localdomain 2.6.32-358.el6.x86_64 #1 SMP Fri Feb 22 00:31:26 UTC 2013 x86_64

Autoconf:
autoconf (GNU Autoconf) 2.63
Copyright (C) 2008 Free Software Foundation, Inc.
License GPLv2+: GNU GPL version 2 or later
<http://gnu.org/licenses/old-licenses/gpl-2.0.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Written by David J. MacKenzie and Akim Demaille.

Bundled Libtool:
ltmain.sh (GNU libtool) 2.2.6b
Written by Gordon Matzigkeit <gord@gnu.ai.mit.edu>, 1996

Copyright (C) 2008 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

System Libtool:
ltmain.sh (GNU libtool) 2.2.6b
Written by Gordon Matzigkeit <gord@gnu.ai.mit.edu>, 1996

Copyright (C) 2008 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
...
```

下面是定义一些全局变量： 

```
$test_files = array();
$redir_tests = array();
$test_results = array();
$PHP_FAILED_TESTS = array('BORKED' => array(), 'FAILED' => array(), 'WARNED' => array(), 'LEAKED' => array(), 'XFAILED' => array());
// If parameters given assume they represent selected tests to run.
$failed_tests_file= false;
$pass_option_n = false;
$pass_options = '';
$compression = 0;
$output_file = $CUR_DIR . '/php_test_results_' . date('Ymd_Hi') . '.txt';
if ($compression) {
    $output_file = 'compress.zlib://' . $output_file . '.gz';
}
$just_save_results = false;
$leak_check = false;
$html_output = false;
$html_file = null;
$temp_source = null;
$temp_target = null;
$temp_urlbase = null;
$conf_passed = null;
$no_clean = false;
$cfgtypes = array('show', 'keep');
$cfgfiles = array('skip', 'php', 'clean', 'out', 'diff', 'exp');
$cfg = array();
```

下面我详细注解一下参数解析(可能有点长哦）:   

```php
if (isset($argc) && $argc > 1) {
    for ($i=1; $i<$argc; $i++) {
        $is_switch = false;
        #获取标签，eg: -s,
        $switch = substr($argv[$i],1,1);
        $repeat = substr($argv[$i],0,1) == '-';
        while ($repeat) {#是标签，则解析，否则，过
            if (!$is_switch) {
                $switch = substr($argv[$i],1,1);
            }
            $is_switch = true;
            if ($repeat) {
                foreach($cfgtypes as $type) {
                    if (strpos($switch, '--' . $type) === 0) {
                        foreach($cfgfiles as $file) {
                            if ($switch == '--' . $type . '-' . $file) {
                                $cfg[$type][$file] = true;
                                $is_switch = false;
                                break;
                            }
                        }
                    }
                }
            }
            if (!$is_switch) {
                $is_switch = true;
                break;
            }
            $repeat = false;
            switch($switch) {
                case 'r':
                case 'l':
                    $test_list = file($argv[++$i]);
                    if ($test_list) {
                        foreach($test_list as $test) {
                            $matches = array();
                            if (preg_match('/^#.*\[(.*)\]\:\s+(.*)$/', $test, $matches)) {
                                $redir_tests[] = array($matches[1], $matches[2]);
                            } else if (strlen($test)) {
                                $test_files[] = trim($test);
                            }
                        }
                    }
                    if ($switch != 'l') {
                        break;
                    }
                    $i--;
                    // break left intentionally
                case 'w':
                    $failed_tests_file = fopen($argv[++$i], 'w+t');
                    break;
                case 'a':
                    $failed_tests_file = fopen($argv[++$i], 'a+t');
                    break;
                case 'c':
                    $conf_passed = $argv[++$i];
                    break;
                case 'd':
                    $ini_overwrites[] = $argv[++$i];
                    break;
                case 'g':
                    $SHOW_ONLY_GROUPS = explode(",", $argv[++$i]);;
                    break;
                //case 'h'
                case '--keep-all':
                    foreach($cfgfiles as $file) {
                        $cfg['keep'][$file] = true;
                    }
                    break;
                //case 'l'
                case 'm':
                    $leak_check = true;
                    $valgrind_cmd = "valgrind --version";
                    $valgrind_header = system_with_timeout($valgrind_cmd, $environment);
                    $replace_count = 0;
                    if (!$valgrind_header) {
                        error("Valgrind returned no version info, cannot proceed.\nPlease check if Valgrind is installed.");
                    } else {
                        $valgrind_version = preg_replace("/valgrind-([0-9])\.([0-9])\.([0-9]+)([.-\w]+)?(\s+)/", '$1$2$3', $valgrind_header, 1, $replace_count);
                        if ($replace_count != 1 || !is_numeric($valgrind_version)) {
                            error("Valgrind returned invalid version info (\"$valgrind_header\"), cannot proceed.");
                        }
                        $valgrind_header = trim($valgrind_header);
                    }
                    break;
                case 'n':
                    if (!$pass_option_n) {
                        $pass_options .= ' -n';
                    }
                    $pass_option_n = true;
                    break;
                case '--no-clean':
                    $no_clean = true;
                    break;
                case 'p':
                    $php = $argv[++$i];
                    putenv("TEST_PHP_EXECUTABLE=$php");
                    $environment['TEST_PHP_EXECUTABLE'] = $php;
                    break;
                case 'q':
                    putenv('NO_INTERACTION=1');
                    break;
                //case 'r'
                case 's':
                    $output_file = $argv[++$i];
                    $just_save_results = true;
                    break;
                case '--set-timeout':
                    $environment['TEST_TIMEOUT'] = $argv[++$i];
                    break;
                case '--show-all':
                    foreach($cfgfiles as $file) {
                        $cfg['show'][$file] = true;
                    }
                    break;
                case '--temp-source':
                    $temp_source = $argv[++$i];
                    break;
                case '--temp-target':
                    $temp_target = $argv[++$i];
                    if ($temp_urlbase) {
                        $temp_urlbase = $temp_target;
                    }
                    break;
                case '--temp-urlbase':
                    $temp_urlbase = $argv[++$i];
                    break;
                case 'v':
                case '--verbose':
                    $DETAILED = true;
                    break;
                case 'x':
                    $environment['SKIP_SLOW_TESTS'] = 1;
                    break;
                //case 'w'
                case '-':
                    // repeat check with full switch
                    $switch = $argv[$i];
                    if ($switch != '-') {
                        $repeat = true;
                    }
                    break;
                case '--html':
                    $html_file = fopen($argv[++$i], 'wt');
                    $html_output = is_resource($html_file);
                    break;
                case '--version':
                    echo '$Id$' . "\n";
                    exit(1);
                default:
                    echo "Illegal switch '$switch' specified!\n";
                case 'h':
                case '-help':
                case '--help':
                    echo <<<HELP
Synopsis:
    php run-tests.php [options] [files] [directories]
Options:
    -l <file>   Read the testfiles to be executed from <file>. After the test
...
    --keep-[all|php|skip|clean]
                Do not delete 'all' files, 'php' test file, 'skip' or 'clean'
                file.
    --set-timeout [n]
                Set timeout for individual tests, where [n] is the number of
                seconds. The default value is 60 seconds, or 300 seconds when
                testing for memory leaks.
    --show-[all|php|skip|clean|exp|diff|out]
                Show 'all' files, 'php' test file, 'skip' or 'clean' file. You
                can also use this to show the output 'out', the expected result
                'exp' or the difference between them 'diff'. The result types
                get written independent of the log format, however 'diff' only
                exists when a test fails.
    --no-clean  Do not execute clean section if any.
HELP;
                    exit(1);
            }
        }
        if (!$is_switch) {
            $testfile = realpath($argv[$i]);
            if (!$testfile && strpos($argv[$i], '*') !== false && function_exists('glob')) {
                if (preg_match("/\.phpt$/", $argv[$i])) {
                    $pattern_match = glob($argv[$i]);
                } else if (preg_match("/\*$/", $argv[$i])) {
                    $pattern_match = glob($argv[$i] . '.phpt');
                } else {
                    die("bogus test name " . $argv[$i] . "\n");
                }
                if (is_array($pattern_match)) {
                    $test_files = array_merge($test_files, $pattern_match);
                }
            } else if (is_dir($testfile)) {
                find_files($testfile);
            } else if (preg_match("/\.phpt$/", $testfile)) {
                $test_files[] = $testfile;
            } else {
                die("bogus test name " . $argv[$i] . "\n");
            }
        }
    }
    if (strlen($conf_passed)) {
        if (substr(PHP_OS, 0, 3) == "WIN") {
            $pass_options .= " -c " . escapeshellarg($conf_passed);
        } else {
            $pass_options .= " -c '$conf_passed'";
        }
    }
    $test_files = array_unique($test_files);
    $test_files = array_merge($test_files, $redir_tests);
    // Run selected tests.
    $test_cnt = count($test_files);
    if ($test_cnt) {
        putenv('NO_INTERACTION=1');
        verify_config();
        write_information($html_output);
        usort($test_files, "test_sort");
        $start_time = time();
        if (!$html_output) {
            echo "Running selected tests.\n";
        } else {
            show_start($start_time);
        }
        $test_idx = 0;
        run_all_tests($test_files, $environment);
        $end_time = time();
        if ($html_output) {
            show_end($end_time);
        }
        if ($failed_tests_file) {
            fclose($failed_tests_file);
        }
        if (count($test_files) || count($test_results)) {
            compute_summary();
            if ($html_output) {
                fwrite($html_file, "<hr/>\n" . get_summary(false, true));
            }
            echo "=====================================================================";
            echo get_summary(false, false);
        }
        if ($html_output) {
            fclose($html_file);
        }
        if ($output_file != '' && $just_save_results) {
            save_or_mail_results();
        }
        junit_save_xml();
        if (getenv('REPORT_EXIT_STATUS') == 1 and preg_match('/FAILED(?: |$)/', implode(' ', $test_results))) {
            exit(1);
        }
        exit(0);
    }
}

```



 



 



### 参考资料  ###  

[php ci jenkins](http://ci.qa.php.net/)    
[php write-test](http://qa.php.net/write-test.php)  
[php run-tests.php](https://github.com/php/php-src/blob/PHP-5.3/run-tests.php)  
[浅谈php官方自动化测试方法](https://www.ibm.com/developerworks/cn/opensource/os-cn-php-autotest/)  


