
# 🧾 About the Project

## Project Name: ANSARI_IndividualTaskModeling

### Project Approach:  
**Individual Task Modeling using SysOperation Framework**
Every time you have to add batch job for different work items through rec id.

This project demonstrates how to implement a batch job in Dynamics 365 Finance and Operations using the **SysOperation framework**. Each task is modeled as a separate service class and scheduled independently to run in parallel. 

### Project Execution Mode:  
**Scheduled Batch** — Each task is submitted as a separate batch job.

### Utilizing Parameters:  
**Yes** — A shared data contract class is used to pass parameters to task.

### Utilizing UI Builder Class:  
**No** — The job does not use a custom UI builder.

### Utilizing Query as Filter Parameters:  
**No** — Filtering is handled via logic in the service classes.

---

## 🔄 Implementation Pattern

- Each task is implemented as a separate class extending `SysOperationServiceBase`.
- All service methods are decorated with `[SysEntryPointAttribute]` to enable batch execution.
- A shared data contract class (`ANSARI_TaskContract`) is used to pass task-specific parameters.
- Custom controller classes are created for each task, extending `SysOperationController`.
- The controller overrides `parmClassName()` and `parmMethodName()` to specify the service and method.
- A launcher class (`ANSARI_IndividualTaskLauncher`) is used to trigger all tasks in parallel.

---

## ❌ Not Using

- **Batch Bundling** — No workload splitting or bundling logic.
- **Top Picking** — No staging table or dynamic record picking.
- **RunBaseBatch** — Entirely based on SysOperation framework. Not based on RunBaseBatch.

---

## ✅ Benefits

- Clean separation of logic per task.
- Easy to extend with additional tasks.
- Fully compatible with D365FO batch framework.
- Supports parallel execution without complex bundling or staging.

### ❌ Not Batch Bundling

No workload bundling or record range splitting.

###  ❌ Not Top Picking

No staging table or dynamic record picking logic.

### ✅ Individual Task Modeling

- Each task is modeled as a separate service class.
- Tasks are scheduled independently and executed in parallel.



# Project Structure
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/d1bcf8b3-ce9e-48b4-a882-c6a04bf13bfe" />

## Action Menu Item
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/54e3d0b0-f1f7-4de6-b73f-133031aef047" />


# Code 

## Controller Class
```
class ANSARI_IndividualTaskController extends SysOperationServiceController
{
    /// <summary>
    /// Construct a SysOperationController for the task service
    /// </summary>
    public static SysOperationController construct(Args _args = null)
    {
        SysOperationController controller = new ANSARI_IndividualTaskController(
            classStr(ANSARI_IndividualTaskService),
            methodStr(ANSARI_IndividualTaskService, process));
        controller.parmDialogCaption("ANSARI Individual Task Modeling");
        controller.parmArgs(_args);
        return controller;
    }

    public ClassDescription caption()
    {
        return "Caption Individual Task Modeling Ansari"; //Batch dialog caption
    }

    /// <summary>
    /// Main entry point
    /// </summary>
    public static void main(Args _args)
    {
        ANSARI_IndividualTaskController::construct(_args).startOperation();
    }

}
```
# Contract Class
```
[DataContractAttribute]
public class ANSARI_BatchIndividualTaskContract
{
    RecId recId;
    int64 workItemIndex;

    [DataMemberAttribute("RecId"), SysOperationLabelAttribute("Rec ID")]
    public RecId parmRecId(RecId _recId = recId)
    {
        recId = _recId;
        return recId;
    }

    [DataMemberAttribute("WorkItemIndex"), SysOperationLabelAttribute("Work Item Index")]
    public int64 parmWorkItemIndex(int64 _workItemIndex = workItemIndex)
    {
        workItemIndex = _workItemIndex;
        return workItemIndex;
    }

}
```
# Service Class
```
public class ANSARI_IndividualTaskService extends SysOperationServiceBase
{
    [SysEntryPoint]
    public void process(ANSARI_BatchIndividualTaskContract _contract)
    {
        // Delegate actual processing to WorkItem class
        ANSARI_IndividualTaskWorkItem::doProcessWorkItem(
            _contract.parmRecId(),
            _contract.parmWorkItemIndex()
        );
    }

}
```
# Individaul Task Handling Class
```
internal final class ANSARI_IndividualTaskWorkItem
{
    public static void doProcessWorkItem(RecId _recId, int64 _index)
    {
        CustTable custTable;

        select firstOnly custTable
            where custTable.RecId == _recId;

        if (custTable)
        {
            info(strFmt("ANSARI: WorkItem %1 — AccountNum=%2",
                        _index,
                        custTable.AccountNum));
        }
        else
        {
            warning(strFmt("ANSARI: WorkItem %1 — CustTable record not found for RecId=%2",
                           _index, _recId));
        }
    }

}
```

# Result

You can make your action menu item as startup object and click on the start button to schedule the batch job. See the green start button.

<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/3f835c78-6f38-4f43-955f-bf3e978fa8d3" />


## Schedule batch job
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/78df5530-4cc2-4fb5-94d6-75741fd41ef4" />


##  In batch job page
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/4604e7e4-4189-4b1c-be23-39586e9bad7c" />

## Inside batch job page
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/e9faaf76-0540-4d69-8902-e9f6caa2b421" />


## Batch job history
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/6a6890cb-4196-46a0-83d0-fb5d356b5a8e" />






