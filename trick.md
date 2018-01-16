1. 创建0字节文件
   ```
   cd.>test.txt
   ```
   奥秘在与没有任何内容重定向到文件中去


2. echo.%str% 和 echo %str%
  当变量 str 的值为空时，echo.%str% 输出了一个空行，而 echo %str% 则返回 echo 的当前状态。
  当用 for 语句读取带空行的文本，并用 echo 语句来输出时，echo.%str% 语句无疑是上上之选


3. cmd窗口检测
   【如果有误，把中文冒号换成英文冒号】
   #1. 如果系统采用的是系统管理员默认名：那么在taskmgr的应用程序选项卡中 每个窗口前会有"管理员： "
   #2. 如果系统采用的是系统管理员默认名：那么在taskmgr的应用程序选项卡中 窗口名不包括"管理员： "
   #3. 英文版是"Administrator: "
   
   ```
   taskkill /fi "WINDOWTITLE eq 管理员:  （*" >nul 2>nul
   ::taskkill /fi "WINDOWTITLE eq （*" >nul 2>nul
   
   tasklist /fi "WINDOWTITLE eq 管理员:  T*" | find "cmd.exe">nul
   if errorlevel 1 start ctl.bat
   ```


4. setx命令
   set设置的是当前运行的环境变量
   setx设置的是用户环境变量和系统环境变量
   ```
   setx userEnvirment userEnvirmentValue------默认是用户环境变量
   setx /m systemEnvirment systemEnvirmentValue------系统环境变量
   ```
   #注意,通过setx设置后，在本cmd窗口不能获取到，需要新开窗口


   
5. set命令
   ```
   set a=1, b=2 ------不可以
   set /a a=1+1, b=a+2 -----可以
   ```


6. 变量延迟函数配置返回值
   ```
   setlocal enabledelayedexpansion
   ............
   ............
   endlocal& set %4=%pickNumStr%
   ```




7. 数字结果值输入到文件出错
   ```
   set a=4
   echo %a%>1.txt     ==========>ECHO 处于打开状态。[原因是0>1.txt 1>1.txt 2>1.txt，会被作为设备标示符]
   解决方案：>1.txt echo %a%
   ```




8. set替换特别方式
   ```
   set str="%str:*a=%"
   ```
   这个替换方法，就是将str字串符中从打头字符到第一个a字符之间的所有字符替换为空（包含a字符）。





9. for循环文本文件会忽略空行
   ```
   @echo off
   for /f "delims=" %%a in ('findstr /n .* a.txt') do (
       set "str=%%a"
       setlocal enabledelayedexpansion
       echo,!str:*:=!
       endlocal
       set /a line+=1
   )
   pause>nul
   ```
   这样就不会忽略空行
   参考：http://www.bathome.net/viewthread.php?tid=11193




10. 按照空格分行
    ```
    for %%a in (0 1 4 8 9) do echo %%a
    会输出
    0
    1
    4
    8
    9
    ```




11. cmd窗口快捷键
    "Ctrl"←"键和"Ctrl+→",跳到前一个单词和后一个
    F7: 反白显示所有历史记录，配有编号。
    Ctrl+M：表示回车确认键





12. 多个set语句简化写法
    ```
    set a=1
    set b=2
    set c=3
    ::用三个 set 分别将 a b c 赋值为 1 2 3
    ==========>
    set /a a=1,b=2,c=3
    ::用一个 set /a 来一次性赋三个值
    #####################################
    【code1】
    ;if %1==a (
    ; if %2==b (
    ;   echo a b
    ; ) else echo 参数有误
    ;) else echo 参数有误
    ::用 if 嵌套判断两个参数是否都为指定值
    【code2】
    ;if %1^&%2==a^&b (
    ; echo a b
    ;) else echo 参数有误
    ::将两个 if 合并为一个
    ```
    


13. for循环读取文件忽略;开头的行
    默认情况下 eol=;




