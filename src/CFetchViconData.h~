#ifndef __FET_VICON_H
#define __FET_VICON_H
#include "Client.h"
#include "unistd.h"
#include <string>
using namespace ViconDataStreamSDK::CPP;
class ObjStatus
{
public:
	double pos[3];
	double vel[3];
    double ort[4];
	double tm;
    bool res;
    ObjStatus();
    ObjStatus & operator =(ObjStatus s);
};

class CFetchViconData
{
    private:
        Client client;
        ObjStatus lastStatus;
        
        char buf[10][20];
        double segOrg[3];
        
    public:
        char host[50];
        int segCount;
        bool Connect(const char * Hostname);
        struct ObjStatus GetStatus(const char *, const char *);
        bool Disconnect();
        bool IsConnected;
        char * GetModelName(int i);
        char * GetSegName(int i);
        

        CFetchViconData();
};
#endif
