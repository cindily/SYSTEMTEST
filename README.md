#include<stdio.h> 
#include<conio.h>  
#include<stdlib.h>  
#include<dos.h>  
#include<bios.h>  
#define INTER 0x21 



 
void menu()  
{ 
  printf("-----------------------------------------\n"); 
  printf("0.exit\n");  
  printf("1.Display system time\n");  
  printf("2.Display system day\n");  
  printf("3.Display Mos-dos version\n");  
  printf("4.Deletefile\n");  
  printf("5.Createfile\n");  
  printf("6.newdirectory\n");  
  printf("7.rename\n");  
  printf("8.Display currrent list\n");  
  printf("9.Echo string\n");  
  printf("10.ReadingFile\n");  
  printf("11.Clear screen\n"); 
  printf("12.Deletedirectory\n");
  printf("13.changedir\n");
  printf("-----------------------------------------\n");  
} 


void time() 
{ 
  union REGS inregs,outregs;
   struct SREGS segregs;  
  inregs.h.ah=0x2C;  
  int86x(INTER,&inregs,&outregs,&segregs);  
  printf("Now the time is:%d:%d:%d\n",outregs.h.ch,outregs.h.cl,outregs.h.dh);  
  getchar();  
}

 
void day() 
{ 
   union REGS inregs,outregs; 
   struct SREGS segregs;  
   inregs.h.ah=0x2a; 
   int86x(INTER,&inregs,&outregs,&segregs);  
   printf("Now the day is: %d,%d,%d\n",outregs.x.cx,outregs.h.dh,outregs.h.dl);  
   getchar();  
}

  
void dos()
{   
  union REGS inregs,outregs;
  struct SREGS segregs;  
  inregs.h.ah=0x30;
  int86x(INTER,&inregs,&outregs,&segregs);  
  printf(" The  DOS  version of this machine is: %d,%d,%d,%d,%d\n",outregs.h.al,outregs.h.ah,outregs.h.bh,outregs.h.ah,outregs.x.cx); 
  getchar(); 
}  

 
void DeleteFile(char filename[80]) 
{  
   union REGS inregs,outregs;  
   struct SREGS segregs;  
   inregs.h.ah=0x41;  
   inregs.x.dx=FP_OFF(filename);  
   segregs.ds=FP_SEG(filename);  
   int86x(INTER,&inregs,&outregs,&segregs);
   int result = outregs.x.ax; 
   if(result==2 || result==5)  
   {  
      printf("\nCan't delete the file:%s The file may not be exist!\n",filename);  
   }  
   else  
   {  
   printf("\nThe file:%s has been delete successfully! \n",filename);   
   }  
} 
 

void Deletedirectory(char filename[80]) 
{  
	union REGS inregs,outregs;  
	struct SREGS segregs;  
	inregs.h.ah=0x3A;  
	inregs.x.dx=FP_OFF(filename);  
	segregs.ds=FP_SEG(filename);  
	int86x(INTER,&inregs,&outregs,&segregs);
	int result = outregs.x.ax; 
	if(result!=0)  
	{  
		printf("The directory has be delete!\n",Deletedirectory);  
	}  
	
} 

void Createfile(char filename[80])
{
	
	union REGS inregs,outregs;
	struct SREGS segregs;
	inregs.h.ah=0x5B;

	inregs.x.dx=FP_OFF(filename);   //获取偏移地址
	
	segregs.ds=FP_SEG(filename);  //获取段地址
	inregs.x.cx=0;
	 
	int86x(INTER,&inregs,&outregs,&segregs);
	if(outregs.x.ax==2)
	{
		printf("Fail To Create New File.\n");
	
	}
	if(outregs.x.ax==80)
	{
		printf("Fail to create file: %s The file has be existed.\n", filename); 
		
	}
	
	printf("Create New File: %s Successfully!\n",filename);
	printf("The File's Attribute is: %d\n",outregs.x.cx);
	printf("The Filecode is:%d.\n",outregs.x.ax);
	
}



void newdirectory(char filename[80]) 
{  
	union REGS inregs,outregs;  
	struct SREGS segregs;  
	inregs.h.ah=0x39;  
	inregs.x.dx=FP_OFF(filename);  
	segregs.ds=FP_SEG(filename);  
	int86x(INTER,&inregs,&outregs,&segregs);
	
	printf("The directory has be new!\n",Deletedirectory);    
	
} 

 