14. for循环不能跳出
    ```
    for /l %%a in (1 1 1000000) do if %%a==1 goto next
    :next
    pause
    ```
    


15. 从命令获取大量输入时，尽量使用临时文件
    ```
    for /f "delims=" %%a in ('dir /s /b \') do echo %%a
    
    dir /s /b \>tmp.txt
    for /f "delims=" %%a in (tmp.txt) do echo %%a
    rem 因为前者是把“临时文件”放在内存中的，过量的内存占用将明显影响 cmd 的执行效率，后者却不用担心这一点。
    ############################################################
    命令能合并就合并
    set m=%m: =%
    set m=@%m%@
    
    set m=@%m: =%@
    rem 命令少一条是一条，这对效率的提升也是很直观的
    #############################################################
    重定向的次数尽量少
    for /l %%a in (1 1 1000) do echo>>1.txt %%a
    
    (for /l %%a in (1 1 1000) do echo %%a)>>1.txt
    rem 重定向也是消耗时间的，所以能一次性搞定最好
    ```




16. 修改cmd颜色槽
    原始颜色 255,255,0---0,128,128
    通过注册表实现更多颜色[255,255,255]
    HKEY_CURRENT_USER\Console\%SystemRoot%_system32_cmd.exe\ColorTable00
    RGB[123 120 23]====>#7B78b7
    ColorTable00的值为8F787B
    http://technet.microsoft.com/en-us/library/cc957409.aspx
    ------->只是改变了16个颜色槽的颜色，本来0A是白色，通过修改可以变成任何颜色
    注意：直接修改system32中的cmd程序不推荐，可以得到一个cmd的快捷方式，在这个快捷方式中修改颜色


    http://blog.163.com/kalxd@126/blog/static/3351497320118323056230/
    http://en.wikipedia.org/wiki/List_of_text_editors#ASCII_and_ANSI_art
    http://sixteencolors.net/pack/blocktronics_1980




17. eudcedit使用---[charmap]
    图片大小64X64正好吻合
    图片长宽相等的可以粘贴，通过拖动铺满
    将png图片打开[画图工具][PS等]，复制，可以直接粘贴到画板上
    快速清空画板：选择框工具选取后，按删除键





18. bat程序无用变量清理
    程序完成后运行，[set>1.txt]，将当前环境变量全部打印出来，
    检查除系统环境变量之外的自定义变量是否可以优化



19. for命令读取文件时，默认跳过以;开头的行
    可以利用这个特性作为注释


    
20. 配色方案
    cf 新年灯笼
    2a 绿色森林
    b1 蔚蓝海洋
    3b 青色湖泊
    2e 草色葱茏
    3f 宁静小屋
    0a 黑客帝国
    f0 白底黑字
    6e 沙漠孤舟
    07 传统窗口
    f2 雪原绿洲
    5f 灯光舞台
    78 地狱边界
    1f 星光夜晚
    e8 沙漠足迹


    

21. 最简单的复制文件自身的方法
    ```
    copy %0 d:\\wind.bat
    ```
    



22. con、nul是MS系统的保留名，它不能做为文件名！检查你的web空间是不是M$ 系统，可以建一个con的文件夹试试




23. for循环体中的set /a
    当set/a中有()括号时，至少需要在括号处加""
    ```
    set /a aa=(%%i-1)-1   =======》报错
    set /a aa="(%%i-1)"-1   =======》OK
    set /a aa="(%%i-1)-1"   =======》OK
    set /a "aa=(%%i-1)-1"   =======》OK
    
    @echo off
    setlocal enabledelayedexpansion
    for /l %%i in (2,1,11) do (
        set /a aa="(%%i-1)"*2
        echo !aa!
    )
    pause
    ```



    
24. for中执行set, 如果没有变量会报错：变量xxxx没有定义
    ```
    for /f "tokens=2,3,4,5* delims=_=" %%i in ('set dighole_ 2^>nul') do ()
    ```






