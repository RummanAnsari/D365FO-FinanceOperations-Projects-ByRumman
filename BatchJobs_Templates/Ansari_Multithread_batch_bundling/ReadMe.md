# About the project

Project Approach: Multithreading with Batch Bundling
Project Execution Mode: Asynchronous
Utilizing Parameters: Yes
Utilizing UI Builder Class: No
Utilizing Query as filter Parameters: No


# Project Structure
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/c816228e-44db-4e42-a345-81dbe4a19ed2" />


# Action Menu Item
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/044937ad-c9b8-4f3d-a024-a4caf3a8b650" />

# Controller Class (Main)
```
class ANSARI_CustTransBundlingController extends SysOperationServiceController
{
    public static void main(Args _args)
    {
        ANSARI_CustTransBundlingController controller = new ANSARI_CustTransBundlingController(
            classStr(ANSARI_CustTransBundlingService),
            methodStr(ANSARI_CustTransBundlingService, processCustTrans),
            SysOperationExecutionMode::Asynchronous
        );

        controller.parmDialogCaption("CustTrans Batch Bundling");
        controller.parmShowDialog(true); // show contract dialog
        controller.startOperation();
    }
}
```
# Contract Class (Main)
```
[DataContract]
class ANSARI_CustTransBundlingContract
{
    int bundleSize = 500;

    [DataMemberAttribute('Bundle size per task')]
    public int parmBundleSize(int _bundleSize = bundleSize)
    {
        bundleSize = _bundleSize;
        return bundleSize;
    }

}
```
# Service Class (Main)
```
class ANSARI_CustTransBundlingService
{


    [SysEntryPointAttribute(true)]
    public void processCustTrans(ANSARI_CustTransBundlingContract _contract)
    {
        CustTrans custTrans;
        container conRecIds = conNull();
        int counter = 0;
        int bundleSize = _contract.parmBundleSize();

        if (bundleSize <= 0)
        {
            bundleSize = 500; // fallback
        }

        // Example: select open CustTrans.
        // Open records commonly have Closed == DateNull()
        while select firstonly1000 custTrans
            where custTrans.Closed == DateNull()
        {
            conRecIds += custTrans.RecId;
            counter++;

            if (counter >= bundleSize)
            {
                this.startBundleTask(conRecIds);
                conRecIds = conNull();
                counter = 0;
            }
        }

        // remaining records
        if (counter > 0)
        {
            this.startBundleTask(conRecIds);
        }

        info("All bundles scheduled (requests submitted).");
    }

    private void startBundleTask(container _recIds)
    {
        SysOperationServiceController controller = new SysOperationServiceController(
        classStr(ANSARI_CustTransBundlingTaskService),
        methodStr(ANSARI_CustTransBundlingTaskService, processBundle),
        SysOperationExecutionMode::Asynchronous
        );

        // Get the data contract object and set the parameters
        ANSARI_BundleDataContract dataContract = controller.getDataContractObject();
        dataContract.parmRecIds(_recIds);

        controller.parmExecutionMode(SysOperationExecutionMode::Asynchronous);
        controller.parmLoadFromSysLastValue(false);
        controller.parmShowDialog(false);

        boolean started = controller.startOperation();

        if (!started)
        error("Failed to start SysOperation job for bundle.");
        else
        info("Bundle task scheduled successfully.");
    }

}
```
# Controller Class (Child Task)
```
Controller created using the main controller class with data
```
# Contract Class (Child Task)
```
[DataContractAttribute]
public class ANSARI_BundleDataContract
{
    container recIds;

    [DataMemberAttribute('RecIds')]
    public container parmRecIds(container _recIds = recIds)
    {
        recIds = _recIds;
        return recIds;
    }

}
```
# Service (Child Task)
```
public class ANSARI_CustTransBundlingTaskService
{
    [SysEntryPointAttribute(true)]
    public void processBundle(ANSARI_BundleDataContract _contract)
    {
        CustTrans custTrans;
        container recIds = _contract ? _contract.parmRecIds() : conNull();
        int recCount = conLen(recIds);
        AmountMST totalAmountMST = 0;
        AmountCur totalAmountCur = 0;

        for (int i = 1; i <= recCount; i++)
        {
            RecId r = conPeek(recIds, i);
            custTrans = CustTrans::find(r);

            if (custTrans)
            {
                // Safe fields present on CustTrans
                totalAmountMST += custTrans.AmountMST;
                totalAmountCur += custTrans.AmountCur;
            }
        }

        info(strFmt("Bundle processed: %1 records. Total AmountMST=%2, AmountCur=%3",
                    recCount, totalAmountMST, totalAmountCur));
    }

}
```

# Results
You can see result from below sections

## Execution
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/0a61df09-6827-4809-9480-63fb3e3dcfc5" />

## Parameter
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/888b7286-9b21-4c42-a4ee-a7a491bbe7db" />


## Batch job history 
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/f3cee30a-e73e-4953-b884-34092e34b75d" />




