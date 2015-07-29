---
layout: post
title: "XCODE LLDB命令的简单实用说明"
date: 2014-07-21 16:47:01 +0800
comments: true
categories: 
---


po（print object）是LLDB的一个命令，其主要功能是输出objective-c中对象（objects）的信息，与之相似的另外一个命令是 p（print），其主要功能是输出原生类型（boolean、integer、float、etc）的信息。

<!--more-->

可以在控制台输入

```
po self.view 或者
p self.view.subViews.count
```

#### 技巧一：运行时修改变量的值

你以前怎么验证是不是某个变量的值导致整段程序不能正常工作？修改代码中的变量的值，然后cmd+r重新启动app？现在你不需要这么做了，只需要设置一个断点，当程序在这进入调试模式后，使用expr命令即可在运行时修改变量的值。

假如有一个loginWithUsername:方法，需要两个参数：username，password。

首先设置好断点，如下图所示：

![id1](http://static.oschina.net/uploads/space/2013/0320/230128_xFae_724414.png "设置断点")


运行app，进入断点模式后，在（lldb）后输入
	
```
expr username = @"username"
expr password = @"badpassword"
```
控制台会返回以下信息

```
(NSString *) $0 = 0x3d3504c4 @"username"
(NSString *) $1 = 0x1d18ef60 @"badpassword"
```
现在跳出断点，执行断点之后的两条输出语句，控制台会有以下输出

```
(0x1c59aae0) A line for the breakpoint
(0x1c59aae0) Username and Password after: username:badpassword
```
看到看吧，我们在运行时修改了变量的值，事情还可以变的更简单一些，我们可以编辑断点，让它自动填充需要的修改的变量的值，并且可以选择在此断点处不进入断点模式，仅仅修改指定变量的值，然后自动执行后续代码。

右击断点选择“Edit Breakpoint…”（或者按住cmd+option，单击断点），然后如下图所示设置断点

![id2](http://static.oschina.net/uploads/space/2013/0320/231442_eqyO_724414.png "编辑断点")

注意选中了最后一行（“Automatically continue after evaluating”）的选择框，这就保证运行到这个断点的时，填充变量的值，然后继续运行，并不在此处断点进入调试模式。

运行app，你会得到和上述手动设置变量的值一样的输出。

接下来单击断点，使其处于禁用状态，现在箭头的颜色应该是浅蓝色的，重新运行app，你会发现username和password的值没有在运行时被改变了。

#### 技巧二：设置断点触发条件

断点的另外一个重要作用，是可以设置触发断点生效的条件，这样我们就可以在运行时针对特定的数据进行分析，观察app是否运行在正确的轨道上。如下图：

![id3](http://static.oschina.net/uploads/space/2013/0320/232822_rEBO_724414.png "触发条件")

上述截图可以看到如下语句

```
(BOOL)[(NSString*)[item valueForKey:@"ID"] isEqualToString:@"93306"]
```

通过这行语句，我们告诉编译器：当item中ID等于93306时，此断点生效，进入断点调试模式。

#### 技巧三：格式化输出数据

如果你厌倦了代码里无穷无尽的NSLog，幸运的是我们可以在编辑断点使其输出格式化字符串就像平常编码时一样。不过有一点需要注意，平常编码时可能会使用NSString‘s stringWithFormat:输出格式化字符串，不过这个方法貌似在断点中木有效果，你需要使用alloc/init形式的方法，如下：

```
po [[NSString alloc] initWithFormat:@"Item index is: %d", index]
```

运行app，就能在控制台看到想要的输出啦！

简单！强大！这就是LLDB给你的选择，从此代码里可以不用再有NSLog满天飞的情况了，代码变得更干净了，心情变得更愉悦了！

LLDB还有很多强大的地方，本教程只不过揭开了它的面纱，即便如此，仍让我们沉醉不已。

 

#### 另附命令合集

apropos           — Find a list of debugger commands related to a particular
                      	word/subject.

  breakpoint        — A set of commands for operating on breakpoints. Also see
                       _regexp-break.

  command           — A set of commands for managing or customizing the
                       debugger commands.

  disassemble       — Disassemble bytes in the current function, or elsewhere
                       in the executable program as specified by the user.

  expression        — Evaluate a C/ObjC/C++ expression in the current program
                       context, using user defined variables and variables
                       currently in scope.

  frame             — A set of commands for operating on the current thread’s
                       frames.

  gdb-remote        — Connect to a remote GDB server.  If no hostname is
                       provided, localhost is assumed.

  gui               — Switch into the curses based GUI mode.

  help              — Show a list of all debugger commands, or give details
                       about specific commands.

  kdp-remote        — Connect to a remote KDP server.  udp port 41139 is the
                       default port number.

  log               — A set of commands for operating on logs.

  memory            — A set of commands for operating on memory.

  platform          — A set of commands to manage and create platforms.

  plugin            — A set of commands for managing or customizing plugin
                       commands.

  process           — A set of commands for operating on a process.

  quit              — Quit out of the LLDB debugger.

  register          — A set of commands to access thread registers.

  script            — Pass an expression to the script interpreter for
                       evaluation and return the results. Drop into the
                       interactive interpreter if no expression is given.

  settings          — A set of commands for manipulating internal settable
                       debugger variables.

  source            — A set of commands for accessing source file information

  target            — A set of commands for operating on debugger targets.

  thread            — A set of commands for operating on one or more threads
                       within a running process.

  type              — A set of commands for operating on the type system

  version           — Show version of LLDB debugger.

  watchpoint        — A set of commands for operating on watchpoints.

Current command abbreviations (type ‘help command alias’ for more info):

  add-dsym  — (‘target symbols add’)  Add a debug symbol file to one of the
               target’s current modules by specifying a path to a debug symbols
               file, or using the options to specify a module to download
               symbols for.

  attach    — (‘_regexp-attach’)  Attach to a process id if in decimal,
               otherwise treat the argument as a process name to attach to.

  b         — (‘_regexp-break’)  Set a breakpoint using a regular expression
               to specify the location, where <linenum> is in decimal and
               <address> is in hex.

  bt        — (‘_regexp-bt’)  Show a backtrace.  An optional argument is
               accepted; if that argument is a number, it specifies the number
               of frames to display.  If that argument is ‘all’, full
               backtraces of all threads are displayed.

  c         — (‘process continue’)  Continue execution of all threads in the
               current process.

  call      — (‘expression –’)  Evaluate a C/ObjC/C++ expression in the
               current program context, using user defined variables and
               variables currently in scope.

  continue  — (‘process continue’)  Continue execution of all threads in the
               current process.

  detach    — (‘process detach’)  Detach from the current process being
               debugged.

  di        — (‘disassemble’)  Disassemble bytes in the current function, or
               elsewhere in the executable program as specified by the user.

  dis       — (‘disassemble’)  Disassemble bytes in the current function, or
               elsewhere in the executable program as specified by the user.

  display   — (‘_regexp-display’)  Add an expression evaluation stop-hook.

  down      — (‘_regexp-down’)  Go down “n” frames in the stack (1 frame by
               default).

  env       — (‘_regexp-env’)  Implements a shortcut to viewing and setting
               environment variables.

  exit      — (‘quit’)  Quit out of the LLDB debugger.

  f         — (‘frame select’)  Select a frame by index from within the
               current thread and make it the current frame.

  file      — (‘target create’)  Create a target using the argument as the
               main executable.

  finish    — (‘thread step-out’)  Finish executing the function of the
               currently selected frame and return to its call site in
               specified thread (current thread, if none specified).

  image     — (‘target modules’)  A set of commands for accessing information
               for one or more target modules.

  j         — (‘_regexp-jump’)  Sets the program counter to a new address.
  
  jump      — (‘_regexp-jump’)  Sets the program counter to a new address.

  kill      — (‘process kill’)  Terminate the current process being debugged.

  l         — (‘_regexp-list’)  Implements the GDB ‘list’ command in all of
               its forms except FILE:FUNCTION and maps them to the appropriate
               ‘source list’ commands.

  list      — (‘_regexp-list’)  Implements the GDB ‘list’ command in all of
               its forms except FILE:FUNCTION and maps them to the appropriate
               ‘source list’ commands.

  n         — (‘thread step-over’)  Source level single step in specified
               thread (current thread, if none specified), stepping over
               calls.

  next      — (‘thread step-over’)  Source level single step in specified
               thread (current thread, if none specified), stepping over
               calls.

  nexti     — (‘thread step-inst-over’)  Single step one instruction in
               specified thread (current thread, if none specified), stepping
               over calls.

  ni        — (‘thread step-inst-over’)  Single step one instruction in
               specified thread (current thread, if none specified), stepping
               over calls.

  p         — (‘expression –’)  Evaluate a C/ObjC/C++ expression in the
               current program context, using user defined variables and
               variables currently in scope.

  po        — (‘expression -O  — ‘)  Evaluate a C/ObjC/C++ expression in the
               current program context, using user defined variables and
               variables currently in scope.

  print     — (‘expression –’)  Evaluate a C/ObjC/C++ expression in the
               current program context, using user defined variables and
               variables currently in scope.

  q         — (‘quit’)  Quit out of the LLDB debugger.

  r         — (‘process launch -c /bin/sh –’)  Launch the executable in the
               debugger.

  rbreak    — (‘breakpoint set -r %1′)  Sets a breakpoint or set of
               breakpoints in the executable.

  repl      — (‘expression -r  — ‘)  Evaluate a C/ObjC/C++ expression in the
               current program context, using user defined variables and
               variables currently in scope.

  run       — (‘process launch -c /bin/sh –’)  Launch the executable in the
               debugger.

  s         — (‘thread step-in’)  Source level single step in specified thread=
               (current thread, if none specified).

  si        — (‘thread step-inst’)  Single step one instruction in specified
               thread (current thread, if none specified).

  step      — (‘thread step-in’)  Source level single step in specified thread
               (current thread, if none specified).

  stepi     — (‘thread step-inst’)  Single step one instruction in specified
               thread (current thread, if none specified).

  t         — (‘thread select’)  Select a thread as the currently active
               thread.
  
  tbreak    — (‘_regexp-tbreak’)  Set a one shot breakpoint using a regular
               expression to specify the location, where <linenum> is in
               decimal and <address> is in hex.

  undisplay — (‘_regexp-undisplay’)  Remove an expression evaluation
               stop-hook.

  up        — (‘_regexp-up’)  Go up “n” frames in the stack (1 frame by
               default).

  x         — (‘memory read’)  Read from the memory of the process being
               debug               