25. call 与内联函数的速度：内联函数快约10倍
    内联函数：
    1. set "function-name=function-body"
    2.要在setlocal enabledelayedexpansion之前定义完成，防止!variableName!被解析；%variableName%换成!!包含
    3.多个参数[IN OUT]   %function:#argvs#=10 20 aa%
    4.发源:http://www.bathome.net/thread-5861-1-1.html    http://www.bathome.net/viewthread.php?tid=15051&highlight=%BA%AF%CA%FD】
    ```
    call currentTimeMillis.bat time1
    for /l %%i in (1,1,100) do call :paint
    call currentTimeMillis.bat time2
    echo %time1%----%time2%
    pause
    
    :paint
    echo ok& goto :EOF
    ########################
    set "paint=echo ok"
    
    call currentTimeMillis.bat time1
    for /l %%i in (1,1,100) do %paint%
    call currentTimeMillis.bat time2
    set /a usedTime=time2-time1
    echo %usedTime%
    pause
    
    
    ##############代码写法##############
    单一参数，且参数不是变量
    set "Fun_PlaySound=start /b "" CAPI Call Winmm.dll sndPlaySoundW "$sound\#sound#.wav" #2"
    (%Fun_PlaySound:#sound#=dighole%)
    
    
    多参数，或参数是变量
    set "Params:=set argvs="
    set "ParseParams=&set n=&(for %%a in (!argvs!) do set/a n+=1&set argv!n!=%%a)"
    
    set "Fun_IsInDighole=%ParseParams%&set !argv3!=false&(for /f "tokens=2,3 delims=_=" %%i in ('set digholeX_ 2^>nul') do if !argv1!!argv2!==%%j!digholeY_%%i! set !argv3!=true)"
    (%Params:%!nextPositionX! !nextPositionY! flag %Fun_IsInDighole%)
    echo !flag!
    ```




26. cd跨分区切换路径
    /d可以切换启动器[C盘、D盘]



27. 字符串相等写法[省一个字符]
    ```
    if "%str%"=="" xxxxxxxxxxxxxxxxx
    =======>
    if %str%.==. xxxxxxxxxxxxxxxxx
    ```




