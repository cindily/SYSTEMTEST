#include < stdio.h > #include < conio.h > #include < stdlib.h > #include < dos.h > #include < bios.h > #define INTER 0x21 void menu() {
    printf("-----------------------------------------\n");
    printf("0.exit\n");
    printf("1.Display system time\n");
    printf("2.Display system day\n");
    printf("3.Display Mos-dos version\n");
    printf("4.Deletefile\n");
    printf("5.Createcontent\n");
    printf("6.Deletecontents\n");
    printf("7.Filename\n");
    printf("8.Display currrent list\n");
    printf("9.Echo string\n");
    printf("10.ReadingFile\n");
    printf("11.Clear screen\n");
    printf("-----------------------------------------\n");
}
void time() {定义中断信息结构体union REGS inregs,
    outregs; //     struct SREGS segregs;      读取时间inregs.h.ah=0x2C; //    int86x(INTER,&inregs,&outregs,&segregs);      printf("Now the time is:%d:%d:%d\n",outregs.h.ch,outregs.h.cl,outregs.h.dh);      getchar();      }     void day() {     定义中断信息结构体union REGS inregs,outregs;//     struct SREGS segregs;      读取日期inregs.h.ah=0x2a; //     int86x(INTER,&inregs,&outregs,&segregs);     printf("Now the day is:%d,%d,%d\n",outregs.x.cx,outregs.h.dh,outregs.h.dl);     getchar();      }   void dos()     定义中断信息结构体union REGS inregs,outregs;//{     9  
    struct SREGS segregs;版本号MS - DOS号功能读取inregs.h.ah = 0x30; //DOS 30    int86x(INTER,&inregs,&outregs,&segregs);     machine this of version DOS printf("The   is:%d,%d,%d,%d:%d\n",outregs.h.al,outregs.h.ah,outregs.h.bh,outregs.h.ah,outregs.x.cx); getchar();    }        void DeleteFile(char filename[80])     {    int result;      union REGS inregs,outregs;      struct SREGS segregs;      是删除文件41  中断号inregs.h.ah=0x41;//DOS      inregs.x.dx=FP_OFF(filename);      segregs.ds=FP_SEG(filename);      int86x(INTER,&inregs,&outregs,&segregs);     if(result==2||result==5)     {      The file may not be exist!\n",filename);    printf("\nCan't delete the file:%s     }     else     {     printf("\nThe file:%s has been delete successfully! \n",filename);      }   }       void Createfile(char filename[80]) {     union REGS inregs,outregs;      struct SREGS segregs;       inregs.h.ah=0x39;     inregs.x.dx=FP_OFF(filename);      segregs.ds=FP_SEG(filename);      int86x(INTER,&inregs,&outregs,&segregs);      表示文件未找到if(02==outregs.x.ax)//02     {     printf("\nFILE %s is not exist!\n",filename);       }     printf("success!\n");      }         void Deletefile(char filename[80]) {    int result;      union REGS inregs,outregs;     10  
    struct SREGS segregs;表示删除目录inregs.h.ah = 0x3A; //3A      inregs.x.dx=FP_OFF(filename);      segregs.ds=FP_SEG(filename);     int86x(INTER,&inregs,&outregs,&segregs);     result=outregs.x.ax;     if(result==2||result==5)     {     The file may not be exist!\n",filename);    printf("\nCan't delete the file:%s      }     else     {     printf("\nThe file:%s has been delete successfully! \n",filename);      }   }       void file(char filename1[80],char filename2[80])     {     union REGS inregs,outregs;      struct SREGS segregs;      是重命名文件56  中断号inregs.h.ah=0x56;//DOS      inregs.x.dx=FP_OFF(filename1);      segregs.ds=FP_SEG(filename1);      inregs.x.di=FP_OFF(filename2);      segregs.es=FP_SEG(filename2);     int86x(INTER,&inregs,&outregs,&segregs);     if(outregs.x.ax==02)      {       printf("\nFILE %s is not exist!\n",filename1);     }      printf("success!\n");      }     void showlist() {      char list[80];      union REGS reg;     struct SREGS segreg;      读取当前目录reg.h.ah = 0x47;//47H     reg.h.dl = 0;       reg.x.si = FP_OFF(list);    segreg.ds = FP_SEG(list);      intdosx(&reg, &reg, &segreg);      printf("The current working directory is:%s\n", list);   }     void rewords()     11  
     {
        union REGS reg,
        reg1;
        struct SREGS sreg;
        char word[200];
        for (int i = 0; i < 200; i++) word[i] = '$';
        scanf("%s", &word);
        reg1.x.dx = FP_OFF(word);
        sreg.ds = FP_SEG(word);
        reg1.h.ah = 0x09;
        int86x(INTER, &reg1, &reg1, &sreg);
        printf("\n");
    }
    void ReadFromFile(char filename[80]) {
        union REGS inregs,
        outregs;
        struct SREGS segregs;
        int result;
        char ReadContents[9 * 1024];打开文件inregs.h.ah = 0x3D; //3D     inregs.x.dx=FP_OFF(filename);     segregs.ds=FP_SEG(filename);     inregs.h.al=2;     int86x(0x21,&inregs,&outregs,&segregs);     result=outregs.x.ax;     if(2==result)     {     The file may not be exist.\n",filename);  printf("\n Can't read contents from file:%s!      }     else     {     inregs.x.bx=result;       读文件或者目录inregs.h.ah=0x3F;//     inregs.x.dx=FP_OFF(ReadContents);      segregs.ds=FP_SEG(ReadContents);      inregs.x.cx=1024;      int86x(0x21,&inregs,&outregs,&segregs);      printf("\n Real Number of read characters in file:%s:%d\n",filename,outregs);      printf("\nThe contents of file:%s!\n",filename);      for(int i=0;i<outregs.x.ax;i++)      {      printf("%c",ReadContents[i]);       }      printf("\n");    12  
        
    }
}
int main() {
    char DeleteFileName[80];
    char ReadingFileName[80];
    char file1[80],
    file2[80];
    int a;
    menu();
    do {
        printf("Please input the number:");
        scanf("%d", &a);
        switch (a) {
        case 1:
            time();
            break;
        case 2:
            day();
            break;
        case 3:
            dos();
            break;
        case 4:
            printf("\nInput the name of existing file to delete: ");
            scanf("%s", DeleteFileName);
            DeleteFile(DeleteFileName);
            break;
        case 5:
            printf("\n Input createcontent: ");
            scanf("%s", &DeleteFileName);
            Createfile(DeleteFileName);
            break;
        case 6:
            printf("\n Input deletecontents: ");
            scanf("%s", &DeleteFileName);
            Deletefile(DeleteFileName);
            break;
        case 7:
            printf("name:");
            scanf("%s", &file1);
            printf("new name:");
            scanf("%s", &file2);
            file(file1, file2);
            break;
            13
        case 8:
            showlist();
            break;
        case 9:
            rewords();
            break;
        case 10:
            printf("\nInput the name of existing file to ReadingFileName: ");
            scanf("%s", ReadingFileName);
            ReadFromFile(ReadingFileName);
            break;
        case 11:
            clrscr();
            menu();
            break;
        default:
            break;
        }
    } while ( a != 0 );
    return 0;
}
