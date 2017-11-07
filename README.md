# darknet-windows
该项目是darknet在windows下编译动态链接库的过程

编译darknet-windows的动态链接库：
1.将src和include中的文件添加到该项目中
2.因为需要生成动态链接库，darknet.h文件中某些细节需要改变，分别是：
#ifdef LIB_EXPORT  
#define DLL_API __declspec(dllexport)
#else
#define DLL_API __declspec(dllimport)
#endif
宏定义DLL_API 是为了修饰函数，将函数接口暴露到动态链接库中，具体修改了那些函数可以查看darknet.h文件和原始文件的不同
需要在项目的预处理器中添加OPENCV， LIB_EXPORT，并且将darknet.h中的
#ifndef __cplusplus 
#endif
这一对删除，原因是darknet是纯C的代码，里边调用的OPENCV的接口也是C接口，及时在VS项目中默认的会定义__cplusplus，因此需要删除
添加
#ifdef __cplusplus
extern “C” {
#endif

#ifdef __cplusplus
}
#endif
保证生成的动态链接库中的函数接口是C风格的
gettimeofday.h和gettimeofday.c中的函数可以替换掉Linux下<sys/time.h>中获取当前时间的函数
做完以上工作，就可以生成动态链接库libdarknet.lib以及libdarknet.dll

利用上边生成的dll以及lib文件，编译生成darknet的exe文件：
1.删除引入文件#include <sys/time.h>；#include <unistd.h>
2.需要将gettimeofday.h和gettimeofday.c作为一个辅助工具配置到项目中去
3.将example下的所有c文件引入到项目中
文件中会遇到go.obj中会遇到和popen,sleep,pclose相关的三个错误，按照go.c文件中相应位置改正即可，注意该方法只能保证项目能够编译通道，不涉及到go.c代码的能够可以运行，但是涉及go.c内容的应该不能运行，因为目前没有弄明白此处三个函数的目的
