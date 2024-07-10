202301111112
Status: #idea
Tags: #work #bosch #camera 

# Bosch RCP Sample Application
This is code that was copied out of the RCP documentation. From this I was able to figure out how the interface was supposed to function.

```c++
3/*
RCPConnect_TCP80.c
Sample RCP Plus Version 3 application
Tested with VJ400 V6.00, VJ1000 V1.0
Compilation tested under Microsoft Visual C++ 5.0
-- make sure wsock32.lib is included in project->settings->linker-
>Object/Library Modules !!! --includes a possible output listing at the end of this file
*/

#include <stdio.h>

#include <winsock.h>

#define RCP_NETWORK_PORT (80) //the HTTP port

//definable IP addresses for this test
//the VJ for testing the RCP connection - this should be an video encoder
#define REMOTE_VJ_IP 0x0a000001 //is "10.0.0.1"

//password and length
//this is a just a default password; replace this if a password is set on the VJ
//if no password is set, all password will be accepted
#define LOGIN_STRING "+service:pass+"
#define LOGIN_STRING_LENGTH 14

//some datatypes
typedef unsigned long  DWORD;
typedef unsigned short WORD;
typedef unsigned char  OCTET;

//structures for the RCP header and commands
//the TPKT header
struct TPKT_header {
  OCTET version;
  OCTET reserved;
  WORD  size;
};

//the RCP header
struct RCPPlus_header {
  WORD  command;
  OCTET datatype;
  OCTET rd_wr: 4, //Note swap these lines on big endian machines!!
        version: 4; //Note swap these lines on big endian machines!!
  OCTET method;
  OCTET reserved;
  WORD  client_id;
  DWORD session_id;
  WORD  num;
  WORD  len;  
};

//the fix part of the RCP Register command
struct h_RCP_ClientRegister {
  OCTET RegType;
  OCTET Reserved;
  WORD  ClientID;
  OCTET PwdEncryption;
  OCTET PwdLen;
  WORD  NbrOfMessages;
  //followed by
  WORD Messages[n];
  OCTET Password[nn];
};

//the replay to the RCP register command
struct h_RCP_ClientRegisterReply {
  OCTET Result;
  OCTET Level;
  WORD  ClientID;
};

//structures and defines for requesting video
struct con_prim_header {
  OCTET method;
  OCTET media;
  OCTET status;
  OCTET reverse_login;
  DWORD reserved2;
};

#define METHOD_GET          0x00
#define METHOD_PUT          0x01

#define MEDIA_DIRECTION_TX  0x00
#define MEDIA_DIRECTION_RX  0x01

#define MEDIA_CHANNEL_VIDEO 0x01
#define MEDIA_CHANNEL_AUDIO 0x02
#define MEDIA_CHANNEL_DATA  0x03

#define CODING_MPEG4        0x0004
#define CODING_MPEG2        0x0008

#define RESOLUTION_QCIF     0x0001
#define RESOLUTION_CIF      0x0002
#define RESOLUTION_2CIF     0x0004
#define RESOLUTION_4CIF     0x0008
#define RESOLUTION_CUSTOM   0x0010

#define MEP_RTP             0x01
#define MEP_TCP_OUTBAND     0x04

struct media_desc_video {
  OCTET media_encapsulation;
  OCTET flags;
  WORD  media_port;
  DWORD media_host;
  OCTET coder;
  OCTET line;
  WORD  media_ctrl_port;
  DWORD media_ctrl_host;
  WORD  coding;
  WORD  resolution;
  WORD  reserved6;
  WORD  bandwidth;
};

int SendPacket(SOCKET Socket,
	           OCTET * TPKT_hdr,
	           int TPKT_hdr_len,
               OCTET * RCP_hdr,
               int RCP_hdr_len,
               OCTET * Payload,
               int Payload_len);

int GetPacket(SOCKET Socket,
	          OCTET * RX_Buf,
	          int RX_BufMaxLen,
	          int doprint);

void view_hex(void * pv, int l);

WORD Register(SOCKET Socket);

DWORD SendConnectPrimitive(SOCKET Socket, WORD ClientID);

int SendConnectionKeepAlive(SOCKET Socket, WORD ClientID, DWORD SessionID);

int Disconnect(SOCKET Socket, WORD ClientID, DWORD SessionID);

void main(int argc, char * argv[]) {
  WORD wVersionRequested;         //for initial access to the winsocks
  WSADATA wsaData;                //for initial access to the winsocks
  SOCKET Socket;                  //socket for the TCP connection
  struct sockaddr_in remote_sin;  //remote address
  WORD ClientID;                  //to be assigned after RCP registration
  DWORD SessionID;                //to be assigned after RCP 'connect to'
  char RX_Buf[2048];              //buffer for data from socket
  int len;                        //length of a received packet
  DWORD cnt = 0;                  //used for a simple timeout mechanism
  unsigned long int nNoBlock = 1; //for setting sockets to non - blocking
  SOCKET VideoDataSocket;         //the video receiving socket
  OCTET PassOverToRCP[] = "GET /rcp_tunnel HTTP1 .0\ r\ n\ r\ n ";
  OCTET PassVideoDataSocketToRCP[128];
  int buffsize = 128 * 1024;

  //init winsock
  wVersionRequested = MAKEWORD(2, 2);
  if (WSAStartup(wVersionRequested, & wsaData) != 0) {
    printf("Error starting winsock\n");
    exit(0);
  }

  //create a socket and connect to the RCP server
  Socket = socket(AF_INET, SOCK_STREAM, 0);

  if (Socket != INVALID_SOCKET) {
    remote_sin.sin_family = AF_INET;
    remote_sin.sin_port = htons(RCP_NETWORK_PORT);
    remote_sin.sin_addr.s_addr = htonl(REMOTE_VJ_IP);
    connect(Socket, (struct sockaddr * ) & remote_sin, sizeof(struct sockaddr));
  } else {
    exit(0);
  }

  //the connect of the the socket goes to the HTTP server on the VideoJet
  //advise the HTTP server to pass the socket internally to the RCP server

  send(Socket, PassOverToRCP, strlen(PassOverToRCP), 0);
  printf("===========================================\n");
  printf("\n\nTry to register...\n");
  Sleep(2000);
  ClientID = Register(Socket);
  Sleep(3000);

  printf("===========================================\n");
  printf("\n\nSend connect primitive...\n");
  SessionID = SendConnectPrimitive(Socket, ClientID);
  //make RCP socket non-blocking
  if (SOCKET_ERROR == ioctlsocket(Socket, FIONBIO, & nNoBlock)) {
    exit(0);
  }

  printf("===========================================\n");
  printf("\n\nOpen TCP socket for receiving video data...\n");

  //open a TCP port for receiving the video data
  VideoDataSocket = socket(AF_INET, SOCK_STREAM, 0);

  if (VideoDataSocket != INVALID_SOCKET) {
    remote_sin.sin_family = AF_INET;
    remote_sin.sin_port = htons(RCP_NETWORK_PORT);
    remote_sin.sin_addr.s_addr = htonl(REMOTE_VJ_IP);
    connect(VideoDataSocket, (struct sockaddr * ) & remote_sin, sizeof(struct sockaddr));
  } else {
    exit(0);
  }

  if (SOCKET_ERROR == setsockopt(VideoDataSocket, SOL_SOCKET,
      SO_RCVBUF, (char * ) & buffsize, sizeof(buffsize))) {
    exit(0);
  }

  //the connect of the the socket goes to the HTTP server on the VideoJet
  //advice the HTTP server to pass the socket internally to the RCP server
  //signal that this is a video receive socket to the corresponding session ID

  sprintf(PassVideoDataSocketToRCP, "GET /media_tunnel/%08lx/ %
    02 x / % 02 x HTTP 1.0\ r\ n\ r\ n ", SessionID, MEDIA_CHANNEL_VIDEO,
    MEDIA_DIRECTION_RX);
  if ((int) strlen(PassVideoDataSocketToRCP) != send(VideoDataSocket,
      PassVideoDataSocketToRCP, strlen(PassVideoDataSocketToRCP), 0)) {
    exit(0);
  }

  //make TCP socket non-blocking
  if (SOCKET_ERROR == ioctlsocket(VideoDataSocket, FIONBIO, &
      nNoBlock)) {
    exit(0);
  }

  while (1) {
    cnt++;
    //a very, very simple timeout mechanism; a timeout retrigger should be sent every 2 - 5 seconds
    if (!(cnt % 100000)) {
      SendConnectionKeepAlive(Socket, ClientID, SessionID);
    }
    GetPacket(Socket, RX_Buf, sizeof(RX_Buf), TRUE);
    //don't check responses for the keep-alive at the moment;

    //receive the video data
    len = GetPacket(VideoDataSocket, RX_Buf, sizeof(RX_Buf), FALSE);
    if (len > 0) {
      printf("rx %d\n", len);
    }
    Sleep(1);
  }
}

WORD Register(SOCKET Socket)
{
  struct TPKT_header tpkt_hdr;                          // the TPKT header
  struct RCPPlus_header rcp_hdr, * prcp_hdr;            // the RCP header and a pointer for access inside an RX buffer
  struct h_RCP_ClientRegister *prcp_reg_hdr;            // pointer to the RCP registration struct
  struct h_RCP_ClientRegisterReply * rcp_reg_reply_hdr; // pointer to access the RCP registration reply inside a buffer
  WORD NbrOfMessages, n;                                // counts the number of messages in the registration command
  WORD Messages[] = {0xFFC3, 0x01C0};                   // the messages to be registered
  OCTET *Payload, * pw;                                 // pointer to access the payload section
  WORD PayloadLen, wTPKTsize;                           // length descriptors
  char RX_Buf[2048];                                    // buffer for data from socket
  WORD ClientID;                                        // assigned client IP; to be taken from the reply

  //fill out the RCP registration
  PayloadLen = sizeof(struct h_RCP_ClientRegister) + sizeof(Messages) + LOGIN_STRING_LENGTH;
  Payload = malloc(PayloadLen);

  if (!Payload) {
    return 0;
  }

  //use pw as a write pointer
  pw = Payload;

  //write fix header
  prcp_reg_hdr = (struct h_RCP_ClientRegister * ) pw;
  prcp_reg_hdr -> RegType = 0x01;

  //Normal registration
  prcp_reg_hdr -> Reserved = 0;
  prcp_reg_hdr -> ClientID = htons(0x0000);     // no ClientID in normal registration
  prcp_reg_hdr -> PwdEncryption = 0x00;         // Plain text password encryption
  prcp_reg_hdr -> PwdLen = LOGIN_STRING_LENGTH; // Length of password string (without '\0')

  NbrOfMessages = sizeof(Messages) / sizeof(WORD);
  prcp_reg_hdr -> NbrOfMessages = htons(NbrOfMessages); // Nbr of messages that follow
  pw += sizeof(struct h_RCP_ClientRegister);

  //write message array
  for (n = 0; n < NbrOfMessages; n++) {
    ((WORD * ) pw)[n] = htons(Messages[n]); // fill in message requests
  }

  pw += sizeof(Messages);

  //write password string
  memcpy(pw,
         LOGIN_STRING,
         LOGIN_STRING_LENGTH); //fill the password string itself

  //fill out the RCP header
  rcp_hdr.command = htons(0xFF00);        // Command 'RCP register'
  rcp_hdr.datatype = 0x0c;                // Datatype P_OCTET
  rcp_hdr.rd_wr = 0x1;                    // WRITE
  rcp_hdr.version = 0x3;                  // Version 3
  rcp_hdr.method = 0x00;                  // REQUEST
  rcp_hdr.reserved = 0;
  rcp_hdr.client_id = htons(0x0000);      // currently no ClientID available
  rcp_hdr.session_id = htonl(0x00000000); // currently no SessionID available
  rcp_hdr.num = htons(0x0000);            // no num parameter needed
  rcp_hdr.len = htons(PayloadLen);        // length of the payload section fill out TPKT
  tpkt_hdr.version = 3;                   // TPKT version 3
  tpkt_hdr.reserved = 0;
  wTPKTsize = PayloadLen + sizeof(rcp_hdr) + sizeof(tpkt_hdr);
  tpkt_hdr.size = htons(wTPKTsize);       //overall length (including TPKT itself)

  //send to packet to the network
  if (-1 == SendPacket(Socket,
  	                   (OCTET * ) &tpkt_hdr,
                       sizeof(tpkt_hdr),
                       (OCTET * ) &rcp_hdr,
                       sizeof(rcp_hdr),
                       Payload,
                       PayloadLen)) {
    printf("Send packet error\n");
    exit(0);
  }

  //get the reply
  if (GetPacket(Socket, RX_Buf, sizeof(RX_Buf), TRUE) ==
      sizeof(struct RCPPlus_header) + sizeof(struct h_RCP_ClientRegisterReply)) {

    //position to RCP header
    prcp_hdr = (struct RCPPlus_header * ) RX_Buf;

    //check for error message
    if (prcp_hdr -> method == 0x03) {
      OCTET * ErrorCode = RX_Buf + sizeof(struct RCPPlus_header);
      printf("An error was returned code %02x\n", * ErrorCode);
      return 0;
    }

    //position to payload section
    rcp_reg_reply_hdr = (struct h_RCP_ClientRegisterReply * )(RX_Buf + sizeof(struct RCPPlus_header));

    //get assigned ClientID
    ClientID = ntohs(rcp_reg_reply_hdr -> ClientID);
    printf("Register reply:\n");
    printf("Result: % 02 x\ n ",rcp_reg_reply_hdr->Result);
    printf("Level: %02 x\ n ",rcp_reg_reply_hdr->Level);
    printf("ClientID %04 x\ n ",ClientID);
  } else {
    printf("Register failed\n");
  }

  return ClientID;
}


DWORD SendConnectPrimitive(SOCKET Socket, WORD ClientID) {

  struct TPKT_header tpkt_hdr;               // the TPKT header
  struct RCPPlus_header rcp_hdr, * prcp_hdr; // the RCP header and a pointer for access inside an RX buffer
  char TX_Buf[256], RX_Buf[2048], * po;      // buffer for data from socket
  struct con_prim_header *h_req;
  struct media_desc_video *md_video;
  WORD payload_len;
  DWORD sessionID = 0;

  //fill out the RCP connect primitive
  po = TX_Buf;
  h_req = (struct con_prim_header*)po;

  h_req->method = METHOD_GET;
  h_req->media = MEDIA_CHANNEL_VIDEO;
  h_req->reverse_login = FALSE;
  po += sizeof(struct con_prim_header);
  md_video = (struct media_desc_video*)po;
  md_video->media_encapsulation = MEP_TCP_OUTBAND;
  md_video->media_port = 0;
  md_video->media_host = htonl( 0 ); //transmitter shall take the destination address from the received packet
  md_video->media_ctrl_port = htons( 0 );
  md_video->media_ctrl_host = htonl( 0 );
  md_video->coding = htons( CODING_MPEG4 );
  md_video->resolution = htons( RESOLUTION_CIF );
  md_video->bandwidth = htons( 1000 );
  md_video->coder = 0;
  md_video->line = 1;
  md_video->flags = 0;
  po += sizeof(struct media_desc_video);
  payload_len = (WORD)(po - TX_Buf);

  //fill out the RCP header
  rcp_hdr.command = htons(0xFF0C);        //Command 'RCP connect primitive'
  rcp_hdr.datatype = 0x0c;                // Datatype P_OCTET
  rcp_hdr.rd_wr = 0x1;                    // WRITE
  rcp_hdr.version = 0x3;                  // Version 3
  rcp_hdr.method = 0x00;                  // REQUEST
  rcp_hdr.reserved = 0;
  rcp_hdr.client_id = htons(ClientID);    // use clientID from register reply
  rcp_hdr.session_id = htonl(0x00000000); // currently no SessionID available
  rcp_hdr.num = htons(0x0000);            // no num parameter needed
  rcp_hdr.len = htons(payload_len);       // length of the payload section

  //fill out TPKT
  tpkt_hdr.version = 3;  // TPKT version 3
  tpkt_hdr.reserved = 0; // overall length(including TPKT itself)
  tpkt_hdr.size = htons((WORD)(payload_len + sizeof(rcp_hdr) + sizeof(tpkt_hdr)));

  //send to packet to the network
  if (-1 == SendPacket(Socket,
  	                   sizeof(tpkt_hdr),
                       (OCTET*)&tpkt_hdr,
                       (OCTET*)&rcp_hdr,
                       sizeof(rcp_hdr),
                       TX_Buf,
                       payload_len)) {
    printf("Send packet error\n");
    exit(0);
  }

  //get the reply
  if(GetPacket(Socket, RX_Buf, sizeof(RX_Buf), TRUE) ==
  	 (int)(sizeof(struct RCPPlus_header) + (payload_len))) {

    //position to RCP header
    prcp_hdr = (struct RCPPlus_header*)RX_Buf;

    //check for error message
    if(prcp_hdr->method == 0x03) {
      OCTET *ErrorCode = RX_Buf + sizeof(struct RCPPlus_header);
      printf("An error was returned code %02x\n",*ErrorCode);
      return 0;
    }
    sessionID = ntohl(prcp_hdr->session_id);
  } else {
    printf("Connect to failed\n");
  }

  return sessionID;
}

int SendConnectionKeepAlive(SOCKET Socket, WORD ClientID, DWORD SessionID) {
  struct TPKT_header tpkt_hdr;   // the TPKT header
  struct RCPPlus_header rcp_hdr; // the RCP header

  //inside an RX buffer
  //Next task; read out the software version number

  //fill out the RCP header
  rcp_hdr.command = htons(0xFFC2); //Command 'Retrigger'
  rcp_hdr.datatype = 0x08; //Datatype T_DWORD
  rcp_hdr.rd_wr = 0x0; // READ
  rcp_hdr.version = 0x3; // Version 3
  rcp_hdr.method = 0x00; // REQUEST
  rcp_hdr.reserved = 0;
  rcp_hdr.client_id = htons(ClientID); // use clientID from register reply
  rcp_hdr.session_id = htonl(SessionID);
  rcp_hdr.num = htons(0x0000); // no num parameter needed
  rcp_hdr.len = 0; // no payload in READ command

  //fill out TPKT
  tpkt_hdr.version = 3; // TPKT version 3
  tpkt_hdr.reserved = 0;     // overall length(including TPKT itself)
  tpkt_hdr.size = htons(sizeof(rcp_hdr) + sizeof(tpkt_hdr));

  //send to packet to the network
  if (-1 == SendPacket(Socket,
  	                   (OCTET * ) & tpkt_hdr,
                       sizeof(tpkt_hdr),
                       (OCTET * ) & rcp_hdr,
  	                   sizeof(rcp_hdr),
                       NULL,
                       0))
  {
    printf("Send packet error\n");
    exit(0);
  }

  return 0;
}


int SendPacket(SOCKET Socket,
               OCTET * TPKT_hdr,
               int TPKT_hdr_len,
               OCTET * RCP_hdr,
               OCTET * Payload,
               int RCP_hdr_len,
               int Payload_len)
{
  int err = 0;
  printf("\n");

  //send in 3 parts (this is allowed)
  //send the TPKT header
  err += send(Socket, (char * ) TPKT_hdr, TPKT_hdr_len, 0);

  printf("snd TPKT hdr:\n");
  view_hex(TPKT_hdr, TPKT_hdr_len);

  //send the RCP header
  err += send(Socket, (char * ) RCP_hdr, RCP_hdr_len, 0);
  printf("snd RCP hdr:\n");
  view_hex(RCP_hdr, RCP_hdr_len);

  //send the payload, if one
  if (Payload_len) {
    err += send(Socket, (char * ) Payload, Payload_len, 0);
    printf("snd Payload:\n");
    view_hex(Payload, Payload_len);
  }

  printf("\n");

  //have all bytes been sent ?
  if (err != TPKT_hdr_len + RCP_hdr_len + Payload_len) {
    return -1;
  }
  return 0;
}

int GetPacket(SOCKET Socket,
	          OCTET * RX_Buf,
	          int RX_BufMaxLen,
	          int doprint)
{
  int len, wait_bytes;
  struct TPKT_header *ptpkt_hdr;

  if (RX_BufMaxLen < sizeof(struct TPKT_header)) {
    return -1;
  }

  //wait for reply
  //wait for the TPKT header to be arrived completely
  do {
    //just check; do not empty TCP buffer
    len = recv(Socket, RX_Buf, sizeof(struct TPKT_header), MSG_PEEK);
    if (len == SOCKET_ERROR) {
      //nothing to do
      return -1;
    }
  } while (len != sizeof(struct TPKT_header));

  //all bytes arrived - empty TCP buffer
  recv(Socket, RX_Buf, sizeof(struct TPKT_header), 0);

  if (doprint) {
    printf("rcv TPKT hdr:\n");
    view_hex(RX_Buf, len);
  }

  //Now the length of the entire RCP packet can be determined
  ptpkt_hdr = (struct TPKT_header * ) RX_Buf;
  wait_bytes = ntohs(ptpkt_hdr -> size) - sizeof(struct TPKT_header);

  if (RX_BufMaxLen < wait_bytes) {
    return -1;
  }
  do {
    //just check; do not empty TCP buffer
    len = recv(Socket, RX_Buf, wait_bytes, MSG_PEEK);
  } while (len != wait_bytes);

  //all bytes arrived - empty TCP buffer
  recv(Socket, RX_Buf, wait_bytes, 0);
  if (doprint) {
    printf("rcv Payload:\n");
    view_hex(RX_Buf, len);
    printf("\n");
  }

  return wait_bytes;
}

void view_hex(void * pv, int l)
{
  //improved display of hex strings
  OCTET * p = (OCTET * ) pv;
  int i;
  for (i = 0; i < l; i++) {
    if (!(i % 16) && i) printf("\n");
    printf("%02x ", p[i]);
  }
  printf("\n");
}


/**

//Possible output when running this test program
(<<-- Note: xxxxx...xxx are additional comments)
===========================================
Try to register...
snd TPKT hdr:
03 00 00 2e

snd RCP hdr:
ff 00 0c 31 00 00 00 00 00 00 00 00 00 00 00 1a

snd Payload:
01 00 00 00 00 0e 00 02 ff c3 01 c0 2b 73 65 72
76 69 63 65 3a 70 61 73 73 2b

rcv TPKT hdr:
03 00 00 18

rcv Payload:
ff 00 0c 31 01 00 00 77 00 00 00 00 00 00 00 04
01 02 00 77

Register reply:
Result:
01
Level:
02

ClientID
0077 <<-- Note: this may vary
===========================================
Try to read out the software version...
snd TPKT hdr:
03 00 00 14
snd RCP hdr:
00 2f 08 30 00 00 00 77 00 00 00 00 00 00 00 00
rcv TPKT hdr:
03 00 00 18
rcv Payload:
00 2f 08 30 01 00 00 77 00 00 00 00 00 00 00 04
11 00 06 00

Software version 11000600
===========================================
Now try to connect to a second VJ...
snd TPKT hdr:
03 00 00 24
snd RCP hdr:
ff cc 0c 31 00 00 00 77 00 00 00 00 00 00 00 10
snd Payload:
0a 00 00 02 00 00 00 00 00 00 00 01 00 00 00 00
rcv TPKT hdr:
03 00 00 24
rcv Payload:
ff cc 0c 31 01 00 00 77 c8 28 00 26 00 00 00 10
0a 00 00 02 00 00 00 00 00 00 00 01 00 00 00 00
connect to reply:
Dest IP:
0a000002
PutChannels 00000001
GetChannels 00000000 (Note VJ1000 V1.0 may return invaild data here)
connected with SessionID c8280026 <<-- Note: this may vary
===========================================
Enough for today; close connection...
© Copyright 2001-2021 Bosch Security Systems
1211Bosch RCP Plus Reference, Firmware 7.80
Appendix / Sample application in C
snd TPKT hdr:
03 00 00 14
snd RCP hdr:
ff 0d 0c 31 00 00 00 77 c8 28 00 26 00 00 00 00
rcv TPKT hdr:
03 00 00 1c
rcv Payload:
ff 0d 0c 31 01 00 00 77 c8 28 00 26 00 00 00 08
01 00 ca e4 0a 00 00 02
Disconnect reply:
Status
01
Remote Host 0a000002
===========================================
<<-- Note: these messages will only be received if an input event on
the VJ takes place
Now waiting for messages...
rcv TPKT hdr:
03 00 00 15
rcv Payload:
01 c0 00 30 02 00 00 77 00 00 00 00 00 01 00 01
00
Message 01c0 num 0001 type 00
Payload (1 bytes)
00
rcv TPKT hdr:
03 00 00 15
rcv Payload:
01 c0 00 30 02 0b 00 77 00 00 00 00 00 01 00 01
01
Message 01c0 num 0001 type 00
Payload (1 bytes)
01
rcv TPKT hdr:
03 00 00 15
rcv Payload:
01 c0 00 30 02 00 00 77 00 00 00 00 00 01 00 01
00
© Copyright 2001-2021 Bosch Security Systems
1212Bosch RCP Plus Reference, Firmware 7.80
Appendix / Sample application in C
Message 01c0 num 0001 type 00
Payload (1 bytes)
00
rcv TPKT hdr:
03 00 00 15
rcv Payload:
01 c0 00 30 02 00 00 77 00 00 00 00 00 01 00 01
01
Message 01c0 num 0001 type 00
Payload (1 bytes)
01

*/
```


# References

