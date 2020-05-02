# Modified WindowsSerialPort
## Introduction
Simple C a C++ library for handling serial port communication.

During embedded system development is always easier to debug the communication protocol on PC, because debugging on PC and with Visual Studio is much more comfortable than on any embedded system.

However embeeded software developers are often not interrested to develop a new quite complex component for Serial port communication on Windows OS.

This library is ready to use for any embedded developer (it requires no additional research or effort), it supports BOTH styles of data receiving - calling an event immediatelly after data are received OR waiting for specific amount of data with timeout.

You can look into example projects for Visual Studio.

## Improvements
Previous class TSerialPort provided public function ReadLine() and WriteLine(), which however are not perfect to use. 

-ReadLine()

```cpp
....
int WriteLine(char* pLine, bool addCRatEnd=true);// Function declaration

    if (pLine[lineLength-1]!=0x0D)
    {
      //Here, Previous code will add '\r' at the end of every command.
       /* char cr = 13;
        result += __WriteBuffer((unsigned char*)&cr, 1);*/

      //Here, modify it according to your UART communication format.
        char end1 = 13;
        char end2 = 10;
        result += __WriteBuffer((unsigned char*)&end1, 1);
        result += __WriteBuffer((unsigned char*)&end2, 1);
        
    }
....
```

-ReadLine()

```cpp
int ReadLine(char* pLine, int maxBufferSize, int timeOutMS=-1);// Function declaration
in int result = __ReadBuffer((unsigned char*)pLine, maxBufferSize-1, timeOutMS); 
....
    while(timeOutCounter>0)
        {            
            bytesRead = 0;
            ReadFile(m_portHandle, pData+bytesReadTotal, dataLength, &bytesRead, NULL);
            printf("byteRead: %d\n", bytesRead);
            if (bytesRead)
            {
                dataLength     -= bytesRead;
                bytesReadTotal += bytesRead;
                printf("%d\n", bytesReadTotal);
                if (dataLength == 0)  break;
                timeOutCounter = timeOutMS;

                //If last two char is ''\r\n'' ,then break.
                // You can modify below part according to your format.
                //added:..
                if ((pData[bytesReadTotal - 2] == '\r')&&(pData[bytesReadTotal - 1] == '\n'))
                    break;
            } 
            else 
            {
                timeOutCounter -= SERIALPORT_INTERNAL__TIMEOUT;
            }            
        }

....
```

