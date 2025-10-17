
# 🧾 About the Project

## Project Name: ANSARI_MultiThreadThreadBatchJob

### Project Approach:  
**Multithreading with Batch Bundling**

This project uses the **Batch Bundling** pattern to achieve parallel execution in Dynamics 365 Finance and Operations. It dynamically creates multiple batch tasks using `addRuntimeTask()`, each responsible for processing a specific range of records.

### Project Execution Mode:  
**Asynchronous** — Tasks are executed in parallel across batch threads.

### Utilizing Parameters:  
**Yes** — Each task receives parameters (e.g., record range) to define its scope.

### Utilizing UI Builder Class:  
**No** — The job does not use a custom UI builder.

### Utilizing Query as Filter Parameters:  
**No** — Filtering is handled via logic, not through query-based parameters.

---

## 🔄 My Bundling Approach

- Splits the workload into **bundles** based on total record count and thread count.
- Each bundle is assigned to a **separate batch task** using `addRuntimeTask()`.
- Tasks are executed **in parallel**, leveraging multiple threads on the batch server.
- **No staging table** is used.
- **Not Top Picking** — Top Picking involves a staging table where each thread picks the next available record dynamically.
- **Not Individual Task Modeling** — That approach involves creating separate classes for each task, typically used when tasks are few or have dependencies.


# Project
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/24526cca-5706-4942-ac85-4e0f9a20b8d2" />


# Menu Item
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/e3bfb5a5-1d90-4adf-b24c-3748d8dd269f" />


