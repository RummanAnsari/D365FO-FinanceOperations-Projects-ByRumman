
# 🧾 About the Project

## Project Name: ANSARI_SingleThreadBatchJobWitoutUIandContract

### Project Approach:  
**Single-threaded Batch Execution**

This project uses a basic batch job structure in Dynamics 365 Finance and Operations. It executes logic in a single thread without parallelism or bundling.

### Project Execution Mode:  
**Synchronous or Asynchronous** — Only one task is executed.

### Utilizing Parameters:  
**No** — The job does not use a data contract class for parameter input.

### Utilizing UI Builder Class:  
**No** — There is no custom UI builder for user input.

### Utilizing Query as Filter Parameters:  
**No** — The job does not use a query object to filter records.

---

## 🔍 Execution Characteristics

- The job runs as a **single batch task**.
- There is **no multithreading** or parallel task creation.
- All logic is executed within one batch thread.
- Ideal for **simple jobs** or **low-volume processing**.
- No use of `addRuntimeTask()` or `addTask()` — only one task is created and executed.

---

### ❌ Not Batch Bundling
- No workload splitting or bundling logic.

### ❌ Not Top Picking
- No staging table or dynamic record picking.

### ❌ Not Individual Task Modeling
- Only one class is used, and no multiple task classes are modeled.


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




