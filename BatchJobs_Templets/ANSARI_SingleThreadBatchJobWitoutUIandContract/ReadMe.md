# Code
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
<img width="1236" height="664" alt="image" src="https://github.com/user-attachments/assets/cf1a686d-470a-4f35-804d-2749a242952c" />


