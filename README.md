<div align="center">

## CHTTPSocket \- Direct/ViaProxy \- Reusable Class


</div>

### Description

CHTTPSocket class with full source code,

full qualified, one step, HTTP client. Can fetch pages from web, no problems

if You try virtual host. If You use proxy server, only set some variables and

get it worked also. I also compile sample application which You can download

and test.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[ATM](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/atm.md)
**Level**          |Advanced
**User Rating**    |3.0 (18 globes from 6 users)
**Compatibility**  |C\+\+ \(general\)
**Category**       |[Internet/ Browsers/ HTML](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/internet-browsers-html__3-9.md)
**World**          |[C / C\+\+](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/c-c.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/atm-chttpsocket-direct-viaproxy-reusable-class__3-270/archive/master.zip)





### Source Code

```
//Download full source code from:
// http://www.tair.freeservers.com/
#include <stdio.h>
#include "httpsocket.h"
/************************************************************************
Sample derived class
************************************************************************/
class CMySock : public CHTTPSocket
{
char szErrMessage[255];
public:
	void OnError();
	void OnResponse();
};
//error trigger
void CMySock::OnError()
{
	wsprintf(szErrMessage,"Error: %d, %d, %s",m_nErrCode,m_nExtErrCode,m_nErrInfo);
	MessageBox(NULL,szErrMessage,"Error",MB_OK);
	CHTTPSocket::OnError();
};
//response trigger
void CMySock::OnResponse()
{
 printf("----m_ulResponseSize=%d\r\n",m_ulResponseSize);
 printf("%s\r\n",(char *)m_szResponse);
 CHTTPSocket::OnResponse();
};
//-----------------------------------------------------------------------
//call style:
//-----------------------------------------------------------------------
// dts.exe /URL http://www.yahoo.com [/PRX 127.0.0.1] [/PRT 8080]
//-----------------------------------------------------------------------
// where /URL - U see
//    /PRX - proxy's internet address
//    /PRT - proxy's port
//-----------------------------------------------------------------------
// You must have KERNEL32.DLL, USER32.DLL and WS2_32.DLL installed.
//-----------------------------------------------------------------------
/************************************************************************
main. entry point for service
************************************************************************/
void main(int argc,char* argv[])
{
	CMySock cs;
	cs.m_bUseProxy=FALSE;
	int i=0;
	char* page=NULL;
	char* serverHost=NULL;
	char* serverPort=NULL;
		while(i<argc)
	{
		if (strcmp(argv[i],"/URL")==0)
		{
			if (argv[++i]!=NULL)
			  page=argv[i];
			else
			  page=NULL;
		}
		if (strcmp(argv[i],"/PRX")==0)
		{
			if (argv[++i]!=NULL)
			  serverHost=argv[i];
			else
			  serverHost=NULL;
		}
		if (strcmp(argv[i],"/PRT")==0)
		{
			if (argv[++i]!=NULL)
			  serverPort=argv[i];
			else
			  serverPort=NULL;
		}
    i++;
	}
	if (page==NULL)
	{
		cs.ThrowError(0,0,"Please specify URL to fetch!");
		return;
	}
	if (serverHost!=NULL)
	{
		//sets proxy server's internet address
		cs.SetServerHost((const char*)serverHost);
	  i=0;
		if(serverPort!=NULL)
		 i=atoi(serverPort);
	  if (i==0)
		  i=8080;
		//sets proxy server's port number (8080 by default)
		cs.m_nServerPort=i;
		//says use proxy to CHTTPSocket derived class
	  cs.m_bUseProxy=TRUE;
	}
  printf("URL to fetch: %s\r\n",page);
	printf("Use proxy %s\r\n",serverHost);
  printf("Port for proxy %d\r\n",i);
	//page request here
	cs.Request(page);
}
and CHTTPSocket interface:
/************************************************************************
clicksocket.h
************************************************************************/
#ifndef __HTTPSOCKET__H__
#define __HTTPSOCKET__H__
#include <windows.h>
//rem next line if no debug dump wanted
#define DEBON
#include <stdio.h>
//default send and recieve timeouts in sec
#define HTTPRTIMEOUTDEF 90000
#define HTTPSTIMEOUTDEF 90000
#define MAXHOSTLENGTH  65
#define MAXIPLENGTH   16
#define MAXBLOCKSIZE  1024
#define MAXURLLENGTH  255
#define MAXHEADERLENGTH 269
//primary error codes
#define ERR_OK      0
//if this error occurs, extended code is WSA's error code
#define ERR_WSAINTERNAL 1
#define ERR_URLNOTHING  2
#define ERR_URLTOOLONG  3
#define ERR_HOSTUNKNOWN 4
#define ERR_PROXYUNKNOWN 5
#define ERR_PROTOPARSE  6
#define ERR_BADHOST   7
#define ERR_BADPORT   8
class CHTTPSocket
{
 static int nInstanceCount;
SOCKET       sckHTTPSocket;
struct sockaddr_in sinHTTPSocket;
struct sockaddr_in sinHTTPServer;
// remote server host address, size 64 bytes, 65th set to \0
	  char     m_szServerHost[MAXHOSTLENGTH];
// host
	  char     m_szHost[MAXHOSTLENGTH];
// requested URI/URL
	  char     m_szURL[MAXURLLENGTH];
// remote server IP address, size 15 bytes, 16th set to \0
	  char     m_szServerHostIP[MAXIPLENGTH];
//-- Win32 specific
WSADATA      wsaData;
void szcopy(char* dest,const char* src,int nMaxBytes);
void szsent(SOCKET sckDest,const char* szHttp);
public:
// set to TRUE in InitInstance if TIME_WAIT not need ()
    BOOL m_bNoTimeWait;
// recieve timeout change in InitInstance
    int  m_nRecvTimeout;
// send timeout change in InitInstance
    int  m_nSendTimeout;
// remote server port
	  int m_nServerPort;
// use proxy flag
    BOOL m_bUseProxy;
// error code
	  int m_nErrCode;
// extended error code;
	  int m_nExtErrCode;
// error info
	  char m_nErrInfo[255];
// response content
	  LPVOID m_szResponse;
// response size
	  ULONG m_ulResponseSize;
public:
  //const/destr
  CHTTPSocket();
  virtual ~CHTTPSocket();
  //utils
  // sets proxy or http server's host
  void SetServerHost(const char* src);
  // sets proxy or http server's ip
  //(should be skipped if SetServerHost used)
  void SetServerHostIP(const char* src);
  //starts request transaction
  void Request(const char* url="http://www.tair.freeservers.com");
  //used for free memory allocated for page
  //(should be skipped if You use CHTTPSocket::OnResponse call in OnResponse)
  void memPostup();
  //fire your OnError with specific error cdes and message
  void ThrowError(int err, int xerr, const char* errdesc);
  //overridable
  //shoul be used for additional inits
  virtual BOOL InitInstance();
  //trigger on any transaction error
  //(its great if U will call CHTTPSocket::OnError inside,
  //to free allocated memory pages)
  virtual void OnError();
  //trigger on response recieved
  //(its great if U will call CHTTPSocket::OnResponse inside,
  //to free allocated memory pages)
  virtual void OnResponse();
};
#endif
```

