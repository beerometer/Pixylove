# Pixylove
Self made pan and tilt

#include <Pixy2.h>
#include <PIDLoop.h>

Pixy2 pixy;
PIDLoop panLoop(400, 0, 400, true);
PIDLoop tiltLoop(500, 0, 500, true);

void setup()
{
  Serial.begin(115200);
  Serial.print("Starting...\n");
 
  
  pixy.init();
 
  pixy.changeProg("color_connected_components");
}

void loop()
{  
  static int i = 0;
  int j;
  char buf[64]; 
  int32_t panOffset, tiltOffset;
  
 
  pixy.ccc.getBlocks();
  
  if (pixy.ccc.numBlocks)
  {        
   i++;
    
   if (i%60==0)
      Serial.println(i);   
    
    
   panOffset = (int32_t)pixy.frameWidth/2 - (int32_t)pixy.ccc.blocks[0].m_x;
   tiltOffset = (int32_t)pixy.ccc.blocks[0].m_y - (int32_t)pixy.frameHeight/2;  
  
    
   panLoop.update(panOffset);
   tiltLoop.update(tiltOffset);
  
    
   pixy.setServos(panLoop.m_command, tiltLoop.m_command);
   
#if 0 
   sprintf(buf, "%ld %ld %ld %ld", rotateLoop.m_command, translateLoop.m_command, left, right);
   Serial.println(buf);   
#endif

  }  
  else 
  {
    panLoop.reset();
    tiltLoop.reset();
    pixy.setServos(panLoop.m_command, tiltLoop.m_command);
  }
}

