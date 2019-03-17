#include "CFetchViconData.h"
#include "unistd.h"
#include <fstream>

ObjStatus::ObjStatus()
{
            for(int i=0;i<3;i++)
            {
                pos[i]=0;
                vel[i]=0;
                ort[i]=0;
            }
            ort[4]=0;
            tm=0;
            res=false;   
}
/*
        bool CFetchViconData::IsConnected()
        {
            return client.IsConnected().Connected;
        }*/
        
    CFetchViconData::CFetchViconData()
    {   
        IsConnected=false;
        segCount=-1;
        segOrg[0]=0.07;
        segOrg[1]=0.1;
        segOrg[2]=-0.1;
    }

ObjStatus & ObjStatus::operator =(ObjStatus s)
{
    for(int i=0;i<3;i++)
    {
        pos[i]=s.pos[i];
        vel[i]=s.vel[i];
        tm=s.tm;
        res=s.res;
    }
    return *this;
}

bool CFetchViconData::Connect(const char * Hostname)
{
	bool res=false;
    IsConnected=false;
    printf("Connecting to %s...\r\n",Hostname);
    res=(client.Connect(Hostname).Result==Result::Success);
    if(res)
    {
        printf("Successfully connected!\r\n");
        client.EnableSegmentData();
        client.EnableMarkerData();
        client.EnableUnlabeledMarkerData();
        client.EnableDeviceData();
        IsConnected=true;
        return true;
    }
    printf("Failed to connect!\r\n");
	return false;
}

bool CFetchViconData::Disconnect()
{
    if(client.IsConnected().Connected)
    {
        client.Disconnect();
    }
    IsConnected=false;
	return true;
}

ObjStatus CFetchViconData::GetStatus(const char * model,const char * segment)
{
    
	ObjStatus s;
    Output_GetFrameNumber output;

    unsigned int fn;
    Output_GetFrame outputframe;
    outputframe=client.GetFrame();
    if(outputframe.Result!=Result::Success)
    {
        s.res=false;
        return s;
    }
    
    Output_GetSegmentGlobalTranslation _Output_GetSegmentGlobalTranslation = 
          client.GetSegmentGlobalTranslation( model, segment );/*;("Patient","Foot")*/
    
    Output_GetSegmentGlobalRotationMatrix Output_Rotation = client.GetSegmentGlobalRotationMatrix( model, segment);
    
    if(_Output_GetSegmentGlobalTranslation.Result!=Result::Success)
    {
        s.res=false;
        return s;
    }
    if(_Output_GetSegmentGlobalTranslation.Occluded)
    {
        s.res=false;
        return s;
    }
    s.res=true;
   /* printf("Get a frame...");*/
    for(int i=0;i<3;i++)
    {
        s.pos[i]=_Output_GetSegmentGlobalTranslation.Translation[i]/1000;
    }
   
   
    output=client.GetFrameNumber();
    Output_GetFrameRate outframerate=client.GetFrameRate();
    if(outframerate.FrameRateHz>0)
    {
        s.tm=(double)output.FrameNumber/outframerate.FrameRateHz;
    }
    else
    {
        s.tm=0.02;
    }
    if(lastStatus.res)
    {
        double dt=s.tm-lastStatus.tm;
        if(dt<=0)
        {
            dt=0.02;   
        }
        for(int i=0;i<3;i++)
        {
            s.vel[i]=(s.pos[i]-lastStatus.pos[i])/dt;
        }
    }


    Output_GetSegmentGlobalRotationQuaternion Output_quat = client.GetSegmentGlobalRotationQuaternion( model, segment);

    if(Output_quat.Result!=Result::Success)
    {
        s.res=false;
        return s;
    }
    for(int i=0;i<4;i++)
    {

        s.ort[i]=Output_quat.Rotation[i];
    }


    lastStatus=s;
   /* printf("Return the value...");*/
	return s;
}