# controller
```
class ANSARI_MultiThreadBatchJobController extends SysOperationServiceController
{
    protected void new()
    {
        super();
        loadFromSysLastValue = false;
     
        this.parmClassName(classStr(ANSARI_MultiThreadBatchService));
        this.parmMethodName(methodStr(ANSARI_MultiThreadBatchService, runTasks));
     
        this.parmDialogCaption("Your batch dialog caption");
    }

    public ClassDescription caption()
    {
        return "Caption Ansari"; //Batch dialog caption
    }

    public static void main(Args args)
    {
        ANSARI_MultiThreadBatchJobController controller;
     
        controller = new ANSARI_MultiThreadBatchJobController ();
        controller.startOperation();
    }

}
```
# contract
```[DataContractAttribute]
class ANSARI_MultiThreadBatchDataContract
{
    Integer        batchTaskSize;
    container      recIdList;

    /// <summary>
    /// parmBatchTaskSize
    /// </summary>
    /// <param name = "_batchTaskSize">_batchTaskSize</param>
    /// <returns>batchTaskSize</returns>
    [DataMember, SysOperationLabel("Batch task size")]
    public Integer parmBatchTaskSize(Integer _batchTaskSize = batchTaskSize)
    {
        batchTaskSize = _batchTaskSize;
        return batchTaskSize;
    }

    [DataMemberAttribute]
    public container parmRecIdList(container _recIdList = recIdList)
    {
        recIdList = _recIdList;
        return recIdList;
    }

    /// <summary>
    /// Pack current instance
    /// </summary>
    /// <returns>Packed instance</returns>
    public container pack()
    {
        return ANSARI_Helper::packContract(this);
    }

    /// <summary>
    /// Unpack the passed container
    /// </summary>
    /// <param name = "_packedObject">Packed instance</param>
    /// <returns>True when successfully unpacked</returns>
    public boolean unpack(container _packedObject)
    {
        ANSARI_Helper::unpackDataContract(_packedObject, this);
        return true;
    }

}

```
# helper
```
class ANSARI_Helper
{
    /// This is helper class to maintain all common methods and increase maintainability and readability of the code
        
    #define.packUnpackDcParentMethodParameterName('return')

    /// <summary>
    /// Packs a data contract
    /// </summary>
    /// <param name = "_dataContract">The data contract to pack</param>
    /// <returns> A container with the packed data contract</returns>
    public static container packContract(Object _dataContract)
    {
        container packedContract = [classIdGet(_dataContract), SysOperationDataContractInfo::newParameterInfo(#packUnpackDcParentMethodParameterName, _dataContract).packDataContractObject()];
 
        return packedContract;

    }

    /// <summary>
    /// Unpacks a data contract
    /// </summary>
    /// <param name = "_packedContract"> The packed data contract</param>
    /// <param name = "_dataContract"> Object representing a data contract instance. </param>
    /// <returns> An instance of the packed contract</returns>

    public static Object unpackDataContract(container _packedContract, Object _dataContract = null)
    {
        ClassId     classId;
        container   packedContract; 
        [classId, packedContract] = _packedContract;
        Object contract = _dataContract;

        if (_dataContract == null)
        {
            SysDictClass dictClass = new SysDictClass(classId);
            contract = dictClass.makeObject();
        }
 
        SysOperationDataContractInfo::newParameterInfo(#packUnpackDcParentMethodParameterName, contract).unpackDataContractObject(packedContract);
 
        return contract;
    }

}
```
# service
```
class ANSARI_MultiThreadBatchService extends SysOperationServiceBase
{
    private BatchHeader             batchHeader;
    private FormletterBatchTaskSize batchTaskSize;
    private Counter                 taskNumber;
    PurchTable                      purchTable;

    /// <summary>
    /// Entry point for the batch service.
    /// </summary>
    public void runTasks(ANSARI_MultiThreadBatchDataContract _contract)
    {
        info("Step 1: Batch service started...");

        try
        {
            // Get or create batch header
            batchHeader = BatchHeader::getCurrentBatchHeader();
            if (!batchHeader)
            {
                batchHeader = BatchHeader::construct();
                info("No BatchHeader found. Using temporary header (manual execution).");
            }

            // Get task size or set default
            batchTaskSize = _contract.parmBatchTaskSize();
            if (!batchTaskSize)
                batchTaskSize = 5; // Default chunk size

            info(strFmt("Batch task size = %1", batchTaskSize));
            
            // Generate tasks
            this.generateTasks(this.getQueryRun(), this.getPackedProcessorDataContract());

            info("Step 2: Task generation complete.");
        }
        catch (Exception::Error)
        {
            error(strFmt("Error in runTasks(): %1", xSession::xppCallStack()));
        }
    }

    /// <summary>
    /// Build a QueryRun with limited PurchTable records.
    /// </summary>
    public QueryRun getQueryRun()
    {
        Query query = new Query();
        QueryBuildDataSource qbds;
        qbds = query.addDataSource(tableNum(PurchTable));
        qbds.addSortField(fieldNum(PurchTable, PurchId), SortOrder::Ascending);

        QueryRun queryRun = new QueryRun(query);
        return queryRun;
    }

    /// <summary>
    /// Generate batch tasks from query results.
    /// </summary>
    private void generateTasks(QueryRun _queryRun, container _packedDataContract)
    {
        Counter recordCounter = 0;
        Counter totalCount = 0;
        List recIdList = new List(Types::Container);

        info("Step 3: Generating tasks...");

        while (_queryRun.next())
        {
            PurchTable currentTable = _queryRun.get(tableNum(PurchTable));
            recIdList.addEnd([currentTable.RecId, currentTable.dataAreaId]);
            recordCounter++;
            totalCount++;

            info(strFmt("Fetched record #%1: %2", totalCount, currentTable.PurchId));

            // Create a new batch task when chunk size is reached
            if (recordCounter >= batchTaskSize)
            {
                this.createTask(recIdList, _packedDataContract);
                recordCounter = 0;
                recIdList = new List(Types::Container);
            }

            if (totalCount >= 50) // Limit total records for demo/testing
                break;
        }

        // Create task for remaining records
        if (recIdList.elements() > 0)
        {
            this.createTask(recIdList, _packedDataContract);
        }

        if (batchHeader)
        {
            batchHeader.save();
            info("BatchHeader saved successfully.");
        }
        else
        {
            warning("BatchHeader was null — skipping save().");
        }

        info(strFmt("Step 4: Task generation completed. Total records processed = %1", totalCount));
    }

    /// <summary>
    /// Create an individual batch task.
    /// </summary>
    private void createTask(List _recIdsList, container _packedDataContract)
    {
        taskNumber++;

        SysOperationServiceController controller = new SysOperationServiceController(
            classStr(ANSARI_MultiThreadBatchService),
            methodStr(ANSARI_MultiThreadBatchService, processOperation),
            SysOperationExecutionMode::Asynchronous);

        controller.parmLoadFromSysLastValue(false);
        controller.parmDialogCaption(strFmt("Batch Task %1", taskNumber));

        ANSARI_MultiThreadBatchDataContract contract = controller.getDataContractObject();
        contract.unpack(_packedDataContract);
        contract.parmRecIdList(_recIdsList.pack());

        if (batchHeader)
        {
            batchHeader.addRuntimeTask(controller, BatchHeader::getCurrentBatchTask().RecId);
            info(strFmt("Task #%1 created with %2 records.", taskNumber, _recIdsList.elements()));
        }
        else
        {
            warning(strFmt("BatchHeader is null. Task #%1 created without header linkage.", taskNumber));
        }
    }

    /// <summary>
    /// Process operation for each batch task.
    /// </summary>
    public void processOperation(ANSARI_MultiThreadBatchDataContract _dataContract)
    {
        info("Started processOperation for task...");

        try
        {
            container recIdContainer = _dataContract.parmRecIdList();
            RecId recId;
            PurchTable purchTableData;
            int i;

            // Skip first 3 metadata values added by List.pack()
            int startIndex = 4;

            // Loop through only actual RecIds
            for (i = startIndex; i <= conLen(recIdContainer); i++)
            {
                recId = conPeek(recIdContainer, i);
                purchTableData = PurchTable::findRecId(recId);

                if (purchTableData.RecId)
                info(strFmt("Processing Purchase Order: %1", purchTableData.PurchId));
                else
                warning(strFmt("No PurchTable record found for RecId %1", recId));
            }

            info("Finished processOperation successfully.");
        }
        catch (Exception::Error)
        {
            error(strFmt("Error in processOperation(): %1", AifUtil::getClrErrorMessage()));
        }
    }

    /// <summary>
    /// Get packed contract data for runtime tasks.
    /// </summary>
    public container getPackedProcessorDataContract()
    {
        ANSARI_MultiThreadBatchDataContract contract = new ANSARI_MultiThreadBatchDataContract();
        List recIdList = new List(Types::Container);
        PurchTable tmp;
        int counter = 0;

        while select tmp
            where tmp.PurchId like "500000000%"
        {
            recIdList.addEnd([tmp.RecId, tmp.dataAreaId]);
            counter++;
            if (counter >= 20)
                break;
        }

        contract.parmRecIdList(recIdList.pack());
        return contract.pack();
    }

}
```