void rename(char filename1[80],char filename2[80]) 
{ 
   union REGS inregs,outregs;  
   struct SREGS segregs;  
   inregs.h.ah=0x56;
   inregs.x.dx=FP_OFF(filename1);  
   segregs.ds=FP_SEG(filename1);  
   inregs.x.di=FP_OFF(filename2);  
   segregs.es=FP_SEG(filename2);  
   int86x(INTER,&inregs,&outregs,&segregs);  
   if(outregs.x.ax==02)  
   {  
     printf("\nFILE %s is not exist!\n",filename1);  
   }  
   printf("success!\n");  
} 


void showlist() {  
   char list[80];  
   union REGS reg;  
   struct SREGS segreg;  
   reg.h.ah = 0x47; 
   reg.h.dl = 0;  
   reg.x.si = FP_OFF(list); 
   segreg.ds = FP_SEG(list);  
   intdosx(&reg, &reg, &segreg);  
   printf("The current working directory is:%s.\n", list);  
}
   
   
void rewords()  
{ 
   union REGS reg,reg1;  
   struct SREGS sreg;  
   char word[200];  
   for(int i=0;i<200;i++) word[i]='$';  
   scanf("%s",&word);  
   reg1.x.dx = FP_OFF(word);  
   sreg.ds = FP_SEG(word);  
   reg1.h.ah=0x09;  
   int86x(INTER,&reg1,&reg1,&sreg);  
   printf("\n");  
}  
 
   
void ReadFromFile(char filename[80]) 
{ 
  union REGS inregs,outregs;  
  struct SREGS segregs;  
  int result;  
  char ReadContents[9*1024];  
  inregs.h.ah=0x3D;
  inregs.x.dx=FP_OFF(filename);  
  segregs.ds=FP_SEG(filename);  
  inregs.h.al=2;  
  int86x(INTER,&inregs,&outregs,&segregs);  
  result=outregs.x.ax;  
  if(2==result)  
  {  
     printf("\n Can't read contents from file:%s! The file may not be exist.\n",filename);
		 
  }  
  else  
  {  
  inregs.x.bx=result;   
   inregs.h.ah=0x3F;  
  inregs.x.dx=FP_OFF(ReadContents);   
  segregs.ds=FP_SEG(ReadContents);   
  inregs.x.cx=1024;   
  int86x(0x21,&inregs,&outregs,&segregs);   
  printf("\n Real Number of read characters in file:%s:%d\n",filename,outregs);   
  printf("\nThe contents of file:%s!\n",filename);   
  for(int i=0;i<outregs.x.ax;i++)   
  {   
  printf("%c",ReadContents[i]);    
  }   
  printf("\n"); 

   
  
  }  
 
} 


/* 7. Change Directory */
void changeDir() {
    union REGS inregs,
    outregs;
    struct SREGS segregs;
    char dir[80];
    printf("Please input where you wanna change your directory:\n");
    scanf("%s",dir);
    inregs.x.dx = FP_OFF(dir);
    segregs.ds = FP_SEG(dir);
    inregs.h.ah = 0x3B;
    int86x(INTER, &inregs, &outregs, &segregs);
    if (outregs.x.ax == 02) printf("Failed to change current directory!\n");
    else printf("Successfully change current directory!\n");
    printf("\n");
}





 
int main()  
{ 
   char DeleteFileName[80];  
   char ReadingFileName[80];  
   char file1[80],file2[80]; 
   char newname[80];
   int a;  
   menu();  
   do  
   {  
   printf("Please input the number:");  
   scanf("%d",&a);  
   switch(a) {  
   case 1: time();  
   break;  
   case 2: day();  
   break;  
   case 3: dos();  
   break;  
   case 4:  
   printf("\nInput the name of existing file to delete: ");  
   scanf("%s",DeleteFileName);  
   DeleteFile(DeleteFileName);  
   break;  
   case 5:  
   printf("\n Input createcontent: ");  
   scanf("%s",&DeleteFileName);  
   Createfile(DeleteFileName);  
   break;  
   case 6:  
   printf("\n Input newname: ");  
   scanf("%s",&newname);  
   newdirectory(newname); 
   break;  
   case 7: printf("name:");  
   scanf("%s",&file1);  
   printf("new name:");  
   scanf  
   ("%s",&file2);  
   rename(file1,file2);  
   break;  
  
   case 8:  
   showlist();  
   break;  
   case 9:  
   rewords();  
   break;  
   case 10:  
   printf("\nInput the name of existing file to ReadingFileName: ");  
   scanf("%s",ReadingFileName);   
   ReadFromFile(ReadingFileName);  
   break;  
   case 11: clrscr();  
   menu();  
   break;
   case 12: 
	   printf("\nInput the name of directory to Delete: ");
	   scanf("%s",DeleteFileName);
   Deletedirectory(DeleteFileName);
   case 13: 
   changeDir();  
   break; 
   default: break;  
   } 
   } 
   while(a!=0);  
   return 0;  
} 