28. 输出换行
    以下程序会输出
    [  bjc5233
    [  bjc5233
    需要注意的是cr下必须空上两行，^在批处理中是连接下行的含义，空两行可以让其连接上一个换行符
    ```
    @echo off
    setlocal enabledelayedexpansion
    set cr=^
    
    
    set a=bjc5233!cr!
    set b=!a!bjc5233
    echo !b!
    goto :EOF
    ```




29. 输入大量数据到文本
    ```
    A：for /l %%i in (1,1,2000) do echo bjc5233>>1.txt        [用时178][2000次输入文本]
    B：(for /l %%i in (1,1,2000) do echo bjc5233)>>2.txt      [用时12][先在内存中处理完毕,一次性输入文本]
    ```



30. 无限循环以及两种循环方式速度
    ```
    无限循环：for /l %%i in () do echo bjc5233
    
    A：for /l %%a in (1,1,2000) do echo bjc5233                            [用时59][for循环]
    B：:0
    set /a n+=1& echo bjc5233& if %n% LSS 2000 goto :0       [用时188][通过标签实现循环，效率比较低]
    ```
    
    
31. set与set /a的速度比较
    ```
    当时设置数值时，可用set /a联合提高速度
    A：set c=1000& for /l %%i in (1,1,40000) do set a=%c%& set b=%c%        [用时383]
    B：set c=1000& for /l %%i in (1,1,40000) do set /a a=c, b=c                        [用时256]
    ```


32. echo的几点用法
    A：在命令提示符输入echo off能关闭之后的回显，直到输入echo on
    B：ECHO 答复语|命令文件名             echo y|del *.*


    
33. errorlevel程序返回码
    ```
    echo %errorlevel%
    ```
    每个命令运行结束，可以用这个命令行格式查看返回码，用于判断刚才的命令是否执行成功
    默认值为0，一般命令执行出错会设 errorlevel 为1


    
34. 只读取文本第一行
    ```
    set /p line=<file.txt
    ```



35. 取制定范围内的随机数
    随机数=random余(上限-下限+1)+下限



36. color命令
    如果没有给定任何参数，该命令会将颜色还原到 CMD.EXE 启动时
    前景后景颜色一致，则不能设置成功

    
37. 打开指定文件地址
    A：地址中无空格 @start c:\path\bat
    B：地址中有空格 @start "" "C:\Users\bjc5233\AppData\Local\Microsoft\Windows\Temporary Internet Files"


38. 半角与全角       halfWidth   fullWidth
    半角占一个位置，全角占两个位置，如.与。
    
    tool_字符串长度.bat ----------有几个字符就是几
    tool_字符串长度II.bat ---------计算的是所占字节数量，半角为1，全角为2





39. for中的eol
    eol会忽略以指定字符开头的行
    eol默认为", delims默认为空格
    ```
    for /f "eol=" %%i in () do ()                   -----------------------   eol并非为空，而是"，因此会忽略分号开头的行
    for /f "eol= delims=" %%i in () do ()   -----------------------   eol并非为空，而是 ，因此会忽略空格开头的行； delims确实是空，不会指定分隔符
    for /f "eol=" %%i in () do ()             -----------------------   eol无法去除，采用折中方案，用一个不可能出现的字符
    ```


40. 当前所在路径的变量
    ```
    echo %cd%
    ```



41. for /r用法
    ```
    for /r "d:\fight" %%a in (*.bat) do set /a a+=1
    ```
    指定目录下有多少bat文件




42. set/p用法
    ```
    不换行输出：set /p=%str%<nul ----------------------- 注意:
    会换行输出：echo %str%
    不换行输出：set /p=_%str%<nul ----------------------- 注意:可处理以空格开头的变量\ 完全以空格组成的变量
    ```
    


43. 退格符号
    ```
    set /p=<nul ----------------- 从当前光标位置后退4个位置[区分全角半角，全角需要回退2次]
    ```



44. 双引号忽略特殊字符
    ```
    set str=%1
    set "str=%1"  ---------------  当%1中有特殊字符，如 <>时，使用双引号可以忽略对其解释
    ```


45. pushd \ popd
    ```
    cd
    pushd %cd%
    cd /d d:\迅雷下载
    cd
    popd
    cd
    pause>nul
    ::将当前路径通过pushd压入栈中，切换到一个临时目录，通过popd从栈中取出路径，并切换
    ```

    

46. batch two versions
    In dos batch should always make two versions:
    
    1) A more readable version with variable names understandable.
    Even indentation and spacing are important.
    
    2) The second version is the version that instead it goes fast. In this case the spaces, length of variables affect the speed. 
    This version can not be easily readable.




47. 对内联函数的使用
    http://www.dostips.com/forum/viewtopic.php?p=8386#p8386
    http://www.dostips.com/forum/viewtopic.php?f=3&t=1827



48. 跳出无限循环
    ```
    goto形式的无限循环可以使用goto方便跳出
    for /l %%i in () do () -------- for形式无限循环可以通过exit\  call跳出
    A.for /l %%i in () do (exit)
    B.for /l %%i in () do (call :loopCheck)
       :loopCheck
       if %condition% (goto :otherLabel) else (goto :EOF)  ---------- goto :EOF会重新进入for无限循环
       
    ```  
   
  
   
   
   
49. 回车符号：就是将指针指向行首
    ```
    for /f %%a in ('copy /Z "%~dpf0" nul') do set "CR=%%a"
    echo bjc5233sascsaa!CR!123
    输出1235233sascsaa
    ```
    暂时不知道该啥用




50. 内敛函数的日常使用
    在代码中如果有段比较长的代码在多个地方出现，且难以移除，只能使用call
    此时推荐做法：将该段代码设置为变量set "execCode=........my code.........."，如果有!，一般只需将!转为^!
    调用形式(%execCode%)