# Execution 
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/b40d07fd-dfc1-44ce-b3d7-3d0ac98e7fc0" />


# Result
## Execution statring queued to batch job form
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/6a12722e-223e-4903-867b-7210c1a66e04" />


## execution
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/8a3d0965-c3d9-41ac-98e7-11a9900591f8" />

## all tasks
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/ed37d74b-df44-4cd1-889e-c9c7d83920bc" />

## Ended
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/f443ed1e-cffa-493f-8eba-c717be0885fa" />


## end result
<img width="1272" height="634" alt="image" src="https://github.com/user-attachments/assets/7f49badd-ac90-45c3-8edb-f75457047d37" />

## Output copy paste

Message details
Infolog for job Caption Ansari execution01 (5653508962)
Infolog for task Caption Ansari execution01 (5903789934)
Step 1: Batch service started...
Batch task size = 4
Step 3: Generating tasks...
Fetched record #1: 5000101721
Fetched record #2: 5000165572
Fetched record #3: 5000169029
Fetched record #4: 5000173707
Task #1 created with 4 records.
Fetched record #5: 5000186036
Fetched record #6: 5000190085
Fetched record #7: 5000198078
Fetched record #8: 5000198303
Task #2 created with 4 records.
Fetched record #9: 5000198304
Fetched record #10: 5000198305
Fetched record #11: 5000198306
Fetched record #12: 5000198308
Task #3 created with 4 records.
Fetched record #13: 5000198309
Fetched record #14: 5000198419
Fetched record #15: 5000198422
Fetched record #16: 5000198629
Task #4 created with 4 records.
Fetched record #17: 5000198632
Fetched record #18: 5000198634
Fetched record #19: 5000198636
Fetched record #20: 5000198639
Task #5 created with 4 records.
Fetched record #21: 5000198641
Fetched record #22: 5000198642
Fetched record #23: 5000198658
Fetched record #24: 5000198661
Task #6 created with 4 records.
Fetched record #25: 5000198665
Fetched record #26: 5000198668
Fetched record #27: 5000198670
Fetched record #28: 5000200553
Task #7 created with 4 records.
Fetched record #29: 5000200559
Fetched record #30: 5000204191
Fetched record #31: 5000204212
Fetched record #32: 5000205230
Task #8 created with 4 records.
Fetched record #33: 5000205232
Fetched record #34: 5000205233
Fetched record #35: 5000205960
Fetched record #36: 5000205963
Task #9 created with 4 records.
Fetched record #37: 5000205983
Fetched record #38: 5000205986
Fetched record #39: 5000206185
Fetched record #40: 5000206329
Task #10 created with 4 records.
Fetched record #41: 5000206657
Fetched record #42: 5000209144
Fetched record #43: 5000209329
Fetched record #44: 5000209594
Task #11 created with 4 records.
Fetched record #45: 5000212632
Fetched record #46: 5000214177
Fetched record #47: 5000214690
Fetched record #48: 5000214691
Task #12 created with 4 records.
Fetched record #49: 5000215820
Fetched record #50: 5000216364
Task #13 created with 2 records.
BatchHeader saved successfully.
Step 4: Task generation completed. Total records processed = 50
Step 2: Task generation complete.
Infolog for task Batch Task 1 (5903789935)
Started processOperation for task...
Processing Purchase Order: 5000101721
Processing Purchase Order: 5000165572
Processing Purchase Order: 5000169029
Processing Purchase Order: 5000173707
Finished processOperation successfully.
Infolog for task Batch Task 2 (5903789936)
Started processOperation for task...
Processing Purchase Order: 5000186036
Processing Purchase Order: 5000190085
Processing Purchase Order: 5000198078
Processing Purchase Order: 5000198303
Finished processOperation successfully.
Infolog for task Batch Task 3 (5903789937)
Started processOperation for task...
Processing Purchase Order: 5000198304
Processing Purchase Order: 5000198305
Processing Purchase Order: 5000198306
Processing Purchase Order: 5000198308
Finished processOperation successfully.
Infolog for task Batch Task 4 (5903789938)
Started processOperation for task...
Processing Purchase Order: 5000198309
Processing Purchase Order: 5000198419
Processing Purchase Order: 5000198422
Processing Purchase Order: 5000198629
Finished processOperation successfully.
Infolog for task Batch Task 5 (5903789939)
Started processOperation for task...
Processing Purchase Order: 5000198632
Processing Purchase Order: 5000198634
Processing Purchase Order: 5000198636
Processing Purchase Order: 5000198639
Finished processOperation successfully.
Infolog for task Batch Task 6 (5903789940)
Started processOperation for task...
Processing Purchase Order: 5000198641
Processing Purchase Order: 5000198642
Processing Purchase Order: 5000198658
Processing Purchase Order: 5000198661
Finished processOperation successfully.
Infolog for task Batch Task 7 (5903789941)
Started processOperation for task...
Processing Purchase Order: 5000198665
Processing Purchase Order: 5000198668
Processing Purchase Order: 5000198670
Processing Purchase Order: 5000200553
Finished processOperation successfully.
Infolog for task Batch Task 8 (5903789942)
Started processOperation for task...
Processing Purchase Order: 5000200559
Processing Purchase Order: 5000204191
Processing Purchase Order: 5000204212
Processing Purchase Order: 5000205230
Finished processOperation successfully.
Infolog for task Batch Task 9 (5903789943)
Started processOperation for task...
Processing Purchase Order: 5000205232
Processing Purchase Order: 5000205233
Processing Purchase Order: 5000205960
Processing Purchase Order: 5000205963
Finished processOperation successfully.
Infolog for task Batch Task 10 (5903789944)
Started processOperation for task...
Processing Purchase Order: 5000205983
Processing Purchase Order: 5000205986
Processing Purchase Order: 5000206185
Processing Purchase Order: 5000206329
Finished processOperation successfully.
Infolog for task Batch Task 11 (5903789945)
Started processOperation for task...
Processing Purchase Order: 5000206657
Processing Purchase Order: 5000209144
Processing Purchase Order: 5000209329
Processing Purchase Order: 5000209594
Finished processOperation successfully.
Infolog for task Batch Task 12 (5903789946)
Started processOperation for task...
Processing Purchase Order: 5000212632
Processing Purchase Order: 5000214177
Processing Purchase Order: 5000214690
Processing Purchase Order: 5000214691
Finished processOperation successfully.
Infolog for task Batch Task 13 (5903789947)
Started processOperation for task...
Processing Purchase Order: 5000215820
Processing Purchase Order: 5000216364
Finished processOperation successfully.
End of job log


