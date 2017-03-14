## Introduction
Now that we have an simplified OS with a number of useful base classes, we need to implement resource sharing for our threads. Because the threads can be switched by the OS preemptively, a thread must be able to lock a particular device/resource such as the `CHUARTController` or the `CTWController`, until the device is in a clean state. By clean state I simply mean that any other thread running on the system can access that device/resource without any context of what was happening before. In the two examples above, this typically means receiving or sending data.

## Instructions
1. I have put updated versions of the `CHUARTController` and `CTWController` in my Dropbox, use these to replace the versions in the MWE that you working on: https://dl.dropboxusercontent.com/u/34090710/controllers.tar.gz
2. [Optional] This code is a bit of mess. It may help to clean it up a bit as I have just taken it from the Arduino project and tried to simply it / remove dependencies. For example, I think there is some double buffering in `CTWController` which could be removed.
3. We need to modify these classes to make them lockable. My approach would be to have these classes inherit from `CResource` so that we are able to do the following in code.
	
Possible example:
	
	class CDemoTask : public CTask {
	   void operator()() {
	      while(m_bEnabled) {
	         /* lock the TW controller */
	         CTWController* pcTwoWireController = CTWController::GetInstance();
	         /* Do some work */         
	         pcTwoWireController->Write(...);
	         pcTwoWireController->Write(...);
	         pcTwoWireController->Read(...);
	         pcTwoWireController->Write(...);
	         /* unlock the TW controller */
	         CTWController::ReleaseInstance(pcTwoWireController);
	         CSystem::Sleep(500);
	      }     
	   }
	};
	
Another possible example:
	
	class CDemoTask : public CTask {
	   void operator()() {
	      while(m_bEnabled) {
	         /* use a sub structure of CTWController to create a proxy 
	            object that enables reads and writes to the controller */
	         CTWController::SInstance& sTWControllerInstance = 
	            CTWController::GetInstance(this);
	         /* Do some work */         
	         sTWControllerInstance.Write(...);
	         sTWControllerInstance.Write(...);
	         sTWControllerInstance.Read(...);
	         sTWControllerInstance.Write(...);
	         /* unlock the TW controller */
	         CTWController::ReleaseInstance(sTWControllerInstance);
	         CSystem::Yield();
	      }     
	   }
	};
	
I will leave the final design and implementation up to you, but you should consider at least two approaches and have notes regarding the pros and cons of each (i.e. as content for your thesis). Start with the `CHUARTController` design as you already know how to test if this is working. Let me know if the above examples or instructions are unclear in anyway.

## Testing
1. Create two, three, and four threads that compete for control over the `CHUARTController` to write text to the serial port. i.e. basically the same as what was done previously. Perhaps you can think of a simple experiment that allows you to measure performance and/or overhead of switching task (i.e. as content for your thesis).
2. To test the `CTWController`, I think it will be easier at this point to start working on the smartblock hardware. We can then test if two threads can access the on-board accelerometer.

Here is the code I was using to test the accelerometer without the OS. You should integrate this into a class. Feel free to change any aspect of this to suit your code, e.g. the
`Firmware::GetInstance().GetTWController().FUNCTION_CALL`
syntax may no longer apply to your implementation.
	
	/* I2C Address Space */
	#define MPU6050_ADDR 0x68
	
	enum class EMPU6050Register : uint8_t {
	   /* MPU6050 Registers */
	   PWR_MGMT_1     = 0x6B, // R/W
	   PWR_MGMT_2     = 0x6C, // R/W
	   ACCEL_XOUT_H   = 0x3B, // R  
	   ACCEL_XOUT_L   = 0x3C, // R  
	   ACCEL_YOUT_H   = 0x3D, // R  
	   ACCEL_YOUT_L   = 0x3E, // R  
	   ACCEL_ZOUT_H   = 0x3F, // R  
	   ACCEL_ZOUT_L   = 0x40, // R  
	   TEMP_OUT_H     = 0x41, // R  
	   TEMP_OUT_L     = 0x42, // R  
	   WHOAMI         = 0x75  // R
	};
		
	void Firmware::TestAccelerometer() {
	   /* Array for holding accelerometer result */
	   uint8_t punMPU6050Res[8];
	
	   Firmware::GetInstance().GetTWController().BeginTransmission(MPU6050_ADDR);
	   Firmware::GetInstance().GetTWController().Write(static_cast<uint8_t>(EMPU6050Register::ACCEL_XOUT_H));
	   Firmware::GetInstance().GetTWController().EndTransmission(false);
	   Firmware::GetInstance().GetTWController().Read(MPU6050_ADDR, 8, true);
	   /* Read the requested 8 bytes */
	   for(uint8_t i = 0; i < 8; i++) {
	      punMPU6050Res[i] = Firmware::GetInstance().GetTWController().Read();
	   }
	   fprintf(m_psOutputUART, 
	           "Acc[x] = %i\r\n"
	           "Acc[y] = %i\r\n"
	           "Acc[z] = %i\r\n"
	           "Temp = %i\r\n",
	           int16_t((punMPU6050Res[0] << 8) | punMPU6050Res[1]),
	           int16_t((punMPU6050Res[2] << 8) | punMPU6050Res[3]),
	           int16_t((punMPU6050Res[4] << 8) | punMPU6050Res[5]),
	           (int16_t((punMPU6050Res[6] << 8) | punMPU6050Res[7]) + 12412) / 340);  
	}
	
## Questions, Issues and Problems

> Add your questions etc here :)