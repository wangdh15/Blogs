---
title: xv6源码剖析之fork与系统第一个用户进程创建
date: 2020-12-25 20:06:06
categories:
	- xv6源码剖析
tags:
	- 基础知识
	- 源码阅读
	- xv6
	- MIT6.828
	- 操作系统

mathjax: true
---

## fork过程以及第一个用户进程创建过程

### fork的过程

我们知道系统中的所有进程都是通过第一个进程不断调用`fork`系统调用来创建的，`fork`系统调用会创建一个和父进程一样的子进程，只不过他们的`fork`的返回值不同，通过`fork`的返回值就可以判断在子进程还是在父进程，如果在子进程需要执行不同的代码的话，可以使用`exec`系统调用来实现。

为了更加清楚地了解上面的整个过程，我们来开始看`xv6`源码到低是怎么做的。

首先我们找到[proc.c](https://github.com/wangdh15/xv6-public/blob/master/proc.c)文件中的`fork`函数，如下所示

```c++
// Create a new process copying p as the parent.
// Sets up stack to return as if from system call.
// Caller must set state of returned proc to RUNNABLE.
int
fork(void)
{
  int i, pid;
  struct proc *np;
  struct proc *curproc = myproc();

  // Allocate process.
  if((np = allocproc()) == 0){
    return -1;
  }

  // Copy process state from proc.
  if((np->pgdir = copyuvm(curproc->pgdir, curproc->sz)) == 0){
    kfree(np->kstack);
    np->kstack = 0;
    np->state = UNUSED;
    return -1;
  }
  np->sz = curproc->sz;
  np->parent = curproc;
  *np->tf = *curproc->tf;

  // Clear %eax so that fork returns 0 in the child.
  np->tf->eax = 0;

  for(i = 0; i < NOFILE; i++)
    if(curproc->ofile[i])
      np->ofile[i] = filedup(curproc->ofile[i]);
  np->cwd = idup(curproc->cwd);

  safestrcpy(np->name, curproc->name, sizeof(curproc->name));

  pid = np->pid;

  acquire(&ptable.lock);

  np->state = RUNNABLE;

  release(&ptable.lock);

  return pid;
}
```

我们可以看到，首先会调用`allocproc`函数为新的进程分配资源，函数源码如下：

```c++
//PAGEBREAK: 32
// Look in the process table for an UNUSED proc.
// If found, change state to EMBRYO and initialize
// state required to run in the kernel.
// Otherwise return 0.
static struct proc*
allocproc(void)
{
  struct proc *p;
  char *sp;

  acquire(&ptable.lock);

  for(p = ptable.proc; p < &ptable.proc[NPROC]; p++)
    if(p->state == UNUSED)
      goto found;

  release(&ptable.lock);
  return 0;

found:
  p->state = EMBRYO;
  p->pid = nextpid++;

  release(&ptable.lock);

  // Allocate kernel stack.
  if((p->kstack = kalloc()) == 0){
    p->state = UNUSED;
    return 0;
  }
  sp = p->kstack + KSTACKSIZE;

  // Leave room for trap frame.
  sp -= sizeof *p->tf;
  p->tf = (struct trapframe*)sp;

  // Set up new context to start executing at forkret,
  // which returns to trapret.
  sp -= 4;
  *(uint*)sp = (uint)trapret;

  sp -= sizeof *p->context;
  p->context = (struct context*)sp;
  memset(p->context, 0, sizeof *p->context);
  p->context->eip = (uint)forkret;
  return p;
}
```

可以看到做了如下的事情：

1. 在`ptable`中找到一个空闲的，分配给新进程，同时设置好当前进程的状态，以及`pid`
2. 调用`kalloc`函数为进程分配内核栈，并将分配的内核栈的高地址赋给进程的`sp`
3. 在栈顶分配`trapframe`，并设置好返回地址为`trapret`，就相当于伪造了一个陷入内核的假的内核栈。然后再分配好进程切换的`context`，并设置好`scheduler`之后开始执行的指令`p -> context -> eip = (uint) forkret`，也就是每个被`fork`创建的新进程，第一次被调度之后，都是从`forkret`函数开始执行。

在分配完这些资源之后，返回到`fork`函数继续执行，可以看到接下来进行了如下的操作：

1. 使用`copyuvm`拷贝父进程的页表。
2. 将自己赋给子进程的父进程，拷贝自己的`trapframe`给子进程。
3. 将子进程的`trapframe`中的`eax`寄存器赋值为0，这就是为什么子进程会返回零！而父进程这个fork函数最终会返回子进程的PID。
4. 拷贝父进程的打开的文件表，工作目录，名字等等
5. 这些都做完了，子进程的所有状态就都设置好了，将子进程的状态设置为`RUNABLE`，并从系统调用中返回！

看完了上面的代码，我们就大概清楚`fork`系统调用做的事情了，他就是相当于创建了父进程的一个副本，不过内核栈的内容会有些区别，同时将子进程的`eax`寄存器清空，所以也就子进程返回零。同时我们可以看到子进程和父进程共享了打开的文件。

下面我们看一下`forkret`做的事情，也就是子进程创建后第一次被调度执行最开始执行的代码。通过注释可以看到，也是做了一些初始化的操作之后，就返回到了用户态。

```c++
// A fork child's very first scheduling by scheduler()
// will swtch here.  "Return" to user space.
void
forkret(void)
{
  static int first = 1;
  // Still holding ptable.lock from scheduler.
  release(&ptable.lock);

  if (first) {
    // Some initialization functions must be run in the context
    // of a regular process (e.g., they call sleep), and thus cannot
    // be run from main().
    first = 0;
    iinit(ROOTDEV);
    initlog(ROOTDEV);
  }

  // Return to "caller", actually trapret (see allocproc).
}
```

**总结：**从上面的源码可以看到，创建一个进程，就相当于创建了一个当前进程的副本，修改一部分内容而已。伪造一下内核栈的内容，然后返回就可以了。

### 整个系统的第一个进程的创建

所谓0生1，1生万物，那么操作系统里的第一个进程是哪里创建的呢。从最开始执行C代码的`main.c`开始，我们看到如下的过程：

```c
// Bootstrap processor starts running C code here.
// Allocate a real stack and switch to it, first
// doing some setup required for memory allocator to work.
int
main(void)
{
  kinit1(end, P2V(4*1024*1024)); // phys page allocator
  kvmalloc();      // kernel page table
  mpinit();        // detect other processors
  lapicinit();     // interrupt controller
  seginit();       // segment descriptors
  picinit();       // disable pic
  ioapicinit();    // another interrupt controller
  consoleinit();   // console hardware
  uartinit();      // serial port
  pinit();         // process table
  tvinit();        // trap vectors
  binit();         // buffer cache
  fileinit();      // file table
  ideinit();       // disk 
  startothers();   // start other processors
  kinit2(P2V(4*1024*1024), P2V(PHYSTOP)); // must come after startothers()
  userinit();      // first user process
  mpmain();        // finish this processor's setup
}
```

可以看到前面初始化了很多东西，我们先不管，看到最后又一个`userinit`函数，是第一个用户进程，跳进去看一下具体的内容，在文件[proc.c](https://github.com/wangdh15/xv6-public/blob/master/proc.c)中。

```c
//PAGEBREAK: 32
// Set up first user process.
void
userinit(void)
{
  struct proc *p;
  extern char _binary_initcode_start[], _binary_initcode_size[];

  p = allocproc();
  
  initproc = p;
  if((p->pgdir = setupkvm()) == 0)
    panic("userinit: out of memory?");
  inituvm(p->pgdir, _binary_initcode_start, (int)_binary_initcode_size);
  p->sz = PGSIZE;
  memset(p->tf, 0, sizeof(*p->tf));
  p->tf->cs = (SEG_UCODE << 3) | DPL_USER;
  p->tf->ds = (SEG_UDATA << 3) | DPL_USER;
  p->tf->es = p->tf->ds;
  p->tf->ss = p->tf->ds;
  p->tf->eflags = FL_IF;
  p->tf->esp = PGSIZE;
  p->tf->eip = 0;  // beginning of initcode.S

  safestrcpy(p->name, "initcode", sizeof(p->name));
  p->cwd = namei("/");

  // this assignment to p->state lets other cores
  // run this process. the acquire forces the above
  // writes to be visible, and the lock is also needed
  // because the assignment might not be atomic.
  acquire(&ptable.lock);

  p->state = RUNNABLE;

  release(&ptable.lock);
}
```

可以看到也是调用`allocproc`函数为进程分配一下资源，然后初始化`kvm`和`uvm`，手动设置第一个进程的`trapframe`，通过注释可以知道，这个`trapframe`会让这个进程返回到用户态的时候执行`initcode.S`的这段汇编。在`userinit`之后，返回到`main`函数的`mpmain`执行，

```c
// Common CPU setup code.
static void
mpmain(void)
{
  cprintf("cpu%d: starting %d\n", cpuid(), cpuid());
  idtinit();       // load idt register
  xchg(&(mycpu()->started), 1); // tell startothers() we're up
  scheduler();     // start running processes
}
```

可以看到这个函数也是做了一下操作，然后执行`scheduler`，然后就会将上面的进程调度执行了。

下面我们看一下`initcode.S`做了什么事情。

```assembly
# Initial process execs /init.
# This code runs in user space.

#include "syscall.h"
#include "traps.h"


# exec(init, argv)
.globl start
start:
  pushl $argv
  pushl $init
  pushl $0  // where caller pc would be
  movl $SYS_exec, %eax
  int $T_SYSCALL

# for(;;) exit();
exit:
  movl $SYS_exit, %eax
  int $T_SYSCALL
  jmp exit

# char init[] = "/init\0";
init:
  .string "/init\0"

# char *argv[] = { init, 0 };
.p2align 2
argv:
  .long init
  .long 0
```

可以看到，上面的进程会调用`exec`系统调用，来执行名称为`init`的代码，同时参数为0。而且这个代码应该永远不会返回，不然的话就会进行死循环。

下面来看一下`init`做了什么。

```c
// init: The initial user-level program

#include "types.h"
#include "stat.h"
#include "user.h"
#include "fcntl.h"

char *argv[] = { "sh", 0 };

int
main(void)
{
  int pid, wpid;

  if(open("console", O_RDWR) < 0){
    mknod("console", 1, 1);
    open("console", O_RDWR);
  }
  dup(0);  // stdout
  dup(0);  // stderr

  for(;;){
    printf(1, "init: starting sh\n");
    pid = fork();
    if(pid < 0){
      printf(1, "init: fork failed\n");
      exit();
    }
    if(pid == 0){
      exec("sh", argv);
      printf(1, "init: exec sh failed\n");
      exit();
    }
    while((wpid=wait()) >= 0 && wpid != pid)
      printf(1, "zombie!\n");
  }
}
```

可以看到这个函数将表述输入，标准输入和标准错误都执行了`console`这个文件，可以认为是控制窗口。然后就会不断地进行死循环，每次死循环都会创建一个新的进程，然后这个进程执行`sh`，也就是`shell`，然后等待这个`shell`返回。当这个`shell`退出之后，它还会不断地继续创建`shell`。

这样，这个系统就起来了！之后所有的进程都通过第一个进程拷贝而来。