## html based o/p
<p>Message details<br />Infolog for job Caption Ansari execution01 (5653508962)<br />Infolog for task Caption Ansari execution01 (5903789934)<br />Step 1: Batch service started...<br />Batch task size = 4<br />Step 3: Generating tasks...<br />Fetched record #1: 5000101721<br />Fetched record #2: 5000165572<br />Fetched record #3: 5000169029<br />Fetched record #4: 5000173707<br />Task #1 created with 4 records.<br />Fetched record #5: 5000186036<br />Fetched record #6: 5000190085<br />Fetched record #7: 5000198078<br />Fetched record #8: 5000198303<br />Task #2 created with 4 records.<br />Fetched record #9: 5000198304<br />Fetched record #10: 5000198305<br />Fetched record #11: 5000198306<br />Fetched record #12: 5000198308<br />Task #3 created with 4 records.<br />Fetched record #13: 5000198309<br />Fetched record #14: 5000198419<br />Fetched record #15: 5000198422<br />Fetched record #16: 5000198629<br />Task #4 created with 4 records.<br />Fetched record #17: 5000198632<br />Fetched record #18: 5000198634<br />Fetched record #19: 5000198636<br />Fetched record #20: 5000198639<br />Task #5 created with 4 records.<br />Fetched record #21: 5000198641<br />Fetched record #22: 5000198642<br />Fetched record #23: 5000198658<br />Fetched record #24: 5000198661<br />Task #6 created with 4 records.<br />Fetched record #25: 5000198665<br />Fetched record #26: 5000198668<br />Fetched record #27: 5000198670<br />Fetched record #28: 5000200553<br />Task #7 created with 4 records.<br />Fetched record #29: 5000200559<br />Fetched record #30: 5000204191<br />Fetched record #31: 5000204212<br />Fetched record #32: 5000205230<br />Task #8 created with 4 records.<br />Fetched record #33: 5000205232<br />Fetched record #34: 5000205233<br />Fetched record #35: 5000205960<br />Fetched record #36: 5000205963<br />Task #9 created with 4 records.<br />Fetched record #37: 5000205983<br />Fetched record #38: 5000205986<br />Fetched record #39: 5000206185<br />Fetched record #40: 5000206329<br />Task #10 created with 4 records.<br />Fetched record #41: 5000206657<br />Fetched record #42: 5000209144<br />Fetched record #43: 5000209329<br />Fetched record #44: 5000209594<br />Task #11 created with 4 records.<br />Fetched record #45: 5000212632<br />Fetched record #46: 5000214177<br />Fetched record #47: 5000214690<br />Fetched record #48: 5000214691<br />Task #12 created with 4 records.<br />Fetched record #49: 5000215820<br />Fetched record #50: 5000216364<br />Task #13 created with 2 records.<br />BatchHeader saved successfully.<br />Step 4: Task generation completed. Total records processed = 50<br />Step 2: Task generation complete.<br />Infolog for task Batch Task 1 (5903789935)<br />Started processOperation for task...<br />Processing Purchase Order: 5000101721<br />Processing Purchase Order: 5000165572<br />Processing Purchase Order: 5000169029<br />Processing Purchase Order: 5000173707<br />Finished processOperation successfully.<br />Infolog for task Batch Task 2 (5903789936)<br />Started processOperation for task...<br />Processing Purchase Order: 5000186036<br />Processing Purchase Order: 5000190085<br />Processing Purchase Order: 5000198078<br />Processing Purchase Order: 5000198303<br />Finished processOperation successfully.<br />Infolog for task Batch Task 3 (5903789937)<br />Started processOperation for task...<br />Processing Purchase Order: 5000198304<br />Processing Purchase Order: 5000198305<br />Processing Purchase Order: 5000198306<br />Processing Purchase Order: 5000198308<br />Finished processOperation successfully.<br />Infolog for task Batch Task 4 (5903789938)<br />Started processOperation for task...<br />Processing Purchase Order: 5000198309<br />Processing Purchase Order: 5000198419<br />Processing Purchase Order: 5000198422<br />Processing Purchase Order: 5000198629<br />Finished processOperation successfully.<br />Infolog for task Batch Task 5 (5903789939)<br />Started processOperation for task...<br />Processing Purchase Order: 5000198632<br />Processing Purchase Order: 5000198634<br />Processing Purchase Order: 5000198636<br />Processing Purchase Order: 5000198639<br />Finished processOperation successfully.<br />Infolog for task Batch Task 6 (5903789940)<br />Started processOperation for task...<br />Processing Purchase Order: 5000198641<br />Processing Purchase Order: 5000198642<br />Processing Purchase Order: 5000198658<br />Processing Purchase Order: 5000198661<br />Finished processOperation successfully.<br />Infolog for task Batch Task 7 (5903789941)<br />Started processOperation for task...<br />Processing Purchase Order: 5000198665<br />Processing Purchase Order: 5000198668<br />Processing Purchase Order: 5000198670<br />Processing Purchase Order: 5000200553<br />Finished processOperation successfully.<br />Infolog for task Batch Task 8 (5903789942)<br />Started processOperation for task...<br />Processing Purchase Order: 5000200559<br />Processing Purchase Order: 5000204191<br />Processing Purchase Order: 5000204212<br />Processing Purchase Order: 5000205230<br />Finished processOperation successfully.<br />Infolog for task Batch Task 9 (5903789943)<br />Started processOperation for task...<br />Processing Purchase Order: 5000205232<br />Processing Purchase Order: 5000205233<br />Processing Purchase Order: 5000205960<br />Processing Purchase Order: 5000205963<br />Finished processOperation successfully.<br />Infolog for task Batch Task 10 (5903789944)<br />Started processOperation for task...<br />Processing Purchase Order: 5000205983<br />Processing Purchase Order: 5000205986<br />Processing Purchase Order: 5000206185<br />Processing Purchase Order: 5000206329<br />Finished processOperation successfully.<br />Infolog for task Batch Task 11 (5903789945)<br />Started processOperation for task...<br />Processing Purchase Order: 5000206657<br />Processing Purchase Order: 5000209144<br />Processing Purchase Order: 5000209329<br />Processing Purchase Order: 5000209594<br />Finished processOperation successfully.<br />Infolog for task Batch Task 12 (5903789946)<br />Started processOperation for task...<br />Processing Purchase Order: 5000212632<br />Processing Purchase Order: 5000214177<br />Processing Purchase Order: 5000214690<br />Processing Purchase Order: 5000214691<br />Finished processOperation successfully.<br />Infolog for task Batch Task 13 (5903789947)<br />Started processOperation for task...<br />Processing Purchase Order: 5000215820<br />Processing Purchase Order: 5000216364<br />Finished processOperation successfully.<br />End of job log</p>


