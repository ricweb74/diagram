```mermaid
graph TD

U[Utente] --> Filt[Dash filtri]
U --> FundForm[Dash funding]
U --> SimForm[Dash sim spending]
U --> ViewDash[Dash grafici]

InjectCosts[Timer 30m] --> Window[Window lookback]
Window --> GetCosts[GET org costs]
GetCosts --> Agg[Fn aggregate]

Agg --> MsgCosts[Msg platform_costs]
Agg --> MsgSpendReal[Msg project_spending real]

MsgCosts --> WriteCosts[Influx write platform_costs]
MsgSpendReal --> WriteSpendReal[Influx write spending real]

GetProjects[GET org projects] --> MapProj[Fn map id name]
MapProj --> Filt
MapProj --> FundForm
MapProj --> SimForm

FundForm --> StoreFunding[Fn store funding]
StoreFunding --> BuildFunding[Fn build funding point]
BuildFunding --> WriteFunding[Influx write funding]

SimForm --> StoreSim[Fn store sim]
StoreSim --> BuildSim[Fn build sim point]
BuildSim --> WriteSpendSim[Influx write spending sim]

Filt --> BuildBil[Fn build bilancio flux]
BuildBil --> FluxBil[Influx bilancio]
FluxBil --> CalcBil[Fn calcola bilancio]

CalcBil --> ViewDash

CalcBil --> Enforce[Fn soglie budget]

Enforce --> AlertMid[Msg soglie 50 75 90]
AlertMid --> TelPrep[Fn telegram alert]
TelPrep --> TelSender[Telegram sender]

Enforce --> Alert100[Msg soglia 100]
Alert100 --> StartBlock[Fn blocco chiavi]
StartBlock --> GetKeys[GET project keys]
GetKeys --> PrepDelKeys[Fn prep delete keys]
PrepDelKeys --> DelKey[DELETE api key]
DelKey --> TelKey[Fn telegram chiave]
TelKey --> TelSender

WriteCosts --> Bucket[Influx bucket]
WriteSpendReal --> Bucket
WriteSpendSim --> Bucket
WriteFunding --> Bucket

Bucket --> FluxBil
Bucket --> ViewDash

GetProjects --> OpenAI[OpenAI admin API]
GetCosts --> OpenAI
GetKeys --> OpenAI
DelKey --> OpenAI

TelSender --> Bot[Telegram chat]
