Parallel scan 
==================================

In scenarios where you are not concerned with the order of the query results, you can use parallel scan to obtain query results in parallel.

Prerequisites 
----------------------------------

* Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).

  

* A table is created. Data is written to the table.

  

* A search index is created on the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Create search indexes.md).

  




Parameters 
-------------------------------



|          Parameter           ||                                                                                                                                                                                                                                                                                                     Description                                                                                                                                                                                                                                                                                                     |
|-----------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TableName                    || The name of the table.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| IndexName                    || The name of the search index.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ScanQuery | Query             | The query statement for the search index. The operation supports term query, fuzzy query, range query, geo query, and nested query, which is similar to those of the Search operation.                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ScanQuery | Limit             | The maximum number of rows that can be returned at a time.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ScanQuery | MaxParallel       | The maximum number of concurrent requests. The maximum number of concurrent requests varies based on the data volume. The larger volume of data requires more concurrent requests. You can use the ComputeSplits operation to query the maximum number of concurrent requests.                                                                                                                                                                                                                                                                                                                                      |
| ScanQuery | CurrentParallelID | The ID of the concurrent request. Valid values: \[0, MaxParallel).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ScanQuery | Token             | The token used for pagination. The result of the parallel scan request contains the token for the next page. You can use the token to query data for the following page.                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ScanQuery | AliveTime         | The validity period of the current parallel scan task. The value is also the validity period of the token. We recommend that you use the default value. Default value: 60. Unit: seconds. If the next request is not initiated within the validity period, no more data can be queried. The validity period of the token is refreshed each time you send a request. **Note** The server uses the asynchronous method to process expired tasks. The current task does not expire within the validity period. However, Tablestore does not guarantee that the task expires after the validity period. |
| ColumnsToGet                 || Parallel scan can be used only to query indexed columns in search indexes. You must set store to true when you create a search index.                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| SessionId                    || The session ID of the parallel scan task. You can call the ComputeSplits operation to create a session and obtain the maximum number of concurrent requests supported in the current task.                                                                                                                                                                                                                                                                                                                                                                                                                          |



Examples 
-----------------------------

The following code provides examples on how to scan data by sending a single request or by using multiple threads at a time:

* Scan data by sending a single request

      /**
       * Send a single parallel scan request to scan
      data.
       */
      func ParallelScanSingleConcurrency(client *tablestore.TableStoreClient, tableName string, indexName string) {
      	computeSplitsResp, err := computeSplits(client, tableName, indexName)
      	if err ! = nil {
      		fmt.Printf("%#v", err)
      		return
      	}
      
      	query := search.NewScanQuery().SetQuery(&search.MatchAllQuery{}).SetLimit(2)
      
      	req := &tablestore.ParallelScanRequest{}
      	req.SetTableName(tableName).
      		SetIndexName(indexName).
      		SetColumnsToGet(&tablestore.ColumnsToGet{ReturnAllFromIndex: false}).
      		SetScanQuery(query).
      		SetSessionId(computeSplitsResp.SessionId)
      
      	res, err := client.ParallelScan(req)
      	if err ! = nil {
      		fmt.Printf("%#v", err)
      		return
      	}
      
      	total := len(res.Rows)
      	for res.NextToken ! = nil {
      		req.SetScanQuery(query.SetToken(res.NextToken))
      		res, err = client.ParallelScan(req)
      		if err ! = nil {
      			fmt.Printf("%#v", err)
      			return
      		}
      
      		total += len(res.Rows) //process rows each loop
      	}
      	fmt.Println("total: ", total)
      }

  

* Use concurrent threads to scan data

      /**
       * Send multiple parallel scan requests to scan
      data.
       */
      func ParallelScanMultiConcurrency(client *tablestore.TableStoreClient, tableName string, indexName string) {
      	computeSplitsResp, err := computeSplits(client, tableName, indexName)
      	if err ! = nil {
      		fmt.Printf("%#v", err)
      		return
      	}
      
      	var wg sync.WaitGroup
      	wg.Add(int(computeSplitsResp.SplitsSize))
      
      	for i := int32(0); i < computeSplitsResp.SplitsSize; i++ {
      		current := i
      		go func() {
      			defer wg.Done()
      			query := search.NewScanQuery().
      				SetQuery(&search.MatchAllQuery{}).
      				SetCurrentParallelID(current).
      				SetMaxParallel(computeSplitsResp.SplitsSize).
      				SetLimit(2)
      
      			req := &tablestore.ParallelScanRequest{}
      			req.SetTableName(tableName).
      				SetIndexName(indexName).
      				SetColumnsToGet(&tablestore.ColumnsToGet{ReturnAllFromIndex: false}).
      				SetScanQuery(query).
      				SetSessionId(computeSplitsResp.SessionId)
      
      			res, err := client.ParallelScan(req)
      			if err ! = nil {
      				fmt.Printf("%#v", err)
      				return
      			}
      
      			total := len(res.Rows)
      			for res.NextToken ! = nil {
      				req.SetScanQuery(query.SetToken(res.NextToken))
      				res, err = client.ParallelScan(req)
      				if err ! = nil {
      					fmt.Printf("%#v", err)
      					return
      				}
      
      				total += len(res.Rows) //process rows each loop
      			}
      			fmt.Println("total: ", total)
      		}()
      	}
      	wg.Wait()
      }

  



