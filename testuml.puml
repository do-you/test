@startuml
participant yaaw
participant json_rpc
participant client
participant global_state_cache
participant Internet_Downloader
participant task_schedule
participant down_task
participant net_io

Note over json_rpc: start_listening
yaaw->json_rpc: 连接
Note over json_rpc: complete_callback
Note over json_rpc: new client(this,fd)
json_rpc->client: client::start
Note over client: async_read
client-->json_rpc:
json_rpc-->yaaw: 
yaaw->client: "aria2.addUri"的json_rpc请求
Note over client: 解析json
client->json_rpc: json_rpc::process(method, parms)
Note over json_rpc: parms转换成list<down_parm> parm_list
json_rpc->global_state_cache: add_task(uri,parm_list)
global_state_cache->Internet_Downloader: add_task(uri,parm_list)
Note over Internet_Downloader: parseUrl(uri,info)
Note over Internet_Downloader: 填充task_info
Internet_Downloader->task_schedule: add(task_info)
Note over task_schedule: stopped_set.insert(task_info)
task_schedule-->Internet_Downloader:
Note over Internet_Downloader: task_set.insert(task_info)\ntask_info状态:pause
Internet_Downloader->Internet_Downloader: start_task(task_info)
Note over Internet_Downloader: task_info状态:pause->waitting
Internet_Downloader->task_schedule: in_schedule(task_info)
Note over task_schedule: stopped_set.erase(task_info),waitting_set.insert(task_info)
task_schedule->task_schedule: schedule
Note over task_schedule: 如果 active_set.size() < max_active_count && waitting_set.size() > 0
Note over task_schedule: 取出waitting_set的第一个 as x
task_schedule->Internet_Downloader: notify(schedule_evcode::change_to_active, x)
Note over Internet_Downloader: x状态转为active
Internet_Downloader->down_task: new down_task(x)
down_task-->Internet_Downloader: new down_task 返回
Internet_Downloader->down_task: init()
down_task->>net_io: async_get_file_info
Note over net_io: 见图2
net_io-->down_task:
down_task-->Internet_Downloader: init() 返回
Internet_Downloader-->task_schedule: notify() 返回
task_schedule-->task_schedule: schedule() 返回
task_schedule-->Internet_Downloader: in_schedule() 返回
Internet_Downloader-->Internet_Downloader: start_task() 返回
Internet_Downloader-->global_state_cache: task_info 地址值
global_state_cache-->json_rpc: task_info 地址值
json_rpc-->client: 地址值的十六进制表示
Note over client: 把结果包装成json
client-->yaaw: json_rpc请求响应
@enduml
