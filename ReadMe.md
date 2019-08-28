## Flyway
### 簡介:
Flyway是一套opensource的Database版本控制工具，直接透過SQL File對於Database進行管控並記錄完整的更新歷程，以達到所謂的Database版本控制。

##### 1. 說明:
採用Gradle + Groovy來對Flyway進行操作，在build.gradle檔中apply plugin Flyway，設定好Groovy相關設定後即可使用Flyway。  

詳細可參考官方網站：[https://flywaydb.org/documentation/](https://flywaydb.org/documentation/)

##### 2. 目錄結構:
~~~
├── lib
├── MIGRATION
│   └─ AP
├── RELEASE
│   └── AP
│       ├── PATCH
│       └── PROGS
├── build.gradle
├── database.dev.properties
├── gengroovy.gradle
└── gradle.properties
~~~
|MIGRATION資料夾|說明|
|---|---|
|AP|存放RELEASE打包要上版的script|

#### Script固定命名方式為
 [V][需求單流水號][.][此需求單下異動流水號][雙底線][異動說明OR需求單號].sql 
##### 如 : V1.0__BASELINE.sql
#####  V2.0__100000001.sql

#### RELEASE 資料夾底下目錄結構說明:
|資料夾|說明|
|---|---|
|PATCH|依`更新日期建立資料夾存放`非PROGS執行SQL檔案(`編碼UTF-8`)，<br>若是`新增TABLE`則SQL檔案命名方式為 `[TABLE名稱].Table.sql`，<br>若是`異動SHCEMA`則SQL檔案命名方式為 `[TABLE名稱].SchemaPatch.sql`，<br>若是`資料異動`則SQL檔案命名方式為 `[TABLE名稱].DataPatch.sql`|
|PROGS|放所有`程式類的SQL(編碼UTF-8)`，如SP、Function、View|

~~~
├── RELEASE
│   └── AP
│       ├── PATCH
│       │	├──20180507
│       │	│   ├──ADM_USERS.SchemaPatch.sql
│       │	│   └──ADM_USERS.DataPatch.sql
│       │	└──20180508
│       │	    ├──WOB_ALERT_LOGS.SchemaPatch.sql
│       │	    └──WOB_ALERT_LOGS.DataPatch.sql
│       └── PROGS
│           ├──JADM_GET_DATADATE.StoredProcedure.sql
│           └──ADM_BRANCHES_EXPAND.View.sql
~~~

#### Gradle 配置文件 build.gradle

|Task|說明|
|---|---|
|migrateDB|Migrate Task名稱|

#### Database設定檔 database.`{profile}`.properties
檔案內紀錄DB設定
注意的是migrate目錄路徑設定位置

ap.location=filesystem:MIGRATION/AP

#### Gradle 設定檔 gradle.properties
profile=dev

|參數|說明|
|---|---|
|profile|設定環境，這個部分會直接對應到database.`{profile}`.properties，比如說設定為dev，那Flyway就會取database.dev.properties|

##### 3. 執行:
在flyway目錄下，執行指令 gradlew migrateDB`(Migrate Task名稱)`
會依據指定的database設定檔內的task對應目錄進行migrate
可參考[https://flywaydb.org/getstarted/firststeps/gradle]

##### 4. RELEASE上版規則:

### Commit  
#### Commit message 格式  

Commit message 由三個部分組成：Subject，Body 和 Footer。  
> Subject：必填項目  
> Body：選填項目  
> Footer：選填項目  

每次完成的 coding 後，在 code 進 Git 時的 commit subject 必須明確的讓人知道本次 commit 的用途及目的性。  

#### Subject格式  

subject 分為二個區塊：type 及本次 commit 的用途及目的性，中間以 : 分隔。  
> e.g. fix:調整CIF_CODE欄位長度  

type 用於說明 commit 的類別，只允許使用下面 7 個類別：  
> `feat`：新功能  
> `fix`：修補 bug  
> `docs`：文檔（documentation）  
> `style`：格式（不影響代碼運行的變動）  
> `refactor`：重構（即不是新增功能，也不是修改 bug 的代碼變動）  
> `test`：增加測試項目  
> `chore`：構建過程或輔助工具的變動  


##### 5. 版控記錄Tables:
TABLE名稱 : flyway_schema_history
第一次migrate `V1.0__BASELINE.sql`
執行後TABLE欄位說明如下所示:

|version|description|type|script|installedby|installedon|executiontime|success|
|---|---|---|---|---|---|---|---|
|1.0|BASELINE|SQL|V1.0__BASELINE.sql|USER|2018-05-04 11:56:00.000|86403|1|


