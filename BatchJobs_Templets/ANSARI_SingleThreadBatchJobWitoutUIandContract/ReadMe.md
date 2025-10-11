# Code

## Project
<img width="1236" height="664" alt="image" src="https://github.com/user-attachments/assets/80a345e7-209f-4058-b12d-7b3b2812cf15" />

## Action Menu Item
<img width="1236" height="664" alt="image" src="https://github.com/user-attachments/assets/4655e49d-c15a-4cf2-8e6b-16e2c267f60e" />


## Controller

```
Class ANSARI_SingleThreadWithoutUIandContractBatchJobController extends SysOperationServiceController
{
    protected void new()
    {
        super();
        loadFromSysLastValue = false;
     
        this.parmClassName(classStr(ANSARI_SingleThreadWithoutUIandContractBatchJobService));
        this.parmMethodName(methodStr(ANSARI_SingleThreadWithoutUIandContractBatchJobService, processOperation));
     
        this.parmDialogCaption("Your batch dialog caption");
    }
  
    public ClassDescription caption()
    {
        return "Caption Ansari"; //Batch dialog caption
    }
  
    public static void main(Args args)
    {
        ANSARI_SingleThreadWithoutUIandContractBatchJobController controller;
     
        controller = new ANSARI_SingleThreadWithoutUIandContractBatchJobController ();
        controller.startOperation();
    }
  
}


```

## Service class

```
class ANSARI_SingleThreadWithoutUIandContractBatchJobService extends SysOperationServiceBase
{
    public void processOperation()
    {
        // Example business logic output
        info("✅ Batch job started...");
  
        int i;
        for (i = 1; i <= 5; i++)
        {
            sleep(1000); // wait 1 second to simulate work
            info(strFmt("Processing record %1 of 5", i));
        }
  
        info("✅ Batch job completed successfully!");
    }
  
}


```
## Schedule 
<img width="1236" height="664" alt="image" src="https://github.com/user-attachments/assets/7df736b7-3324-4608-bae7-608849e12582" />

## Scheduled 
<img width="1236" height="664" alt="image" src="https://github.com/user-attachments/assets/d317b954-0a15-4233-826f-e1b5dbaecefe" />

## See batch job page
<img width="1236" height="664" alt="image" src="https://github.com/user-attachments/assets/8e848447-930d-4d91-9943-0054965ebbc4" />


## Output
<img width="1236" height="664" alt="image" src="https://github.com/user-attachments/assets/f549b635-fbf6-44a6-99ce-9bd69f2795a7" />




