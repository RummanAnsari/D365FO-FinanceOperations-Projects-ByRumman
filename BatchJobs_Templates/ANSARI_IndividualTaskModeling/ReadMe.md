# Project

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






