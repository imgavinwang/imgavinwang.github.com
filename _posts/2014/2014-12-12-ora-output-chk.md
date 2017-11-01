---
layout: post
title: ORACLE库表导出文件和库表记录数平衡性校验
date: 2014-12-12 11:00:51
categories:
- c/c++
tags:
---
从ORACLE导完数后，需要做平衡性校验，避免由于程序异常或人为失误导致导数不完整。分享个自己写的小工具。


{% highlight c++ %}
/****************************************************************************
 *ckcnt coded by Gavin @2014.
 *netgene@hotmail.com
 *
 *compile:
 *proc ckcnt.pc
 *xlc -o ckcnt ckcnt.c $ORACLE_HOME/lib/libclntsh.so -q 64
 ****************************************************************************/

#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<string.h>

EXEC SQL INCLUDE SQLCA;

char g_connstr[1024];
char g_table_name[1024];
char g_file_name[1024];

int file_cnt(const char *filename)
{
        int cnt=0;

        FILE* fd=fopen(filename, "r");
        char sLine[1024]={0};
        while ( fgets( sLine, 1024, fd) != NULL ) {
                //printf("%s\n",sLine);
                cnt++;
        }
        //printf("%d\n",cnt);
        fclose(fd);

        return cnt;
}

int table_cnt(const char *tablename, const char *connstr)
{
        int cnt=0;

        EXEC SQL BEGIN DECLARE SECTION;
        char *sql_str;
        VARCHAR orastr[1024];
        char table_name[1024];
        EXEC SQL END DECLARE SECTION;

        strcpy(orastr.arr,connstr);
        orastr.len = strlen(orastr.arr);
        orastr.arr[orastr.len]='\0';

        strcpy(table_name,tablename);

        EXEC SQL CONNECT :orastr;
        if (0 != sqlca.sqlcode) {
                printf("connect oracle[%s] err:%s\n", orastr.arr,sqlca.sqlerrm.sqlerrmc);
                return -1;
        }

        sql_str=(char *)malloc(1024);
        strcpy(sql_str,"select count(*) as cnt from ");
        strcat(sql_str,table_name);
        EXEC SQL PREPARE SQL_STR FROM :sql_str;
        EXEC SQL DECLARE table_cursor CURSOR FOR SQL_STR;
        //EXEC SQL OPEN table_cursor USING :table_name;
        EXEC SQL OPEN table_cursor;

        if (0 != sqlca.sqlcode) {
                printf("execute count table[%s] failed!sqlcode=%ld,sqlserr=%s\n",table_name,sqlca.sqlcode,sqlca.sqlerrm.sqlerrmc);
                return -1;
        }

        EXEC SQL WHENEVER NOT FOUND DO break;
        //printf("table_name:%s sql:%s\n",table_name,sql_str);
        while(1){
                EXEC SQL FETCH table_cursor INTO :cnt;
                //printf("[%d]\n",cnt);
        }

        EXEC SQL close table_cursor;
        EXEC SQL commit work release;

        return cnt;
}

int print_usage()
{
        printf( "ckcnt [-h] [-d connstr] [-t table_name] [-f filename]\n" );
        printf( "          -h Show help\n" );
        printf( "          -d connstr\n");
        printf( "          -t table_name\n");
        printf( "          -f file_name\n");
        return 0;
}

int getoption(int argc, char *argv[])
{
        extern char *optarg;
        int optch;

        static char optstring[] = "hd:t:f:";
        while ((optch = getopt(argc , argv , optstring)) != -1 ) {
                switch( optch ) {
                        case 'h':
                                print_usage();
                                exit(-1);
                        case 'd':
                                strcpy(g_connstr , optarg);
                                break;
                        case 't':
                                strcpy(g_table_name , optarg);
                                break;
                        case 'f':
                                strcpy(g_file_name , optarg);
                                break;
                        default:
                                break;
                }
        }

        if (strlen(g_connstr) == 0) {
                printf("connstr is not inputed, please check!\n");
                print_usage();
                exit(-1);
        }

        if (strlen(g_table_name) == 0) {
                printf("tablename is not inputed, please check!\n");
                print_usage();
                exit(-1);
        }

        if (strlen(g_file_name) == 0) {
                printf("filename is not inputed, please check!\n");
                print_usage();
                exit(-1);
        }
        else {
                if (access(g_file_name, F_OK) != 0) {
                        printf("filename %s is not existed!\n", g_file_name);
                        exit(-1);
                }
        }

        return 0;
}

int main(int argc,char **argv)
{
        getoption(argc,argv);

        int tablecnt=0;
        int filecnt=0;


        if ((tablecnt = table_cnt(g_table_name,g_connstr)) == -1) {
                printf("count table failed!\n");
                return -1;
        }

        if ((filecnt = file_cnt(g_file_name)) == -1) {
                printf("count file failed!\n");
                return -1;
        }

        if (tablecnt == filecnt) {
                printf("%s[%d] %s[%d] OK\n", g_table_name, tablecnt, g_file_name, filecnt);
        }
        else {
                printf("%s[%d] %s[%d] ERROR\n", g_table_name, tablecnt, g_file_name, filecnt);
        }

        return 0;
}
{% endhighlight %}
