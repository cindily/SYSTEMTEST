# SYSTEMTEST

#include<stdio.h>
#include<windows.h>
#include<direct.h>
int main()
{
	 SYSTEMTIME time;
	 GetLocalTime(&time);
    printf("当前时间为：%2d:%2d:%2d\n",time.wHour,time.wMinute,time.wSecond);
    printf("当前日期是：%2d年%d月%d日\n",time.wYear,time.wMonth,time.wDay);

    

    char filepath[_MAX_PATH];
	  _getcwd(filepath,_MAX_PATH);
	  printf("当前工作路径为:\n");
	 printf("%s",filepath);
    

    char changepath[100];
	  printf("输入变化后的路径:\n");
    gets(changepath);
	  _chdir(changepath);
	  _getcwd(changepath,_MAX_PATH);
     printf("当前工作路径为:\n");
	  printf("%s",filepath);


    _mkdir("mydemo");
   	return 0;

}
