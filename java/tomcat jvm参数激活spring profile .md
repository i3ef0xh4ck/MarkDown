# 新建setenv.bat
- windows  
在`%tomcat%/bin`下创建`setenv.bat`文件
- linux  
在`%tomcat%/bin`下创建`setenv.sh`文件
# 设置spring profile
setenv.bat加入如下内容
- windows  
`set "JAVA_OPTS=%JAVA_OPTS% -Dspring.profiles.active=dev"`
- linux  
`JAVA_OPTS="$JAVA_OPTS -Dspring.profiles.active=dev"`
