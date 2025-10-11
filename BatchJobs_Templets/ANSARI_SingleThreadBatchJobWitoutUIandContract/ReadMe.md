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