51. import.bat    导入内敛函数



52. start命令
    ```
    start /b xx.bat -------- 是在当前窗口中打开
    start /i  xx.bat -------- 传递给xx.bat新的环境，而非当前环境【在调用者中定义的变量不会传递给新窗口中】【一般情况下都应该加上/i】
    ```



53. 短路径
    但路径中存在空格，可以使用双引号包围，但是在for /f读取文件时，带双引号的路径会被解析为字符串，只能通过短路径来处理
    C:\Program Files\NVIDIA Corporation\license.txt   ===>    C:\PROGRA~1\NVIDIA~1\license.txt
    转换方法：  for %%i in ("C:\Program Files\NVIDIA Corporation\license.txt") do set shortPath=%%~si





54. call set\ call echo等
    以下两句效果相同
    ```
    set a=12345& setlocal enabledelayedexpansion& for /l %%i in (0,1,4) do call echo !a:~%%i,1!
    set a=12345& for /l %%i in (0,1,4) do call echo %%a:~%%i,1%%
    ```



55. set /a命令
    ```
    ::同时把数值赋予多个变量
    set /a a=b=c=d=e=f=1
    ```




56. mode命令改变屏幕大小
    ```
    mode con: cols=50 lines=20
    mode 50,20
    ```
    两种写法等效，且速度几乎没有差别，推荐使用第二种写法





57. 注释
    在括号包含语句[for if]内书写注释时，用分号经常会出错，可用REM





58. 局域网文件传输
    A设置文件共享，将桌面文件夹共享
    B使用copy命令，复制文件
    copy \\192.168.1.104\Users\BZWX\Desktop\12.txt C:\Users\bjc5233\Desktop





59. 变量值长度上限8189 ------- 在书写内敛函数时注意
    ```
    for /l %%i in (1,1,12000) do (
        set a=!a!a
        set b=!b!鲍
    )
    echo !a!>1.txt ----- 半角是8189个
    echo !b!>2.txt ----- 全角也是8189个
    ```





60. for /f 中执行命令，对于=和,和&都需要转义
    ```
    for /f "tokens=1 delims==" %%i in ('wmic DESKTOPMONITOR where Status^='ok' get ScreenWidth^,ScreenHeight /VALUE') do echo %%i
    ```








61. 读取文件技巧,可以中途跳出
    ```
    :readOneLine
    set line=0
    for /f "delims=" %%i in (b.txt) do (
        set /a line+=1
        if !line!==10 echo %%i& goto :readOneLineEnd
    )
    :readOneLineEnd
    goto :EOF
    ```





62. 统计文件行数
    A. for一行一行读取 --------- 速度快
    B. find /c /v "" <C:\path\bat\正能量\data\words.txt -----速度慢




63. 窗口标题
    ```
    使用title命令设置窗口标题，但会携带前缀"管理员：  ", 英文版也会
    start "ok" cmd "pause"      ---------  携带前缀
    start "ok" cmd /c "pause"       ---------  不携带前缀
    ```
    没有什么用处, 就是个个例





64. for语句动态条件
    ```
    for /f "!skip!" %%i in (a.txt) do ------- 报错
    for /f "%skip%" %%i in (a.txt) do ------- 正确
    ```


65. set /a可用变量名
    A.set定义变量名首先不能有=
    B.二维功能时一般需要定义x_y,           set /a 1_2=1 ----- 错误
    C.二维功能时应该定义成[加点前缀]       set /a _1_2=1 \ set /a cell1_2=1
    D.二维功能时如果x_y出现负数值          set /a cell1_-2=1 ---- 错误, ，目前还不知道怎么解决




66. 对于errorlevel返回值的处理，简单模式
    ```
    tasklist | find /i "qq.exe" && taskkill /f /im qq.exe || echo 你开了QQ?我不信
    =======================================
    tasklist | find /i "qq.exe"
    if errorlevel 1 echo 你开了QQ?我不信
    if errorlevel 0 echo taskill
    ```




