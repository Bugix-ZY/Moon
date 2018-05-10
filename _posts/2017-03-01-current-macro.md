---
layout: post
title: Linux - Current Macro
description: what is current macro
category: blog
---


**```current``` macro**

- **作用**：快速获得当前进程的process descriptor (即```struct task_struct```)			
- **方法**：一些做法是把指向task_struct的指针存进一个register，但x86的register很少，于是通过计算```thread_info```的位置，以此获得```task_struct ```
- **可行原因**：Process Kernel Stack, ```thread_info```, ``task_struct``间的关系

 ![processKernelStack](../../images/postImages/linux/processKernelStack.png)

- **具体代码**：

```c
#define current get_current()
#define get_current() (current_thread_info()->task)

//path: arch/arm/include/asm/thread_info.h
register unsigned long current_stack_pointer asm ("sp");// sp 栈顶指针
static inline struct thread_info *current_thread_info(void) 
{ 
	return (struct thread_info *)
      (current_stack_pointer & ~(THREAD_SIZE - 1));
}

//thread_info structure
struct thread_info {
	struct task_struct	*task;		/* main task structure */
	struct exec_domain	*exec_domain;	/* execution domain */
	......
};
```



- **```current_thread_info()```解释：**

  ``` current_stack_pointer & ~(THREAD_SIZE - 1)``` 的意思是向上对齐到栈顶部（找到``thread_info``的位置）,  然后强制类型转换成``thread_info``类型

  ``thread_info``里有``task``成员