67. 复制为路径【比较方便的操作】
    随便选择一个文件，shift+右键，出现菜单“复制为路径”




68. 对于内部函数, 如果不需要直接影响调用者的话，内部变量名定义格式[_xxxx]



69. 对于独立文件形的函数, 文件名约定_xxxxxx



70. 调用第三方速度
    A. cpaint 0 0 "0A" "bj" ----------- 100次执行75
    A. c:\path\bat\cpaint 0 0 "0A" "bj" ----------- 100次执行65




71. cmd中的“且”操作
    A.if %condition0% if %condition1% (xxxx) ----------- 无法直接写else中的语句
    B.(if %condition0% set /a flag+=1)& (if %condition1% set /a flag+=1)
      if flag==2 (echo 符合且) else (echo 不符合且)
    
    
    
72. 定时任务命令，已经取代at命令
    SCHTASKS
    SCHTASKS /Create /TN bjcmod /SC ONCE /ST 00:53 /TR "C:\Users\bjc5233\Desktop\taskRouter.bat C:\path\bat\bjcmod bjcmod-min.bat"
    
    
73. 处理界面时，尽量不使用cls，这种刷新会导致界面变动较大
    A.将所有界面拼接为字符串，统一输出，局限性比较大
    B.curs /pos 0 1，当输出完成后，下次输出前调用，将光标重新移动到第1行第0列，但是界面输出内容如果变动较大，可能导致界面叠加
    
    
74. 窗口宽度长度
    正常8x16模式下
    一个mode 38,7的cmd窗口，外窗口大小38*8+12, 7*16+34
	
	
	
75. 找不到对应的标签
    ```
    goto :%1>nul 2>nul& echo 没有对应的方法& pause>nul& goto :EOF
    ```
    %1是由用户输入的标签
    如果存在此标签，goto跳转，echo语句没有执行的机会
    如果不存在此标签，goto跳转失败，屏蔽报错语句；echo语句能够执行，可以在此提示用户
    


76. object项目
    ::规则 
    ::        函数前缀_表示该函数是内部函数
    ::        属性前缀_field_; 数据前缀_data_
    ::        对外可见没有加_, 隐藏属性加_
    ::        size是原始函数, 需要增加额外的holder输出参数; size_是简化的打印函数
    ::        readObject和writeObject是object级别函数
    ::              call object writeObject aa "C:\path\bat\batlearn\beta工厂\object\testMapSaved.txt"
    ::              call object readObject bb "C:\path\bat\batlearn\beta工厂\object\testMapSaved.txt"




77. goto写法(form object项目)
    ```
    if "%1"=="" endlocal& goto :EOF
    goto :%1>nul 2>nul& echo 没有对应的方法& endlocal& goto :EOF
    ```



78. 调用bat中具体方法写法(from object项目)
    ```
    ::定义
    set funList=new put get get_ size size_ containsKey containsKey_ toString clear containsValue containsValue_ remove
    (for %%i in (%funList%) do set "object.%%i=call object objectVar %2")
    ::调用
    call object objectVar
    %objectVar.get% 参数n
    ```



79. 获取字符串的重复次数的方案
    ```
    for %%a in (test 123 ABC test @#$ 123) do set /a ".%%a+=1"
    set .
    ```




80. 将tokens的取值范围无限拓展
    ```
    set str=a b c d e f g h i j k
    for /l %%x in (1 1 10) do (
    	for /f "tokens=1* delims= " %%a in ("!str!") do (
    		echo %%a
    		set str=%%b
    	)
    )
    ```


81. cmd命令变量延迟
    ```
    set a=b
    set b=c
    cmd /v:on /c echo !%a%!
    ::不需要setlocal，照样可以使用变量延迟
    ```



82. 不用变量延迟, 取变量的变量
    ```
    set key=age
    set age=18
    call echo %%%key%%%
    ```
